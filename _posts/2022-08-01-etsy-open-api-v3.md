---
layout: post
title: Etsy Open API v3
date: '2022-08-01'
published: true
handle: etsy-open-api-v3
---

# Etsy Open API v3

Nothing better to test a blog's capabilities than making some personal notes public. I have been hacking around Etsy APIs to extract some sales data from their backend and send it to some custom spreadsheets.

Sure I could have used the limited [CSV export](https://help.etsy.com/hc/en-us/articles/360000343508-How-to-Download-Your-Listing-Information) capabilities from their admin portal, but where is the fun in that? I needed to see how much I could get done with their APIs.

## Etsy documentation

Some [documentation](https://developer.etsy.com/documentation/) exists and there is an [API reference](https://developer.etsy.com/documentation/reference/) section which gives a good idea of what is available. Worth having a look before considering the advanced engineering path.

One thing that I’ll mention upfront is that you need to create an application in the developer dashboard in order to get a valid API key. It can take a few days for an application to be approved by their team and the API key will not work until then. It is worth it to [create an app](https://www.etsy.com/developers/documentation/getting_started/register) ASAP so the review process does not become a blocker.

## Ruby implementation

I’m a Ruby dev, so that’s what you are going to get today. Since [OpenAPI](https://www.openapis.org/) is all about supporting many languages, odds are it should also work for you the same way if you use something else.

### OpenAPI Generator

I don’t have any experience running an OpenAPI server, but I appreciate consuming from one.

Inspecting the network calls performed by their API reference page, there is an XHR to [https://www.etsy.com/openapi/generated/oas/3.0.0.json](https://www.etsy.com/openapi/generated/oas/3.0.0.json).

I installed `openapi-generator`:

{% highlight sh %}
# https://github.com/OpenAPITools/openapi-generator
brew install openapi-generator
{% endhighlight %}

And ran the following to create my `EtsyApi` client library:

{% highlight sh %}
# https://github.com/OpenAPITools/openapi-generator/blob/master/docs/generators/ruby.md
openapi-generator generate \
  -i https://www.etsy.com/openapi/generated/oas/3.0.0.json \
  --skip-validate-spec \
  --additional-properties moduleName=EtsyApi \
  -g ruby \
  -o ./vendor/etsy_api
{% endhighlight %}

It generates a gem, so do not forget to add `gem "etsy_api", path: "vendor/etsy_api"` to your `Gemfile`.

And that’s it.

### Etsy Authentication

#### Obtaining Oauth2 tokens (once)

This is the part where I had a lot of trial and error. Some of it was my limited understanding of Etsy API, and the other half was remembering how to configure Oauth2 in OpenAPI.

[Authentication - Etsy Open API v3](https://developers.etsy.com/documentation/essentials/authentication)

I only needed to run some scripts server-side. The Oauth2 code demonstrated here is limited to self-use, not requesting through web application users.

Preparation:

A callback URL (eg.: "https://localhost:8080/oauth2/callback") needs to be added to your [Etsy application](https://www.etsy.com/developers/your-apps). Without a valid callback URL, your Oauth2 won’t go through.

{% highlight rb %}
# Called "KEYSTRING" in the developer portal
client_id = ENV.fetch('ETSY_CLIENT_ID')

# Called "SHARED SECRET" in the developer portal
client_secret = ENV.fetch('ETSY_CLIENT_SECRET')

# Eg.: "listings_r transactions_r"
client_scopes = ENV.fetch('ETSY_CLIENT_SCOPES')

# Some random secret
client_state = ENV.fetch('ETSY_CLIENT_STATE')
{% endhighlight %}

Note: I use [dotenv](https://github.com/bkeepers/dotenv) for my environment variables. It works well, be sure to add the `.env` to your `.gitignore`.

{% highlight rb %}
require 'dotenv'
Dotenv.load
{% endhighlight %}

[Authentication - Etsy Open API v3: Step 1 request an authorization code](https://developers.etsy.com/documentation/essentials/authentication/#step-1-request-an-authorization-code)

I have been using `gem "oauth2"` so I didn’t have to worry too much about the implementation details.

{% highlight rb %}
require "securerandom"
code_verifier = SecureRandom.hex

require "oauth2"
client = OAuth2::Client.new(client_id, client_secret,
  site: 'https://www.etsy.com',
  authorize_url: "/oauth/connect",
)

client.auth_code.authorize_url(
  redirect_uri: 'https://localhost:8080/oauth2/callback',
  state: client_state,
  scope: client_scopes.split(',').join(" "),
  code_challenge: Digest::SHA256.base64digest(code_verifier).tr("+/", "-_").tr("=", ""),
  code_challenge_method: "S256",
)
# => "https://www.etsy.com/oauth/connect?client_id=p0imeiqc..."
{% endhighlight %}

The next steps are manual, but it is a one-time thing:

1. Take the URL and punch it in a browser. The following page will open:

<img src="/assets/posts/etsy-open-api-v3/images/image1.png" width="415" height="565">

2. Grant permission. The redirect URL should look like: `https://localhost:8080/oauth2/callback?code=kq7ORZrP...&state=...`.

3. Validate the "state" parameter matches the `ETSY_CLIENT_STATE` environment variable.

4. Assign the "code" from the query parameters in a local variable.

{% highlight rb %}
code = "..."
{% endhighlight %}

[Authentication - Etsy Open API v3: Step 3 request an access token](https://developers.etsy.com/documentation/essentials/authentication/#step-3-request-an-access-token)

{% highlight rb %}
client = OAuth2::Client.new(client_id, client_secret,
  site: 'https://api.etsy.com',
  token_url: "/v3/public/oauth/token"
)

access = client.auth_code.get_token(code,
  redirect_uri: 'https://localhost:8080/oauth2/callback',
  client_id: client_id,
  code_verifier: code_verifier,
  headers: { "Authorization": nil }
)

access_hash = access.to_hash

# Optional: Add human debug information
access_hash["expires_at_human"] = Time.at(access_hash["expires_at"]).strftime("%Y-%m-%dT%H:%M:%S%z")

# Can be anywhere
# Do not forget to add this file to your .gitignore.
File.write("etsy.json", JSON.pretty_generate(access_hash))
{% endhighlight %}

#### Configuring Oauth2 tokens

{% highlight rb %}
client_id = ENV.fetch('ETSY_CLIENT_ID')
client_secret = ENV.fetch('ETSY_CLIENT_SECRET')

client = OAuth2::Client.new(client_id, client_secret,
  site: 'https://api.etsy.com',
  token_url: "/v3/public/oauth/token",
  connection_opts: {
    headers: {
      "X-Api-Key" => client_id,
    }
  }
)

# Use the file location from the previous step. 
access_hash = JSON.parse(File.read("etsy.json"))

# Reduce expiration by 10 min for tolerance.
# I do not want to see this token expire mid-script.
access_hash['expires_at'] -= 60 * 10

access = OAuth2::AccessToken.from_hash(client, access_hash)
if access.expired?
  puts "Refreshing API key"

  access = access.refresh(client_id: client_id, headers: { "Authorization" => nil })

  access_hash = access.to_hash
  access_hash["expires_at_human"] = Time.at(access_hash["expires_at"]).strftime("%Y-%m-%dT%H:%M:%S%z")
  File.write("etsy.json", JSON.pretty_generate(access_hash))
end

# This is the gem generated through OpenAPI
require "etsy_api"
EtsyApi.configure do |config|
  config.api_key['api_key'] = client_id

  config.access_token = access.token

  # These debug settings allow for the observation of the HTTP interactions.
  # config.debugging = true
  # logger = Logger.new(STDOUT)
  # logger.level =Logger::Severity::DEBUG
  # config.logger = logger
end
{% endhighlight %}

#### Usage

Once the complex part is complete, the following should work:

{% highlight rb %}
EtsyApi::ShopApi.new.find_shops("shop name")

EtsyApi::ShopListingApi.new.get_listing(1051518224)

# Your Etsy shop ID.
# Can be obtained by inspecting the "contact" link on a shop
shop_id = 12345
EtsyApi::ShopReceiptApi.new.get_shop_receipts(shop_id)
{% endhighlight %}

## Technical support

Through my work, I had some questions. I feel it is worth sending some kudos when due. The [https://github.com/etsy/open-api/](https://github.com/etsy/open-api/) repository was a nice touch where developers can interact with Etsy engineers. Developers can ask questions, provide feedback or file bug reports.

## Conclusion

I hope this test post was of help to you and made the road to API integration as fast and boring as possible.

