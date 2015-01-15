---
layout: post
title: Don't Trigger Analytics When Previewing Your Jekyll Site
categories: technology
tags: jekyll
---

When I'm writing, I preview articles locally in [Jekyll](http://jekyllrb.com) and reload them **a lot**. I don't want to trigger my analytics code everytime and record hits that are just me checking my article in progress.
So I came up with a quick solution that will even work if you run Jekyll in safe mode, like [GitHub Pages does](https://help.github.com/articles/using-jekyll-with-pages/#configuration-overrides).

In my layout, I check if Jekyll generates the site for local preview. If it's the case, `site.draft` will be set and I can use that to not render the analytics code in preview.
The relevant lines are 18 to 20:

{% highlight html linenos %}
{% raw %}
<!DOCTYPE html>
<html>

  {% include head.html %}

  <body>

    {% include header.html %}

    <div class="page-content">
      <div class="wrapper">
        {{ content }}
      </div>
    </div>

    {% include footer.html %}

    {% unless site.draft %}
      {% include google_analytics.html %}
    {% endunless %}

  </body>

</html>
{% endraw %}
{% endhighlight %}

Now you might ask how to set `site.draft` to the proper value? Good question.

Thankfully, Jekyll accepts multiple config files on the command line using the `--config` switch.
I take advantage of that by creating another config file used only for my preview mode. Let's call it `_config_draft.yml` and create it in the root of the Jekyll site along the main config. It will only contain the following:

{% highlight yaml %}
draft: true
{% endhighlight %}

Loading this config file in preview mode is only a matter of having the proper Rake tasks set up.
Here is an example that you can integrate to your `Rakefile`:

{% highlight ruby %}
namespace :preview do
  task :draft do
     sh 'bundle exec jekyll serve --config _config.yml,_config_draft.yml --future --drafts --unpublished'
  end

  desc 'Preview before release (as it will be on production)'
  task :release do
    sh 'bundle exec jekyll serve'
  end
end

desc 'Preview for editing'
task :preview => 'preview:draft'
{% endhighlight %}

Then when I run `rake preview` and have a look at the HTML source of my site, it now doesn't include the analytics code anymore! Win! Cookies!
Now next time I'll need to include some stuff only on the live site, it's just a conditionnal away.

Note that the `preview` task will also render draft, unpublished and future posts. The kind of things you might want to do when previewing your site.

I also included a `preview:release` task to check my site with everything rendered before I push it live.
