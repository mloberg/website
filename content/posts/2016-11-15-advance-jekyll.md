+++
title = 'Advance Jekyll'
categories = ['jekyll']
summary = """
Bring your Jekyll site to the next level. Learn how to manage dependencies,
organize your site, write your own plugins, build assets, test your site, and
deploy changes automatically.
"""
proof = false
[hero]
  id = 'jb1Mc1lv8X0'
  url = 'https://unsplash.com/photos/jb1Mc1lv8X0'
  credit = 'Karsten Würth'
+++
This is the final part in a three part series on static site generators.

1. [Why Static Site Generators]({{< ref "2016-01-13-why-static-site-generators" >}})
2. [Getting Starting With Jekyll]({{< ref "2016-01-27-getting-started-with-jekyll" >}})
3. Advance Jekyll (_You are here_)

In the previous post I talked about [Jekyll][jekyll], a popular static site
generator written in Ruby. In fact, it's what this site has been using for a
while. During that time, I've picked up some tips and tricks that I want to
share with you today.

The things you find in here are not best practices, absolute truths, or even the
best way to do things. With development, there's a thousand different ways to do
something, these just happen to be what I found works.

### In This Post

* [Deploy To S3](#deploy-to-s3)
* [Manage Your Dependencies](#manage-your-dependencies)
* [Stop Excluding Files](#stop-excluding-files)
* [Plugins](#plugins)
* [Custom Liquid Tags](#custom-liquid-tags)
* [Custom Filters](#custom-filters)
* [Assets](#assets)
  * [Gulp](#gulp)
  * [Jekyll Assets](#jekyll-assets)
* [Testing](#testing)
* [Automated Deployments](#automated-deployments)
* [Wrapping Up](#wrapping-up)

## Deploy To S3

GitHub Pages offers a free solution for hosting a Jekyll site on a platform you
probably already use. This works wonderfully for the majority of sites out there.
However, because it is a hosted solution, there's limitations to it. You can't
run custom plugins and the build process has to be completely done through
Jekyll. The power of Jekyll is really unlocked when you deploy to another
platform that you have control over.

Because Jekyll outputs static files, the options for where you can deploy your
site are endless. You could manually upload the files to a web server you own or
even automate that process with a tool like [rsync][rsync].

This site is hosted on [Amazon S3][awsS3]. It was a product I was already using
and I don't have to worry about security, updates, or any of the other things
you have to with a normal web server. They have
[static website hosting][s3WebsiteHosting] built in, but it can be a bit
confusing to get setup. Instead, I recommend using a tool called
[s3_website][s3_website]. Not only does it manage S3 static hosting
configuration, it can upload your site for you (and only the files that changed),
use AWS CloudFront to distribute your website, manage HTTP cache control and
gzipping, and much more. Obviously you don't want to commit your AWS access or
secret keys, so either keep the file local, or commit an example copy of it
without those values.

## Manage Your Dependencies

As you dive deeper into customizing and streamlining your Jekyll site, you'll
find yourself using lots of different tools. Using a dependency management tool
like [Bundler][bundler] can help easy that and provide consistency between any
different environments you may be using.

To get you started, here is a starting `Gemfile` for Jekyll.

{{< highlight ruby >}}
source 'https://rubygems.org'

gem 'jekyll'
{{< /highlight >}}

You can also target a specific version of Jekyll.

{{< highlight ruby >}}
source 'https://rubygems.org'

gem 'jekyll', '~> 3.3'
{{< /highlight >}}

Make sure you also commit your `Gemfile.lock` file to manage specific versions
of dependencies.

## Stop Excluding Files

We've added two more files to our directory that we probably don't want to be a
part of our final site. If we add more tools like [Gulp][gulp] or [Yarn][yarn],
we would need to add more files to exclude from our site. Jekyll allows you to
define the source of your site. By default this is the current directory (`.`),
we can change that with the `source` option. Either on the command line or even
better, our `_config.yml` file.

{{< highlight yaml >}}
source: site
{{< /highlight >}}

This directory can contain only files that Jekyll uses to generate the site and
you won't have to worry about extra files getting into your build.

## Plugins

Now that we have all the boring stuff out of the way, we can start getting to
the cool parts. Arguably one of the most powerful features of Jekyll is it's
plugin system. There are plugins out there to
[generate sitemaps][jekyll-sitemap], [RSS feeds][jekyll-feed],
[add SEO tags][jekyll-seo], and more. There's even support for some plugins on
[GitHub Pages][pages-gem].

There are a couple different ways to install plugins in Jekyll. The first is to
list it in your `_config.yml`.

{{< highlight yaml >}}
gems:
  - jekyll-sitemap
{{< /highlight >}}

If you're using Bundler, you can use the `jekyll_plugins` group and those will
automatically be registered with Jekyll.

{{< highlight ruby >}}
# Gemfile

group :jekyll_plugins do
  gem 'jekyll-sitemap'
end
{{< /highlight >}}

You can find out all about Jekyll plugins at their [documentation][plugins].

## Custom Liquid Tags

[Liquid][liquid] is a pretty powerful templating language. With Jekyll we can
build on top of that and add custom tags. This is done by as a Jekyll plugin,
all we need is a little Ruby.

As an example, I want a tag that strips out all extra whitespace. To do this,
I create a file called `_plugins/spaceless.rb`.

{{< highlight ruby >}}
module Jekyll
  class Spaceless < Liquid::Block

    def initialize(tag_name, text, tokens)
      super
    end

    def render(context)
      text = super.to_s
      lines = text.split("\n").map { |line| line.strip }.reject! { |line| line.empty? }
      lines.join("\n") if lines
    end

  end
end

Liquid::Template.register_tag('spaceless', Jekyll::Spaceless)
{{< /highlight >}}

Now we have access to a `spaceless` tag in our site.

{{< highlight liquid >}}
{% spaceless %}
     This won't have whitespace before it
{% endspaceless %}
{{< /highlight >}}

## Custom Filters

You can also add Liquid filters. Filters are Ruby modules that export all their
methods to Liquid.

{{< highlight ruby >}}
module Jekyll
  module MyFilters
    def asset_url(input)
      "http://my-cdn.example.com/assets/#{input}"
    end
  end
end

Liquid::Template.register_filter(Jekyll::MyFilters)
{{< /highlight >}}

Filters are applied via pipe (`{{ profile.jpg | asset_url }}`).

## Assets

I remember when Jekyll didn't support Sass or CoffeeScript out of the box,
luckily that's all changed and adding Sass to your site is easy. Concatenating
CSS/JS, optimizing images, and adding in third-party assets aren't as easy. If
Jekyll out of the box works for you, that's great. If you need something a little
bit more advance, read on. I'm going to go over two different options.
[Gulp][gulp] or [Jekyll Assets][jekyll-assets].

### Gulp

Gulp is a build system for JavaScript. I've personally used it for other
projects and I love it. I'm not a front-end developer by any means, but it's
really easy to use. Unfortunately, there's a million different ways to configure
your build. If you're looking for a place to start, I would check out
[generator-jekyllized][generator-jekyllized].

I was never able to get a build pipeline that I liked in Gulp, so I don't have
much insight here, but there are tons of sites out there that have done this.

### Jekyll Assets

I was never able to get a build pipeline in Gulp that I liked, so I decided to
go with a pure Ruby option of [Jekyll 3 Assets][jekyll-assets]. If you're not
comfortable with NodeJS or Gulp, but need something better than what Jekyll
provides out of the box, this might be a good solution for you. Jekyll Assets is
an asset pipeline for Jekyll 3 using Sprockets, the same system Rails uses. With
the support for Sprockets, I was able to switch over to Jekyll Assets from my
old Gulp build in about 10 minutes.

## Testing

We have this badass site. It builds all our assets for us, we've customized it
with some third-party plugins and even a few of our own. It's perfect, how do we
make sure it stays that way? We can test it!

What can we test? First we can test that everything with Jekyll is ok. Let's run
the `doctor` command.

    $ jekyll doctor

We should also test that all the links in our site are going to resolve to a
working page. To do this, we use the [html-proofer][html-proofer] gem. This will
scan all links in HTML pages and make sure they return a real page. I've had
this catch links that were broken on old posts.

    $ jekyll build
    $ htmlproof [site-dest]

For most sites, this is probably sufficient. Let's say you have a site that uses
a lot of custom plugins or logic in your templates that you want to test. We
could use a tool like [Selenium][selenium] or a lighter tool like
[PhantomJS][phantomjs]. ~~I used [CasperJS][casperjs] for this site.~~
(_see below_) It offers some really useful tools specific for testing. Here is
an example test:

{{< highlight javascript >}}
casper.test.begin('Homepage', 7, function suite(test) {
  casper.start('http://localhost:4000/', function() {
    test.assertTitle("Matthew Loberg · Software Engineer");
    test.assertNotVisible('#sidebar', 'Sidebar is not visible');
    test.assertElementCount('.post', 5, 'There should be 5 posts on the first page.');
    this.click('.older');
  }).then(function() {
    test.assertUrlMatch(/page2/, "We are on the second page of posts.");
    test.assertElementCount('.post', 5, 'There should be 5 posts on the second page.');
    this.click('.sidebar-toggle');
  }).then(function() {
    test.assertVisible('#sidebar', 'Sidebar should be visible after clicking hamburger.');
    test.assertElementCount('#sidebar .social ul li', 2);
  }).run(function() {
    test.done();
  });
});
{{< /highlight >}}

You can check out the full test for this site on [GitHub][test-script].

Additionally you could do some CSS regression testing using a tool like
[BackstopJS][backstopjs], [Wraith][wraith], or [PhantomCSS][phantomcss].

__Update 10/25/17__: With ChromeDriver now supporting headless mode, I switched
to [Nightwatch.js][nightwatch]. The documentation feels lacking at points, but
it's very powerful and easier than Casper to write tests.

## Automated Deployments

One of the things I really missed about GitHub Pages was the ability to push a
change to my site and have it automatically built. With s3_website, I need to
remember to build and deploy my site. If I'm away from home, I can't make a
change with GitHub's interface and have it automatically build.

Or can I?

With a tool such as [TravisCI][travisci] it is possible to have automatic
deploys on push. First let's create a `.travis.yml` that just builds and tests
our site.

{{< highlight yaml >}}
language: ruby
sudo: false
rvm:
  - 2.1

cache:
  directories:
    - vendor

env:
  global:
    - TZ=America/Chicago # Makes sure posts are generated with the right timestamp
    - NOKOGIRI_USE_SYSTEM_LIBRARIES=true # speeds up installation of html-proofer
    - JEKYLL_ENV=production

install: bundle install --jobs=3 --retry=3 --path vendor --standalone --deployment

script:
  - bundle exec jekyll build
  - bundle exec htmlproof build
{{< /highlight >}}

Here we're using a Ruby 2.1 Travis container to build our site. I've set a
custom install script that installs gems to `vendor` and then caches that
directory to speed up the installation process. For our test script, we build
the site and then run [html-proofer][html-proofer] against the build.

Travis has an `after_script` option that will run after a successful build. We
don't want to expose our AWS access and secret key, so we need to add them as
[encrypted variables][encrypted-variables]. Then change our `s3_website.yml` to
load those from the environment instead.

{{< highlight yaml >}}
s3_id: <%= ENV['S3_ID'] %>
s3_secret: <%= ENV['S3_SECRET'] %>
s3_bucket: <%= ENV['S3_BUCKET'] %>
cloudfront_distribution_id: <%= ENV['CF_ID'] %>
{{< /highlight >}}

Finally, we only want to run this on the master branch. Travis gives an option
to only run on certain branches.

{{< highlight yaml >}}
language: ruby
sudo: false
rvm:
  - 2.1

cache:
  directories:
    - vendor

env:
  global:
    - TZ=America/Chicago # Makes sure posts are generated with the right timestamp
    - NOKOGIRI_USE_SYSTEM_LIBRARIES=true # speeds up installation of html-proofer
    - JEKYLL_ENV=production
    - secure: xxx
    - secure: xxx
    - secure: xxx
    - secure: xxx

install: bundle install --jobs=3 --retry=3 --path vendor --standalone --deployment

script:
  - bundle exec jekyll build
  - bundle exec htmlproof build

after_success: bundle exec s3_website push

branches:
  only:
    - master
{{< /highlight >}}

If we make a change to our site using GitHub's interface, it should
automatically build and push our site. Huzzah!

## Wrapping Up

This is only scratching the surface of what Jekyll and static site builders are
capable of. If you have any useful tips or tricks you've learned with Jekyll,
please share them in the comments.

[jekyll]: http://jekyllrb.com/
[awsS3]: https://aws.amazon.com/s3/
[rsync]: https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories-on-a-vps
[s3WebsiteHosting]: http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html
[s3_website]: https://github.com/laurilehmijoki/s3_website
[bundler]: http://bundler.io/
[gulp]: http://gulpjs.com/
[yarn]: https://yarnpkg.com/
[jekyll-sitemap]: https://github.com/jekyll/jekyll-sitemap
[jekyll-feed]: https://github.com/jekyll/jekyll-feed
[jekyll-seo]: https://github.com/jekyll/jekyll-seo-tag
[pages-gem]: https://github.com/github/pages-gem
[plugins]: http://jekyllrb.com/docs/plugins/
[liquid]: http://shopify.github.io/liquid/
[gulp]: http://gulpjs.com/
[jekyll-assets]: https://github.com/jekyll/jekyll-assets
[generator-jekyllized]: https://github.com/sondr3/generator-jekyllized
[selenium]: http://docs.seleniumhq.org/
[phantomjs]: http://phantomjs.org/
[casperjs]: http://casperjs.org/
[test-script]: https://github.com/ivorisoutdoors/website/blob/cb1e3bca2b1e9c1d303570431d2d1f180eeccc60/test.js
[backstopjs]: https://github.com/garris/BackstopJS
[wraith]: http://bbc-news.github.io/wraith/index.html
[phantomcss]: https://github.com/Huddle/PhantomCSS
[travisci]: https://travis-ci.org/
[encrypted-variables]: https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml
[nightwatch]: http://nightwatchjs.org/
[html-proofer]: https://github.com/gjtorikian/html-proofer
