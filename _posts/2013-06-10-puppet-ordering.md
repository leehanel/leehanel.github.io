---
layout: post
title: Puppet Ordering
date: '2013-06-10T22:59:00+00:00'
tags: 
tumblr_url: http://www.stringpuncher.com/post/52683357132/puppet-ordering
---
We've been doing ordering by wrapping puppet's package resource with defined types like follows:

{% highlight puppet %}
define puppet::rpm($ensure = "latest") {
  include yumrepo::client
  package{
    $name:
      ensure   => $ensure,
      required => YumRepo["repo_name"];
  }
}
{% endhighlight %}

to ensure that we have the providing yum repo on the server.

{% highlight puppet %}
puppet::rpm {
  "httpd":
    ensure => "latest",
}
{% endhighlight %}

this leaves us with tons of calls to a custom type, which could all could be represented by a single line:

{% highlight puppet %}
Yumrepo <| |> -> Package <| provider == ym |>
{% endhighlight %}

and then we could revert to using the regular package resources so as to not require a defined type.

{% highlight puppet %}
package {
  'httpd':
    ensure => latest
}
{% endhighlight %}
