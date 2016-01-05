---
layout: post
title: Jekyll Plugins and Hosting on GitHub Pages
tags: jekyll github pages liquid plugin snippet tip include template
image_small: /images/2016-01-04-small-jekyll-include.png
---

Jekyll allows you to write [plugins](https://help.github.com/articles/using-jekyll-plugins-with-github-pages/ "Jekyll Plugins with GitHub Pages") to create custom generated content for your site. By default, GitHub Pages supports a few useful plugins (in the form of gems), but disables user plugins for security reasons. What if you want to get the most out of Jekyll, while still having GitHub Pages generate your site?

<!--more-->

## Available Plugins

To begin with, GitHub Pages supports a few useful plugins that you might want to take advantage of. For example, the `jekyll-feed` plugin creates [an Atom feed](https://en.wikipedia.org/wiki/Atom_%28standard%29 "Atom XML") for subscribing to new posts. You can enable plugins on GitHub Pages by adding to your site's _config.yml file:

{% highlight yaml %}
gems:
- jekyll-feed
- jekyll-mentions
- jekyll-redirect-from
- jekyll-sitemap
- jemoji
{% endhighlight %}

## Liquid Tags

Jekyll uses the [Liquid](http://liquidmarkup.org/ "Liquid Markup") templating language, which can be used in combination with the `include` tag to create some fairly powerful templates. The `include` tag supports passing variables to templates:

{% highlight liquid %}
{% raw %}
{% include footer.html param="value" param_2=page.param_value %}
{% endraw %}
{% endhighlight %}

Inside the template you can access these variables using `include.param` and `include.param_2` respectively.

### An Example

Liquid filters include a method for [formatting dates](https://docs.shopify.com/themes/liquid-documentation/filters/additional-filters#date "Liquid Date Filters"), but you're limited to a few options that might not support what you're looking for. I wanted to create a filter that added ordinals to the day (e.g. January 4**th**), but there's no way to do this using basic Liquid filters. If your site isn't hosted on GitHub Pages, the easiest way would be to create a simple filter and place this into the `_plugins/` directory.

If you're hosting your site on GitHub Pages, and don't want to push any generated content, you can still accomplish this by using the technique above. Using some basic Liquid syntax we can write:

{% highlight liquid %}
{% raw %}
{% assign d = include.date_param | date: "%-d" %}
{% case d %}
  {% when '1' or '21' or '31' %}{% assign ordinal = "st" %}
  {% when '2' or '22' %}{% assign ordinal = "nd" %}
  {% when '3' or '23' %}{% assign ordinal = "rd" %}
  {% else %}{% assign ordinal = "th" %}
{% endcase %}
{% endraw %}
{% endhighlight %}

Save this in a file called `ordinalize.html` and place it in the `_includes/` directory. To display an ordinalized date, you just need to include this template and can access the `ordinal` variable as a page variable. For example, to display the date formatted as "January 4th" you could use the following:

{% highlight liquid %}
{% raw %}
{% include ordinalize.html date_param=post.date %}
<h1>{{ post.title }}</h1>
<time datetime="{{ post.date }}">
  {{ post.date | date: "%B" }} {{ post.date | date: "%-d" }}{{ ordinal }}
</time>
{% endraw %}
{% endhighlight %}

## Data Files

Using data files for SVG icons
