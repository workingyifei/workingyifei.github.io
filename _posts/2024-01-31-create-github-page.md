---
layout: post
title:  "How to create a github page with Jekyll"
date:   2024-01-31 15:44:27 -0000
categories: [Notes]
---
Steps I used to set up my github pages. 

## Step 1: Install Jekyll
### Prerequisites:
- Ruby 2.5.0 or higher with `ruby -v`
- RubyGems with `gem -v`
- GCC and Make
    ```
    xcode-select --install # Xcode command line tools installation
    gcc -v
    g++ -v 
    make -v
    ```
- Bundler, which is already pre-installed on my macOS

### Installation:
1. `gem install Jekyll` shows a permission error, instead of sudo, as recommended by github and Jekyll, bundle is used to install jekyll.
2. create a github page following the steps in GitHub Pages doc, `git clone` to local
3. `cd` to local project directory, create a 'Gemfile' in the jekyll project directory with `bundle init`
4. edit the 'Gemfile' and ad jekyll as a dependency `gem "jekyll"`
5. run `bundle install` instead of `gem install jekyll` to install jekyll and its dependencies.
6. jekyll should be installed successfully, verify with `bundle info jekyll`


## Step 2: Creating the site 
- To keep things simple, i decided to deploy from `main` branch and `/root` folder. 
- create a new jekyll site `jekyll new --skip-bundle'`
- prompts me that current dir is not empty, add `--force` to overwrite existing files
- update `Gemfile` to comment out `gem "jekyll"`
- update the GITHUB-PAGES-VERSION to 228, the version can be found in [github page versions](https://pages.github.com/versions/)
- save the `Gemfile` and run `bundle install`
- run `bundle exec jekyll serve` and navigate to `http://localhost:4000` to test the site locally.


## Step 3: Update the `_config.yml`
- update site name, email, settings relatting to the site
- update about.md
- update index.md
- git commit and push to remote 


## References:
- https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll
- https://jekyllrb.com/docs/
- https://jekyllrb.com/docs/ruby-101/
