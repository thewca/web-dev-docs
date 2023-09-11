# WCA Software Documentation

The WCA's [docs website](https://docs.worldcubeassociation.org/) is automatically deployed to Github Pages using Jekyll, a Ruby-based static site generator which Github Pages supports natively. We use the [Just the Docs](https://just-the-docs.com/) theme.

## Changing Content

If you just want to change content on an existing page, there's no setup required. All content is stored in markdown files, either at the top-level or in subfolders (eg, "contributing", "guides", etc). So, you can just:
1. Clone this repo
2. Find that page's markdown folder, make the text changes
3. Submit the change as a PR

## Running Locally

Running on your machine will let you preview the changes before you make them. The setup for this is pretty fast: 
1. Install the [prerequisites](https://jekyllrb.com/docs/installation/)
2. Clone this repo (if you haven't already)
3. Run `bundle install` to install the required dependencies
4. Run `bundle exec jekyll serve --livereload` to run locally, with automatic reloading whenever you make a change.

*Note: You must restart the server if changes are made to `_config.yml` - live reloading does not support config changes.*

## Deploying Changes

Changes are automatically deployed to Github Pages when merged into main - note that *it can take up to 10 minutes for changes to appear*.

If changes aren't showing up, please:
- Make sure that the pages you're expecting to see show up in the files of the Git repo you might have just forgotten to add them! 
- Check the "Actions" tab for any errors during the build or deploy process
- Ensure that the website is a valid Jekyll configuration runs locally on your machine (see above)

## FAQ

### How do you edit the left navigation bar? 

If you're familiar with Jekyll, we do not use Collections to organize the content. JustTheDocs provides navigation tools which we make use of. 

In short, we create parent/child relationships between pages in the "frontmatter" of our markdown files. The values that matter for parent-child relationships are the frontmatter values - folder names and filenames are just for convenience, they do not impact the sidebar.

- To create a "parent", the frontmatter should have the `has_children: true`
- To add a child to a parent, frontmatter should have `parent: Example Page` (where "Example Page") gets replaced with the `title` property specified in the parent's frontmatter

**Ordering of sidebar components:** Change the `nav_order` property in the frontmatter to have the integer position where you want it to appear (eg, `nav_order: 1`)

### I want to do something that isn't discussed in this README.

Consult the documentation for either:
- [Jekyll](https://jekyllrb.com/docs/), or
- [Just the Docs](https://just-the-docs.com/)

### Why is `_site` in the Gitignore?

Basically: `_site` is the *output* that Jekyll creates - this repo should only store the *inputs* for Jekyll. 

Jekyll is a static site generator - meaning that it takes inputs in the form of `_config.yml`, your various markdown files, and other snippets/assets, and precompiles them into HTML which is then served to the user. Each time Jekyll is run, it creates `_site`, which contains the compiled website. Github Pages automatically runs the Jekyll build process and generates its own `_site` directory - meaning we should not be including a conflicting/redundant copy in the repo.
