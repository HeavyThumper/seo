# SEO: A plugin for Pelican :fr:

[![Build Status](https://img.shields.io/github/workflow/status/pelican-plugins/seo/build)](https://github.com/pelican-plugins/seo/actions)

This plugin helps you improve your Pelican site SEO (Search Engine Optimization) to reach the top positions on search engines. To see what can do SEO for you, go directly to [Usage](#usage) section.

| Author          | GitHub                            |
| :-------------: | :-------------------------------: |
| Maëva Brunelles | https://github.com/MaevaBrunelles |

## Why do you need SEO

If :
* you need some help to improve the SEO of your Pelican site
* you are not familiar with SEO
* you want a simple site with all SEO basis

This plugin is for you !

SEO comes with two complete features to help you :
* [SEO Report](#seo-report) : generation of an HTML report based on articles analysis. Provides you what is good, what should be improved and what is problematic. Useful if you want to improve the referencement of your articles.
* [SEO Enhancer](#seo-enhancer) : generation of robots indexation hints, HTML tag and structured data. Usefull if you want to control the appareance of your site in the search engine.

## Installation

This plugin can be installed via:

    pip install pelican-seo

## Requirements

SEO needs Beautiful Soup 4 :

```
pip install beautifulsoup4
```

`SITEURL` Pelican parameter must be defined as features are based on it.

## Usage

You can choose which feature to enable or disable in the plugin settings. Default is `True` for both.

```
# settings.py
SEO_REPORT = True # To enable this feature
SEO_ENHANCER = False # To disable this feature
```

SEO runs when you [generate your site](https://docs.getpelican.com/en/stable/quickstart.html#generate-your-site). If you want to see SEO logs, you should use `--verbose` Pelican argument:
```
$ pelican content --verbose
-> SEO plugin initialized
-> SEO plugin - SEO Report: seo_report.html file created
-> SEO plugin - SEO Enhancement: robots.txt file created
-> Writing /output/my-first-review.html
-> SEO plugin - SEO Enhancement: Done for /output/my-first-review.html
Done: Processed 1 articles, 0 drafts, 0 pages, 0 hidden pages and 0 draft pages in 0.17 seconds.
```

### SEO report

SEO analyzes all your articles and pages, and generate an HTML SEO report in your Pelican root project : `seo-report.html`

Example :

![SEO report example](docs/seo-report-example.png)

You can set up a limit for article and page analysis in the plugin settings.py. By default, it's set up to 10 articles and 10 pages.

```
ARTICLES_LIMIT = 10
PAGES_LIMIT = 10
```

The analysis works from the most recents articles or pages to the olders according to the `date` metadata.
Analysis is focused on :
* Page title `<title></title>`
* Page description `<meta name="description" content="" />`
* Title content `<h1></h1>`
* Internal link `<a href="SITEURL/..."></a>`

Which are declared by article and page metadata and content :

```markdown
# article.md
Title: Title page
Description: Description page

# Title content
[Internal link](https://example.com/about.html)
```

Theses elements form the basis of page SEO optimization. Have a look to the report, optimize your articles and pages according to analysis, and restart the process to see if all elements are now in green.

### SEO Enhancer

SEO generates for you :
* HTML enhancements
* Structured data
* Robots file

#### Robots.txt file

Indicates to search engines which pages they are allowed to access to or not. By default, all pages are authorized to be explored by all the existing robots.

```
User-agent: *
```

To disallow the exploration or to forbid the indexation of a specific ressource, add theses metadata :

```
Disallow: True
Noindex: True
```

A `robots.txt` file is added at the website root, in the `OUTPUT_PATH` setting (Pelican's default is `output/`).

```
# robots.txt
User-agent: *

Disallow: example.html
Noindex: other-example.html
```

#### Canonical URL tag

SEO automatically adds for each article the canonical URL tag in the `<head>` to avoid duplicate content.

```html
<link rel="canonical" href="<SITEURL>/example.html" />
```

#### Structured data

SEO automatically adds structured data in the `<head>` to improve the display of result snippet in search engines. Articles will have both article schema and breadcrumb schema, while pages will only have breadcrumb schema.
Structured data are based on [Schema.org](https://schema.org/) vocabulary, with `JSON-LD` encoding. Note that schemas generated by default are Schema.org compliant, but not automatically Google compliant. Additionnal metadata are required for it.

##### Breadcrumb schema

Based on [BreadcrumbList schema](https://schema.org/BreadcrumbList) :

```
{
    "@context": "https://schema.org",
    "@type": "BreadcrumbList",
    "itemListElement": [
        {
            "@type": "ListItem",
            "position": :n=1:,
            "name": :Sitename:,
            "item": :SITEURL:
        },
        {
            "@type": "ListItem",
            "position": :n+1:,
            "name": :name:,
            "item": :url:
        },
        {
            "@type": "ListItem",
            "position": :n+x:,
            "name": :name:,
            "item": :url:
        }
    ]
}
```

Each element of the file path has his `ListItem`, even folders, so it's better to create a user friendly page for those (otherwise you'll get your server's default page).

##### Article schema

Based on [Article schema](https://schema.org/Article) :

```
{
    "@context": "https://schema.org",
    "@type": "Article",
    "author": {
        "@type": "Person",
        "name": :author:
    },
    "publisher": {
        "@type": "Organization",
        "name": :sitename:,
        "logo": {
            "@type": "ImageObject",
            "url": :logo:
        }
    },
    "headline": :title:,
    "about": :category:,
    "datePublished": :publication_date:,
    "image": :image:
}
```

`:logo:` and `:image:` fields are not required by Schema.org but they are by Google.

To fill `:logo:`, add a parameter in the `pelicanconf.py` :

```
# pelicanconf.py
LOGO = 'https://www.example.com/logo.jpg'
```

To fill `:image:`, add a metadata for each Markdown or reST article :

```
# article.md
Image: https://www.example.com/article-image.jpg

# article.rst
:image: https://www.example.com/article-image.jpg
```

## Contributing

Contributions are welcome and much appreciated. Every little bit helps. You can contribute by improving the documentation, adding missing features, and fixing bugs. You can also help out by reviewing and commenting on [existing issues][].

To start contributing to this plugin, review the [Contributing to Pelican][] documentation, beginning with the **Contributing Code** section.

[existing issues]: https://github.com/pelican-plugins/seo/issues
[Contributing to Pelican]: https://docs.getpelican.com/en/latest/contribute.html
