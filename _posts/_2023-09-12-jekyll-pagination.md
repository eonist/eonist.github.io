Notes on Jekyll pagination<!--more-->

- Find ruby version: `ruby -v`
- Pagination pages through every post in the posts variable unless a post has hidden: true in its front matter.

Add this to _config.yml
```yml
gems: [jekyll-paginate]

paginate: 5

paginate_path: "page:num"
```


### To get pagination v1 working:
1. Make blog.md in pages (with permalink in frontmater, layout and title )
2. add root/blog/index.html with the layout in the frontmater (this is the pagination template)

### Gotchas:
- jekyll-paginate-v2 plugin is not supported by GitHub Pages.
- You also don't need jekyll-paginate explicitly in your Gemfile as it's already included in github-pages gem. see https://stackoverflow.com/a/56410675/5389500

### Resources:
- Paginator code: https://shivabhusal.github.io/jekyll-paginator/
- tags, topics, cloud topics etc: https://www.thecandidstartup.org/2023/02/20/jekyll-pagination-tagging.html
- Supported github jekyll plugins: https://pages.github.com/versions/
- official doc (also has first page ege case code): https://jekyllrb.com/docs/pagination/#enable-pagination
- running github pages locally: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll
- some nuanced info to get pagination v1 working (altho missing some key info): https://talk.jekyllrb.com/t/paginate-not-showing-page-2/2122/6
- adding indext list of pages https://stackoverflow.com/questions/67493944/add-ellipses-to-pagination-using-jekyll-paginate-v2
- some claim that you have to use special serve for pagination to work: https://stackoverflow.com/a/70602326/5389500
- some tips regarding installing pagination: https://stackoverflow.com/questions/35401566/dont-have-jekyll-paginate-or-one-of-its-dependencies-installed

### Syntax:
```yml
paginator.page ## The number of the current page
paginator.per_page ## Number of posts per page
paginator.posts ## Posts available for the current page
paginator.total_posts ## Total number of posts
paginator.total_pages ## Total number of pages
paginator.previous_page ## The number of the previous page, or nil if no previous page exists
paginator.previous_page_path ## The path to the previous page, or nil if no previous page exists
paginator.next_page ## The number of the next page, or nil if no subsequent page exists
paginator.next_page_path ## The path to the next page, or nil if no subsequent page exists
```
