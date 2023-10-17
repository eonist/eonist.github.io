
These are my notes on implementing pagination in Jekyll, including tips on enabling pagination, using the `jekyll-paginate` gem, and available resources.<!--more-->

- To find your Ruby version, run `ruby -v`.
- By default, pagination will display every post in the `posts` variable, unless a post has `hidden: true` in its front matter. This can be customized to display a specific number of posts per page.
- To enable pagination, add the following code to your `_config.yml` file:

```yml
gems: [jekyll-paginate]

paginate: 5

paginate_path: "page:num"
```

### Gotchas:
- The `jekyll-paginate-v2` plugin is not supported on GitHub Pages, so you should use the `jekyll-paginate` gem instead. This gem is already included in the `github-pages` gem, so you don't need to include it in your Gemfile. See https://stackoverflow.com/a/56410675/5389500.

### Resources:
- Paginator code: a tutorial on how to implement pagination in Jekyll (https://shivabhusal.github.io/jekyll-paginator/)
- Tags, topics, cloud topics, etc.: a guide to implementing tags and topics in Jekyll pagination (https://www.thecandidstartup.org/2023/02/20/jekyll-pagination-tagging.html)
- Supported GitHub Jekyll plugins: a list of plugins supported on GitHub Pages (https://pages.github.com/versions/)
- Official doc: Jekyll's official documentation on pagination, including code examples (https://jekyllrb.com/docs/pagination/#enable-pagination)
- Running GitHub Pages locally: a guide to testing your GitHub Pages site locally with Jekyll (https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
- Some nuanced info to get pagination v1 working (although missing some key info): a forum post with tips on implementing pagination in Jekyll (https://talk.jekyllrb.com/t/paginate-not-showing-page-2/2122/6)
- Adding index list of pages: a Stack Overflow post with tips on adding an index list of pages (https://stackoverflow.com/questions/67493944/add-ellipses-to-pagination-using-jekyll-paginate-v2)
- Some claim that you have to use a special server for pagination to work: a Stack Overflow post with tips on getting pagination to work (https://stackoverflow.com/a/70602326/5389500)
- Some tips regarding installing pagination: a Stack Overflow post with tips on installing Jekyll pagination (https://stackoverflow.com/questions/35401566/dont-have-jekyll-paginate-or-one-of-its-dependencies-installed)

### Syntax:
```yml
paginator.page: the number of the current page
paginator.per_page: number of posts per page
paginator.posts: posts available for the current page
paginator.total_posts: total number of posts
paginator.total_pages: total number of pages
paginator.previous_page: the number of the previous page, or nil if no previous page exists
paginator.previous_page_path: the path to the previous page, or nil if no previous page exists
paginator.next_page: the number of the next page, or nil if no subsequent page exists
paginator.next_page_path: the path to the next page, or nil if no subsequent page exists
```

