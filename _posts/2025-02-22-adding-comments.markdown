---
layout: post
title: "Adding Disqus Comments to My GitHub Pages Blog"
date: 2025-02-22 13:00:00 -0500
categories: Technology
comments: true
---

Today I successfully added comments to my GitHub Pages blog with help from [Claude](https://claude.ai) (Anthropic's AI assistant). While my blog was built with static pages using Jekyll and the Minima theme, I wanted to enable readers to engage with my content through comments. Here's how I implemented Disqus comments on my site.

## The Setup Process

First, I created a Disqus account and set up a new site at [Disqus Admin](https://disqus.com/admin/create/) to get my unique shortname. Then I set up several key files and directories in my blog:

### 1. Configure Disqus

I added this to my `_config.yml`:

```yaml
disqus:
  shortname: humanintheloop  # My unique Disqus shortname
```

This configuration tells Jekyll what Disqus site to connect to. The shortname is like a unique identifier that links my blog to my Disqus account.

### 2. Set Up the Comments Template

I created an `_includes` folder and added `disqus_comments.html` with this code:

```html
{%- if page.comments != false -%}
  <div id="disqus_thread"></div>
  <script>
    var disqus_config = function () {
      this.page.url = '{{ page.url | absolute_url }}';
      this.page.identifier = '{{ page.url | absolute_url }}';
    };
    (function() {
      var d = document, s = d.createElement('script');
      s.src = 'https://humanintheloop.disqus.com/embed.js';
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
    })();
  </script>
  <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
</div>
{%- endif -%}
```

This template does several important things:

- Checks if comments are enabled for the page
- Creates a container for the comments section (`disqus_thread`)
- Sets up the page URL and identifier so Disqus knows which page the comments belong to
- Loads the Disqus JavaScript code that creates the comments interface
- Provides a fallback message for users without JavaScript

### 3. Create the Post Layout

I created an `_layouts` folder with `post.html` containing:

```html
---
layout: default
---
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">
  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time class="dt-published" datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        {{ page.date | date: date_format }}
      </time>
      {%- if page.author -%}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span class="p-author h-card" itemprop="name">{{ page.author }}</span></span>
      {%- endif -%}</p>
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {{ content }}
  </div>

  {%- if site.disqus.shortname -%}
    {%- include disqus_comments.html -%}
  {%- endif -%}

  <a class="u-url" href="{{ page.url | relative_url }}" hidden></a>
</article>
```

This layout template:

- Uses Jekyll's default layout as a base
- Creates a structured article with proper HTML5 semantics
- Displays the post title, date, and author if specified
- Shows the post content
- Includes the Disqus comments section if a shortname is configured
- Adds metadata for better SEO

### 4. Enable Comments on Posts

For each post where I want comments, I added `comments: true` to the front matter:

```yaml
---
layout: post
title: "Your Post Title"
date: 2025-02-21 9:00:00 -0500
categories: YourCategory
comments: true
---
```

This front matter tells Jekyll that this specific post should display comments. If I want to disable comments on a particular post, I can set `comments: false` or omit the line entirely.

## The Result

After pushing my changes to GitHub and waiting for the pages to rebuild, I was excited to see the Disqus comments section appear at the bottom of my posts! The integration provides:

- A clean comment box for readers
- Emoji reactions
- Comment sorting options
- Privacy settings
- Subscribe functionality

## Key Learnings

Working with Claude, I learned:

- How Jekyll's templating system connects different components
- The importance of proper file structure in GitHub Pages
- How to integrate third-party services with a static site
- The role of front matter in controlling post features

Some troubleshooting tips I discovered:

- Always verify your Disqus shortname in `_config.yml`
- Double-check that `comments: true` is in your post's front matter
- Ensure all files are in their correct directories
- Be patient while GitHub Pages rebuilds your site

I'm looking forward to seeing how this new feature helps foster discussion and community around my blog posts. Feel free to test it out by leaving a comment below!
