+++
title = 'From Jekyll to Hugo'
categories = ['jekyll']
[hero]
  id = 'ljvKJ84BV3o'
  url = 'https://unsplash.com/photos/a-large-rock-sitting-on-top-of-a-beach-next-to-the-ocean-ljvKJ84BV3o'
  credit = 'NIR HIMI'
+++
Last weekend, I migrated this blog from [Jekyll](https://jekyllrb.com/) to [Hugo](https://gohugo.io/).
I've been using Jekyll for over 10 years and have been a financial backer since
2018. It's hard to say goodbye, but getting Ruby to work seems to becoming more
of a hassle. I'm also working mainly with Go in my day job (when I'm coding), so
the ecosystem feels at home to me.

The whole migration took the better part of two days, but I spent a lot of time
making sure the site was as identical to the old one. Had I decided that breaking
permalinks or other minor differences were okay, I wouldn't have had to spend so
much time digging through Hugo's docs.

I wanted to get some quick thoughts down on the migration process while fresh in
my mind. I also want to get back into writing a little bit more.

## Everything is content

Jekyll felt geared towards using it for a blog. Posts are a first class citizen.
You could create data sources other types of content, but it never felt natural.
In Hugo, posts are a category of content and you can create different types. For
example, if you look at the source of this site, you'll notice I have a talks type.

If you're dealing with more than a blog, this can be useful. I've built documentation
sites in Jekyll and it never felt like it fit that well. With Hugo, I don't see
that being an issue.

The one downside of this in Hugo, is it felt a little hard to create unique pages.
For example, I have a [name]({{< ref "/name" >}}) page that generates a random
name. To get that page to render, I had to create both a Markdown page and then
a layout. Perhaps there's a better way to do this, but it felt easier to add
unique pages in a Jekyll site.

## Hugo import

To start off the migration, I tried using `hugo import jekyll`. This didn't work
for me. My old site used a different directory structure than a standard Jekyll
site. I ended up having to start a new Hugo site and migrate everything over by
hand. If you have a standard layout for Jekyll, the import command would work
better, but YMMV.

## No plugins

I was using the Jekyll SEO plugin to help generate tags for Twitter Cards, Open
Graph, etc. For Hugo, there isn't your typical plugin system. So I dug into Open
Graph and ended up hand writing everything. I did drop Twitter Cards and validated
my Open Graph cards with [Open Graph Preview](https://www.opengraphpreview.com/)

After the fact, I learned that while there's not plugins, you can use themes as
a sort of plugin. Themes work by going through the list of configured themes until
it finds a matching layout, partial, or shortcode and using that. You can see a
list of third-party "components" and how to use them in [Awesome Hugo](https://github.com/theNewDynamic/awesome-hugo?tab=readme-ov-file#theme-components).

## Go templates

Because Hugo uses Go, it's not surprising that templating uses Go's template engine.
I know some people don't like it, but I've written a lot of Go templates and this
wasn't an issue for me. In fact, it felt more familiar to me than Liquid ever did.

## Shortcodes

By default Hugo doesn't render raw HTML included in markdown. Instead if you need
to include something more complex than Markdown, you can use a Shortcode.

For example, let's add some syntax highlighting. In Jekyll, you would highlight
code with:

```liquid
{% highlight yaml %}
{% endhighlight %}
```

In Hugo, the syntax is similar.

{{< highlight go-template >}}
{{</* highlight yaml */>}}
{{</* /highlight */>}}
{{< /highlight >}}

Hugo has other neat built-in [Shortcodes](https://gohugo.io/shortcodes/), like
[YouTube](https://gohugo.io/shortcodes/youtube/) and [Gists](https://gohugo.io/shortcodes/gist/).

{{< highlight go-template >}}
{{</* gist ivorisoutdoors id */>}}
{{< /highlight >}}

You can write your own Shortcodes. For example, I have a couple pages that include
links to slides, so I created a [Speaker Deck](https://github.com/ivorisoutdoors/website/blob/46fbd981f5c04c33e586df30abfa7482d7546fcd/layouts/shortcodes/speakerdeck.html)
shortcode. Now I can include slides with:

{{< highlight go-template >}}
{{</* speakerdeck slideid */>}}
{{< /highlight >}}

## Pipes

This feature is an absolute game changer. I was able to get rid of Webpack without
having to use another complicated frontend build process. I can build my assets
right from my layouts.

{{< highlight go-html-template >}}
{{ with resources.Get "css/main.css" | postCSS }}
  <link href="{{ .RelPermalink }}" rel="stylesheet">
{{ end }}

{{ with resources.Get "js/post.js" | js.Build }}
  <script src="{{ .RelPermalink }}"></script>
{{ end }}
{{< /highlight >}}

There's even options for minifying and fingerprinting your assets all right in
the template, no build system required!

{{< highlight go-html-template >}}
{{ with resources.Get "css/main.css" | postCSS | minify | fingerprint }}
  <link
    crossorigin="anonymous"
    href="{{ .RelPermalink }}"
    integrity="{{ .Data.Integrity }}"
    rel="preload stylesheet"
    as="style"
  />
{{ end }}

{{ with resources.Get "js/post.js" | js.Build (dict "minify" hugo.IsProduction) | fingerprint }}
  <script
    defer
    crossorigin="anonymous"
    src="{{ .RelPermalink }}"
    integrity="{{ .Data.Integrity }}"
  ></script>
{{ end }}
{{< /highlight >}}

## Minify HTML

To minify HTML in Jekyll, you have a couple of different options. There are some
plugins you can install, you could add a step to your build process, or I used a
Liquid template that would minify HTML for me. Each of these options has it's
own drawback. With Hugo, it's a flag you pass when building the site.

    hugo build --minify

## Closing thoughts

Is Hugo better than Jekyll? One tool isn't better than another in all situations.
It depends on what you need and what you're comfortable with. There's a ton of
static site generators out there. If you like Jekyll, use Jekyll. If you like
Gatsby, use that. For me, I found that Hugo is what works for me. It feels familiar
to me. If that gets me to update my blog, than that's the tool for me.
