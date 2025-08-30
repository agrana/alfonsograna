# Personal Jekyll Site

This is my personal website built with Jekyll, a static site generator.

## Features

- Clean, responsive design using the Minima theme
- Blog functionality with automatic post listing
- GitHub Pages deployment via GitHub Actions
- RSS feed support

## Local Development

To run this site locally:

1. Install Ruby (version 3.2 or higher)
2. Install Jekyll and Bundler:
   ```bash
   gem install jekyll bundler
   ```
3. Install dependencies:
   ```bash
   bundle install
   ```
4. Start the local server:
   ```bash
   bundle exec jekyll serve
   ```
5. Open [http://localhost:4000](http://localhost:4000) in your browser

## Adding New Posts

To create a new blog post:

1. Create a new file in the `_posts/` directory
2. Use the format: `YYYY-MM-DD-title.md`
3. Add the required front matter:
   ```yaml
   ---
   layout: post
   title: "Your Post Title"
   date: YYYY-MM-DD HH:MM:SS -0000
   categories: category1 category2
   ---
   ```

## Deployment

This site is automatically deployed to GitHub Pages when you push to the main branch. The deployment is handled by the GitHub Actions workflow in `.github/workflows/pages.yml`.

## Customization

- Edit `_config.yml` to change site settings
- Modify the theme or create custom layouts in the `_layouts/` directory
- Add custom styles in the `_sass/` directory
- Update the about page in `about.md`

## License

This project is open source and available under the [MIT License](LICENSE).
