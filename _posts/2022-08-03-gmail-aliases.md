---
layout: post
title: Gmail aliases
date: '2022-08-03'
published: true
handle: gmail-aliases
---

# Gmail aliases

I always end up with some vanity domain and would like to send emails from them. There are different ways to achieve this goal. The following is free and simple.

<img src="/assets/posts/gmail-aliases/images/image7.png" width="372" height="124">

## Prerequisite

These notes presume you can receive emails to said address. Either forwarded or you have the ability to read them from elsewhere it does not matter.

There is a verification process later on where a code to be entered during the setup process will be sent to said email.

I personally use [Google Domains](https://domains.google.com/) and simply use the [Email forwarding](https://support.google.com/domains/answer/3251241?hl=en) feature. Most domain registrars will offer email forwarding.

## Setup

### Preparation

Before we can configure the alias in Gmail, we will need to go create what is called an "App passwords".

Technically, I think this step is only required if you use 2-Step-Verification on your account. I will presume you are and if not I will encourage you to look into it. Additional security of what is one of the most important asset you possess, your online identity, is very important.

App passwords are 16 characters long codes that can act as secondary passwords of your Google account. Their special characteristic is they bypass 2FA.

With great power comes great responsability, you will want to use it during the alias configuration steps then make sure you delete any noted taken during the process for security reasons. You will not want for anyone to stumble on this priviledged key.

App passwords are created from your [Google account](https://myaccount.google.com/), under [Security](https://myaccount.google.com/security) in the "Signing in to Google" section.

<img src="/assets/posts/gmail-aliases/images/image9.png" width="624" height="182">

Follow the steps to generate an app password.

The reference name of the password can be anything you’d like. I personally use the alias email (eg.: "Gmail: alternate.name@customdomain.com"). It can be anything you’d like.

<img src="/assets/posts/gmail-aliases/images/image6.png" width="539" height="430">

You now have an active application password. Keep it around for the next step.

<img src="/assets/posts/gmail-aliases/images/image5.png" width="351" height="250">

### Creating the alias

### Step 1: Gmail settings

From your [Gmail](https://mail.google.com/), go to your [setting page](https://mail.google.com/mail/u/0/#settings/general).

<img src="/assets/posts/gmail-aliases/images/image1.png" width="624" height="315">

Then navigate toward the "Send mail as" option and select "Add another email address".

<img src="/assets/posts/gmail-aliases/images/image2.png" width="624" height="227">

This will open a popup window where you will enter the different email settings.

### Step 2: Email definition

Enter your desired email display name and said email address.

Name: The display name.

Email address: The address of the alias.

Treat as an alias: This [Google support answer](https://support.google.com/a/answer/1710338) will guide you as to if you need to select it or not.

<img src="/assets/posts/gmail-aliases/images/image3.png" width="357" height="207">

### Step 2: Credentials

Fill in the account credentials.

SMTP Server: Should be `smtp.gmail.com`.

Username: The main Google account identifier (not the alias, without `@gmail.com`).

Password: The "App password" from the preparation step.

<img src="/assets/posts/gmail-aliases/images/image4.png" width="360" height="201">

### Step 3: Email confirmation

The process will ask you for a confirmation code. This code will be sent to the alias email and it is why it is important as it was mentioned in the prerequisite to have the ability to read it.

<img src="/assets/posts/gmail-aliases/images/image8.png" width="407" height="180">

<img src="/assets/posts/gmail-aliases/images/image10.png" width="409" height="81">

Once the code is entered, the alias will has been created successfully!

