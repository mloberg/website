+++
title = 'Getting Started With Jekyll'
categories = ['jekyll']
summary = """
Learn how to setup a Jekyll site complete with layouts, includes, and basic assets.
Then dive into how to create a post and publish it to GitHub pages.
"""
proof = false
[hero]
  id = 'jb1Mc1lv8X0'
  url = 'https://unsplash.com/photos/jb1Mc1lv8X0'
  credit = 'Karsten Würth'
+++
This is part two in a three part series on static site generators.

1. [Why Static Site Generators]({{< ref "2016-01-13-why-static-site-generators" >}})
2. Getting Starting With Jekyll (_You are here_)
3. [Advance Jekyll]({{< ref "2016-11-15-advance-jekyll" >}})

In the first post I talked about what static sites are and a little bit about
static site generators. I talked a lot about [Jekyll][jekyll], which what we are
going to be talking about today.

Jekyll is a static site generator written in Ruby. It uses Markdown
(or Textile), and [Liquid][liquid] to generate a static site. It's also
blog-aware, which means there's a lot of cool features around blog posts such as
permalinks and categories. In fact this blog post you are reading right now, was
generated using Jekyll.

## Installation

To [use Jekyll][requirements], you'll need Ruby, RubyGems, and a *nix system.
If you are using Jekyll 3 you need Ruby v2 or above. If you are using Jekyll 2,
you will need Ruby 1.9.3 or greater, NodeJS, and Python 2.7. Because Jekyll is
distributed as a Gem, installation is really easy.

    $ [sudo] gem install jekyll

## Creating a New Site

To look at what's in a Jekyll site, we are going to create a new site using the
`jekyll new` command. This will setup some boilerplate code for a Jekyll site.

    $ jekyll new myblog
    $ cd myblog
    $ jekyll serve

This will start a development server at `http://localhost:4000`.

### Directory Structure

Let's take a look at the directory structure.

    .
    |___ _config.yml
    |___ _includes
    | |___ footer.html
    | |___ head.html
    | |___ header.html
    | |___ icon-github.html
    | |___ icon-github.svg
    | |___ icon-twitter.html
    | |___ icon-twitter.svg
    |___ _layouts
    | |___ default.html
    | |___ page.html
    | |___ post.html
    |___ _posts
    | |___ 2016-01-27-welcome-to-jekyll.markdown
    |___ _sass
    | |___ _base.scss
    | |___ _layout.scss
    | |___ _syntax-highlighting.scss
    |___ about.md
    |___ css
    | |___ main.scss
    |___ feed.xml
    |___ index.html

### Configuration

The `_config.yml` file is the [configuration][configuration] file for Jekyll.
One of the cool things about Jekyll is that none of these files are required.
Even though this file holds the configuration for the site, there are enough
defaults that it can build the site without this file. In addition you can add
custom settings that will become global site variables.

### Includes

The `_includes` directory holds any partials that can be included in your
layouts or pages. To do this, use the _include_ Liquid tag. For example:
`{% include footer.html %}` will include `_includes/footer.html`.

### Layouts

The `_layouts` directory holds all the page templates for the site. Layouts are
chosen by the [YAML Front Matter][yfm] of a page. To inject the page content,
the `{{ content }}` Liquid tag is used.

### Posts

Remember how I said Jekyll is blog-aware? The `_posts` directory contains all
posts for the site and will generate [permalinks][permalinks] based on the file
name. The default will use the format
`/:categories/:year/:month/:day/:title.html`, but you can customize that using
`_config.yml`.

### Assets

Your site won't just be HTML pages, you'll have CSS and possibly some
JavaScript. You could use just normal CSS or Jekyll has support for both Sass
and CoffeeScript. In the site generated for us, it has some Sass files. First
there is a `_sass` directory, which is our Sass partials directory. The we have
`css/main.scss`. If you look at this file, you'll notice it has empty YAML Front
Matter. In order to process through Jekyll, files must include a YAML Front
Matter block. You can read more about [assets][assets] at Jekyll's docs.

### YAML Front Matter

Before we talk about how to add a page to Jekyll, let's talk about
[YAML Front Matter][yfm]. YAML Front Matter is a block of [YAML][yaml] that is
used for building the page. It can contain information such as the layout to
use, the page title, permalink, or any other custom settings or page variables.
Any page that contains Front Matter, will be processed by Jekyll. Otherwise the
file will just be copied over during build. Front Matter must be the first thing
in a file. It must start and end with three-dashed lines. Here is an example:

    ---
    layout: default
    title: About
    permalink: /about/
    ---
    <!-- page content -->

### Content

Now that we've covered what Front Matter is, let's talk about creating some
content. First, let's take a look at `index.html`. This is an HTML file, but
because it contains Front Matter, it will be processed by Jekyll and use the
`default` template. The HTML file can contain just HTML with Front Matter, but
it can also contain Liquid tags. [Liquid][liquid] is a templating language that
feels similar to Twig or Jinja. For example, if we want to list out all our
posts, we can do that using this block of code:

