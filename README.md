# Website Updating Workflow

## Testing locally

Running `bundle exec jekyll serve` will build and serve the website locally using `jekyll`. This will let you preview what the website will look like before deploying.

## Adding new projects

New projects can be added by writing markdown files in `_projects` (just follow the other existing files). You can specify images and links to accompany presentation. These are based on my own personal layout, which is simple and can be found in `_layouts/projects.html`.

## Adding new blog posts

Same procedure as normal—add new blog posts as markdown files in the `_posts` folder.

## Styling

CSS is located in `_sass`

## Resources

I've tried to organize different images and resources into folders in the `assets` directory. For project images, place them in `assets/projects`, and for blog posts `assets/blogs`. For all web page general resources, put them in `assets/img`.