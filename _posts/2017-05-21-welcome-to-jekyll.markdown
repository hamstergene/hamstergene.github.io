---
layout: post
title:  "Welcome to Jekyll!"
categories: jekyll update
---

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

As of April 2013, just under 12% of web users are browsing with IE 7 & 8 (according to W3C). That may not seem like many people to worry about, but visitors using iOS and Android are only 2% higher when you combine the two. So who do you code for: the 12% who won’t benefit from your media queries, or the 14% that want a smoother mobile experience?

That’s up to you. Statistics from 3rd parties may sound impressive, but they may differ wildly from site to site. Less than 1% of your visitors might be @media incompatible, while over 50% are mobile users. Knowing your audience can show you were to focus your efforts. Something like Google Analytics can show you who your visitors are, and what kind of devices / browsers they’re using. If it turns out that a much larger portion of your visitors are using IE8 than mobile, then you have my sympathies – but at least you know.

 

(Google Analytics and Webmaster Tools can also help you start looking at your site’s SEO health in a simple, straightforward way: read my Simple SEO Health Check: Part One)

 

There are various Javascript libraries out there that try to polyfill functionality back into older browsers like IE, which would render this problem largely irrelevant. But they are an extra consideration in terms of performance, especially when an older version of IE might imply an older and much less powerful PC is in use. If they’re still using IE 7 or 8, it’s probably crawling along with malware and bloatware galore.

In my mind, it’s about shaping the best user experience for your actual users, not just the trends you hear about. That’s not to say don’t be prepared for both, or for your user base to grow and change. But if you’re finding it difficult to decide where to start with a responsive layout for your site, it’s good information to keep in mind.

I’m going to continue using a combination of the two: min-width for more “heavy” styling (including background images, etc), and max-width for more subtle layout changes that I want to be visible for even the most archaic browsers. It’s certainly a reminder that the sheer variety and flexibility of the web continues to present interesting questions and challenges for developers, even in small ways.