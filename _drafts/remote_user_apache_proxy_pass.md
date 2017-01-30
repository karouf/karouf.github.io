---
layout: post
title:  "Make Apache proxy the REMOTE_USER header. And why."
date:   2014-12-10 11:56:36
categories:
  - tech
tags:
  - apache
  - authentication
  - reverse proxy
---

The other day one of my colleague was trying to integrate a new application behind a simple authentication proxy: Apache + mod_authnz_sspi + mod_proxy_http, and it just wasn't working. The proxified app wasn't getting the `REMOTE_USER` header with the ID of the authentified user. the weird thing is that another app proxified with mod_proxy_ajp was getting it! We knew that something was up with our Apache conf. I remembered working on something similar before and went to dig up some old Apache conf to help him out.
So here it is:

```
conf
```

That snippet just needs to be added to the vhost, location or whatever you are using to authenticate your users, and BAM! your app now gets the `REMOTE_USER` header like it should.

## But why should I need to do that?!

It's exactly what I thought when I first encountered this bit of rewrite conf... I mean if you look at it, it looks kind of ridiculous. Should the header not be set by Apache already?!
Well, it happens that Apache will only set what it calls an **environment variable** when it is done authenticating the user. And those kind of variables ([here is the list](https://httpd.apache.org/docs/current/en/expr.html#vars)) are only available during the request processing. After that, it's gone. Unless you instruct Apache to set a header with the value of the said variable. That's what the above conf is doing.
The fact that mod_proxy_ajp does forwards `REMOTE_USER` is totally misleading. The [docs](https://httpd.apache.org/docs/2.4/en/mod/mod_proxy_ajp.html#env) clearly state that it will forward every environment variable prefixed with `AJP_` as an AJP request attribute. I do not know where it finds a `AJP_REMOTE_USER` environment variable though, but it would explain why our backend Tomcat server was initially getting `REMOTE_USER` through AJP but not HTTP.
