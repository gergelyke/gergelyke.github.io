---
title: Using GitHub for comments on your blog
description: A free Disqus alternative using GitHub comments, mainly for blogs with developer audiences 
githubIssueId: 2
date: 2017-10-07
tags:
    - 'github'
    - 'tutorials'
---

As I've just started to build my personal website, I wanted to add a comment section for each published article. Previously I've used Disqus to do so - however, this time I wanted to keep it as lightweight as possible.

### The problem with Disqus

Disqus is a great product, no question there. But all the great features Disqus has to offer come with a price. A price you will pay with the loading time of your website:

![disqus load time](/images/disqus_load_time.png)

If you are interested in details how Disqus works, check out the article I wrote a few years back on the topic on the RisingStack blog: [On Third-Party JavaScript - In Production Case-Study](https://blog.risingstack.com/on-third-party-javascript-in-production/).

### What I ended up using instead: GitHub

[GitHub has a very well documented REST API](https://developer.github.com/v3/), which exposes most of the resources used by GitHub. Luckily, it also exposes issues, with all of their information, [including comments](https://developer.github.com/v3/issues/#get-a-single-issue)! 🤗 

The only thing left was to write a small JavaScript snippet that grabs the details of an issue and append it to the website. The script looks like something like this:

```javascript
const apiUrl = 'https://api.github.com/repos/gergelyke/gergelyke.github.io/issues/<%- page.githubIssueId %>/comments'

$(function() { 
  $.ajax(apiUrl, {
    headers: {
        Accept: 'application/vnd.github.v3.html+json'
    },
    dataType: 'json',
    success: function(comments) {
      const $commentSection = $('.blog-post-comments')
      comments.forEach(function (comment) {
        $commentSection.append(
          `
          <div class="comment">
            <a class="commenter" href="${comment.user.html_url}" 
              target="_blank">${comment.user.login}</a>, 
              ${new Date(comment.created_at).toUTCString()}
            <p>
              ${comment.body_html}
            </p>
          </div>
          `
        )
      })
    }
  })
})
```

Actually, you can already comment on this post using the very technique you just read about! Give it a try! 😎

You can see the actual implementation [here](https://github.com/gergelyke/gergelyke.github.io/blob/master/src/themes/cactus-light/layout/_partial/comments.ejs).

### Gotchas

As most solutions, using GitHub for the comment engine on your site has some drawbacks:

* [GitHub rate limits its' APIs](https://developer.github.com/v3/#rate-limiting), so this solution won't work if you have a huge number of visitors,
* it only suits audiences with GitHub accounts.

If you are ok with this limitations, GitHub provides a great way to let your audience comment on your writings.
