---
title: Sample post
date: '2022-07-28'
published: true
x-meta: '1234'
handle: sample-post
---

# Heading 1

Some Text

## Image

<img src="/assets/posts/sample-post/images/image1.png" width="90" height="102">

## Table
<table>
  <tr>
    <td>
      <p>
        <span>1</span>
      </p>
    </td>
    <td>
      <p>
        <span>2</span>
      </p>
    </td>
    <td>
      <p>
        <span>3</span>
      </p>
    </td>
  </tr>
  <tr>
    <td>
      <p>
        <span>4</span>
      </p>
    </td>
    <td>
      <p>
        <span>5</span>
      </p>
    </td>
    <td>
      <p>
        <span>6</span>
      </p>
    </td>
  </tr>
  <tr>
    <td>
      <p>
        <span>7</span>
      </p>
    </td>
    <td>
      <p>
        <span>8</span>
      </p>
    </td>
    <td>
      <p>
        <span>9</span>
      </p>
    </td>
  </tr>
</table>
## Code

{% highlight rb %}
def fibonacci(n)
  return n if n <= 1

  fibonacci(n-1) + fibonacci(n-2)
end

fibonacci(5)
# => 5
{% endhighlight %}
