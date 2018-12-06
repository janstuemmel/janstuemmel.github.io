Revealjs is a powerful framework for HTML based presentations. With Jekyll
you're able to host your presentations on Github Pages.

## Jekyll

Jekyll is a powerful static site generator for blogs and websites. See the
[Jekyll Homepage][jekyll] for further information.

Assuming you have ruby and gem already installed, type the following lines
in a terminal window to install Jekyll. (If haven't already installed ruby, see
[Install Ruby][ruby_install])

```sh
sudo gem install jekyll
jekyll new ghpages-slides
cd ghpages-slides
jekyll serve
```

Jekyll serves your static site at [localhost:4000](http://localhost:4000)

## Github Pages

[Github Pages][github_pages] will host static sites for you. You can create
a repository an then put all your static files in it. But maybe you have
a more complex site, and want to use templates for example. Well, Github Pages
works together with Jekyll, you can push your Jekyll 'source code' and Github
will automatically deploy your static site.

First, create a repository with following name convention: `<github_username>.github.io`,
then initialize a git repository and add the remote origin.

```sh
git init
git remote add origin git@github.com:<github_username>/<github_username>.github.io.git
git push origin master
```

A few seconds later, your site will be accessible under *http://\<github_username\>.github.io*.

## RevealJS

[RevealJS][revealjs] is a powerful framework for presentations. Write your presentations in
HTML and present it in a browser. You can even use Markdown syntax.

I will explain how i use RevealJS in my Jekyll based website:

First, create two directories `mkdir assets _slides`. Then download the contents
from their [Github Page][revealjs_github] and put them into `assets/revealjs`.
Maybe skip files in the root and `test`.

Then add following lines to the `_config.yml`:

```yml
collections:
  slides:
    output: true
    permalink: /:collection/:path

defaults:
  -
    scope:
      path: ""
      type: "slides"
    values:
      layout: "slides"
```

Let's create a new layout for slides, `touch _layouts/slides.html`:

```html
<html>
<head>
  <link rel="stylesheet" href="/assets/reveal.js/css/reveal.css">
  <link rel="stylesheet" href="/assets/reveal.js/css/theme/{{ "{% if page.theme " }}%}{{ "{{ page.theme " }}}}{{ "{% else " }}%}white{{ "{% endif " }}%}.css" id="theme">
</head>
<body>
  <div class="reveal">{{ "{{ content " }}}}</div>
  <script src="/assets/reveal.js/js/reveal.js"></script>
  <script>
    var reveal_config = {{ "{{ page.reveal_config | jsonify " }}}} || {};
    Reveal.initialize(reveal_config);
  </script>
</body>
</html>
```

Now, we can begin creating your presentation, cause' presentations normally
have a lot of images, we put everything in one folder. Create the folder
`mkdir _slides/test-slides` and put an `index.html` in there.

We can also set the `reveal_config` and a theme.

```html
---
title: Test Slides
description: Test Slides at the Lorem Ipsum Event October 2001
theme: night
date: 2016-1-05
reveal_config:
  controls: true
  progress: false
  transition: slide
---
<section>
  <h1>Slide 1</h1>
  <p>Hello World!</p>
</section>
<section>
  <h1>Slide 2</h1>
  <p>Hello again!</p>
</section>
```

Additionally, we can create a site that lists our presentations,
`touch slides.html`:

```html
---
layout: default
---
<ul>
  {{ "{% for slide in slides " }}%}
  <li>
    <h1>
      <a href="{{ "{{ slide.url | prepend: site.baseurl | remove: 'index' " }}}}">
        {{ "{{ slide.title " }}}}
      </a>
    </h1>
    <p>{{ "{{ slide.date | date: "%b %-d, %Y" " }}}} - {{ "{{ slide.description " }}}}</p>
  </li>
  {{ "{% endfor " }}%}
</ul>
```

For more features, like markdown support and code hightlighting, you have to extends
the slides layout, visit [RevealJS at Github][revealjs_github] and read the README.

[github_pages]: https://pages.github.com/
[ruby_install]: https://www.ruby-lang.org/en/documentation/installation
[jekyll]: https://jekyllrb.com/
[revealjs]: http://lab.hakim.se/reveal-js/
[revealjs_github]: https://github.com/hakimel/reveal.js
