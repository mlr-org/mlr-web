**The blog has moved to https://github.com/mlr-org/mlr3website on 2022-03.**


[![Netlify Status](https://api.netlify.com/api/v1/badges/d1f08466-dd92-49e3-a200-fdedccb8e9ed/deploy-status)](https://app.netlify.com/sites/mlr-web/deploys)

Sources of the main [mlr website](https://mlr-web.netlify.com/) including the blog post sources.

`blogdown` and Hugo are used to render the site. Deployment is done via Netlify.

It is highly recommended to use **RStudio** for tasks in this repo.
It integrates very well with the live preview of `blogdown`.

# How to add a new blog post

1. Add yourself as an author to <https://github.com/mlr-org/mlr/wiki/Misc#mlr-developer-team-and-contributors-page> and create an "author page" for the website (similar to the ones in `data/authors`).

1. Create a new post in `content/post/` via `blogdown::new_post(title = "<title>", categories = "R", ext = ".Rmd")`
1. Put external images in `post/<name of your post>/<image name>` and reference it via `[<alt text>](<image name>)` in the post.
1. Run `git submodule update --init --recursive` from the repo root to clone the Hugo theme.
1. Preview your post by running `blogdown::serve_site()`.
1. Commit the **Rmd and HTML** file and possible external resources.
1. Open a Pull Request. You can preview the changes you made by clicking on "Details" of your opened PR on Github:

![Netlify preview example](static/images/readme01.png)

## RSS

Hugo automatically creates RSS feeds (index.xml) for tags and categories.

For R-Bloggers the feed should return the full content, not just a summary.
To accomplish this, a custom `layouts/_default/rss.xml` file exists where `.Summary` was replaced by `.Content`.

The RSS feed used for R-Weekly and R-Bloggers is <https://mlr-org.com/categories/r/index.xml>.
Hence, every post tagged with `categories: r` will also forward the post to these two sites.