{{< highlight html >}}
<ul>
    {% for post in site.posts %}
        <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
{{< /highlight >}}

That's pretty cool, but sometimes writing HTML for every page is lame. Luckily
we don't have to as Jekyll supports Markdown and Textile. This will take
Markdown (or Textile if you so choose) and then convert it into an HTML file.
How awesome is that? You can write your blog posts in Markdown!

With content, you aren't just limited to HTML or Markdown files. Any file that
contains Front Matter can use Liquid and be processed by Jekyll during the
build. For example, in the site Jekyll gave us, it has a `feed.xml` file that
creates an RSS feed from all the posts. You could also create a JSON file that
contains information about all your blog posts to do searching through
JavaScript.

### Customization

I hope you're getting a sense of how cool Jekyll is and all the stuff you can do
with it, but we've barely scratched the surface. There's [drafts][drafts],
[collections][collections], [data files][datafiles], [pagination][pagination],
and [variables][variables] that allow you to do some pretty cool stuff.
If that's not enough for you, you can add [plugins][plugins] or even write your
own to really show how powerful Jekyll can be.

I'm not going to dig into this stuff in this post. If you are interested, part
three of this series will be about some more advance Jekyll features and tricks,
otherwise the [Jekyll docs][docs] has a ton of information.

## Deploying to GitHub Pages

Now that you've got a functioning Jekyll site that you can view with
`jekyll serve`, how do you get that deployed for the world to see? There's a lot
of options here because it is just static files. You can drop them on any web
server. You can also create this advance script that will deploy your site
around the globe using Amazon Web Services every time you run `git push` (which
I'll be talking about in the next post). Or you can keep it simple (and free)
and deploy to [GitHub Pages][ghpages]. We're going to be talking about deploying
an account/organization site, but you can also have a site for a
[project][babl] as well.

Setting up a site through GitHub Pages is easy. All you have to do is create
a repository called _username_.github.io, push your code to the repository, and
wait for it to build (the first time can be slow).

This will host your site at http://_username_.github.io/. If you want to use
your own domain name, worry not, it's [really easy][ghpages-cname]. All you need
to do is add a file called `CNAME` that contains your custom domain name. This
should not include the `http:` or any slashes (`mlo.io` not `http://mlo.io`).
Commit and push this file. Then you'll need to add record to your DNS provider.
If you are using a subdomain, you can use a `CNAME` record. This is the
recommended option as it gives you the benefit of their Content Delivery Network
and any changes to the IP of the GitHub servers (which they've done) won't
affect your site. If you can't use a `CNAME` record, you can use an `A` record.
To get the current IP address and more information, please visit their
[help page][ghpages-arecord].

While GitHub Pages is awesome, free, and easy, there are some limitations and
potential downfalls to the service. The first is that you can't run any custom
plugins, you can only run plugins listed on [this page][ghpage-plugins]. One
thing you'll notice, is that at the time of writing GitHub Pages is only running
Jekyll 2.4.0. While this shouldn't be an issue for most sites, it may cause some
inconsistencies between what's built on your machine and what GitHub Pages will
deploy. Finally, the biggest issue for me with GitHub Pages in the past was
downtime. A couple years ago, my site would go down for hours at a time. I'm not
sure if this is still an issue or if they have improved their architecture to
prevent it from happening, but it was the reason why I moved my site to AWS.

## Build Something Awesome

With all this new-found knowledge of Jekyll, I hope you build some cool sites or
[migrate][migrate] over your blog to Jekyll. Maybe you're still not 100% sold on
the idea of a static site. In the next post, I'm going to go through some more
advance Jekyll features and hopefully show you that Jekyll is a lot more
powerful than you think.

**Update 2/1/16**: GitHub Pages just upgraded to Jekyll 3.0 along with a few
other changes to the service. You can view their [blog post][ghpages-jekyll3]
for more information.

[jekyll]: http://jekyllrb.com/
[docs]: http://jekyllrb.com/docs/home/
[requirements]: http://jekyllrb.com/docs/installation/#requirements
[configuration]: http://jekyllrb.com/docs/configuration/
[yfm]: http://jekyllrb.com/docs/frontmatter/
[permalinks]: http://jekyllrb.com/docs/permalinks/
[assets]: http://jekyllrb.com/docs/assets/
[drafts]: https://jekyllrb.com/docs/posts/#drafts
[collections]: http://jekyllrb.com/docs/collections/
[datafiles]: http://jekyllrb.com/docs/datafiles/
[pagination]: http://jekyllrb.com/docs/pagination/
[variables]: http://jekyllrb.com/docs/variables/
[plugins]: http://jekyllrb.com/docs/plugins/
[migrate]: http://jekyllrb.com/docs/migrations/
[liquid]: http://liquidmarkup.org/
[yaml]: http://yaml.org/
[ghpages]: https://pages.github.com/
[ghpages-cname]: https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/
[ghpages-arecord]: https://help.github.com/articles/tips-for-configuring-an-a-record-with-your-dns-provider/
[ghpages-jekyll3]: https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0
[babl]: https://github.com/ivorisoutdoors/Babl/tree/gh-pages
