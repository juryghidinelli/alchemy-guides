---
prev: /about.html
next: /cells.html
---

# Pages

## Overview

In contrary to most CMSs pages in Alchemy **do not hold the actual content**. The actual content is stored in [essences](essences.html) inside of [elements](/elements.html) pages are built of.

Pages have a unique friendly url and are organized as nested tree and represent the structure of your website.

Beside a language, a page has attributes for name, title, visibility, published and restriction status and all SEO relevant attributes like meta tags and meta descriptions.

Every page has a [layout](#defining-page-layouts) which defines additional properties like caching, uniqueness and it defines [elements](elements.html) and [cells](cells.html) that can be placed on that Page.

## Defining page layouts

Page layouts are defined in the `config/alchemy/page_layouts.yml` file.

Every page layout needs at least a name. You don't need to set every option. It depends on what you need for pages with that layout type.

### Recommended settings

* **name** `String` *required*

  The name of the layout used for views and inside the database. You can render a layout with the `render_page_layout(name)` helper.

* **elements** `Array`

  A list of element names that can be placed on this layout i.e. `[text, picture]`.

  [Elements](elements.html) are defined inside the `elements.yml` file.

* **autogenerate** `Array`

  A list of element names that are autogenerated after creating a Page of that type.

* **unique** `Boolean` (Default `false`)

  Pass `true` and the user can only choose this layout once inside a language tree.

* **cache** `Boolean` (Default `true`)

  Pass `false` to disable the cache headers for this kind of pages. **Recommended for contact forms** and such likes.

* **layoutpage** `Boolean` (Default `false`)

  Layout pages (or [global pages](#global-pages)) are outside the normal page tree and can be used to place "global" Elements like a header and footer.

* **taggable** `Boolean` (Default `false`)

  Pass `true` to be able to assign tags within page settings.

### Optional settings

* **hide** `Boolean` (Default `false`)

  Pass `true` to hide this layout from the user.

* **searchresults** `Boolean` (Default `false`)

  Pass `true` to use this type of page for rendering the search results of the build in fulltext search.

* **feed** `Boolean` (Default `false`)

  Pass `true` to enable a RSS feed of news elements from this page.

* **redirects_to_external** `Boolean` (Default `false`)

  Pass `true` to disable normal page rendering and redirect to a external page instead.

* **controller** `String`

  Controller to use instead of the default `Alchemy::PagesController`

* **action** `String`

  Controllers action to use instead of the default `Alchemy::PagesController#show`

### Example

Lets say you want to create a contact page with a headline element, a contactform and a text element on it.

This page should be unique, because you don't want to give your content manager the possibility to create more than one contact form.

This page must not be cached, because of validation messages and user specific form content.

We also want to autogenerate the headline and the contactform element after the page gets created.

~~~ yaml
# config/alchemy/page_layouts.yml
- name: contact
  cache: false
  unique: true
  elements: [headline, contactform, text]
  autogenerate: [headline, contactform]
~~~

::: warning NOTE
Please ensure to restart the Rails server after editing `page_layouts.yml`.
:::

## Page templates

Each page type (called `page_layout` in Alchemy) has a Rails view partial which is yielded on the Rails application layout (`app/views/layouts/application.html.erb`).

All page layout partials live in the `app/views/alchemy/page_layouts` folder.

They are named after the pages `page_layout` you defined in the `page_layouts.yml` file.

::: warning NOTE
If no page layout partial is found for a page, the `_standard.html.erb` layout gets rendered instead.
:::

### Template generator

There is no need to create these partials manually. AlchemyCMS comes with a Rails generator task which creates these partials for you.

So after defining the page layouts, you can generate all the corresponding partials for them.

~~~ bash
bin/rails g alchemy:page_layouts --skip
~~~

Using the example above, which defines a contact layout, the generator will create a partial named `_contact.html.erb`.

::: tip
You can pass `--template-engine` or `-e` as option to use one of `haml`, `slim` and `erb`. The default depends on your default template engine in your Rails host app.
:::

### Rendering elements

Alchemy does not place any HTML markup in your generated page layouts partial.

So:

~~~ erb
<%= render_elements %>
~~~

is all you will see. Feel free to customize the HTML so it fits your needs.

::: tip
The `render_elements` view helper has lots of options. Please have a look at the [`Alchemy::ElementsHelper` documentation](https://www.rubydoc.info/github/AlchemyCMS/alchemy_cms/Alchemy/ElementsHelper#render_elements-instance_method)
:::

## Global pages

Global pages (or layout pages) are pages that are not in the default page tree (your navigation). They will never get rendered on its own. Use them to store shared elements that should be rendered on multiple other pages (ie. footer, header, tracking codes etc) or somewhere directly on the application layout.

To define a global page set `layoutpage: true` in the page layout definition of that page.

### Render an element from a global page

To render an element from a global page use the `from_page` option of the `render_elements` helper.

#### Example

~~~ erb
<%= render_elements only: 'news_teaser', from_page: 'sidebar' %>
~~~

::: tip
You can pass a `page_layout` name as a `String`, an `Array` of page layout names, or an instance of a certain `Alchemy::Page`.
:::

## Caching

If using the global caching option (defined in `config/alchemy/config.yml`) - which is enabled by default - your page requests will deliver cache headers. Most browsers, CDNs and proxys use these headers to cache the page.

::: tip
You can disable cache headers for certain pages by using the `cache: false` setting.
:::

### Russian doll caching

You can use Rails' "Russian-Doll-Caching" to cache page templates.

```erb
<% cache @page do %>
  <%= render_elements %>
<% end %>
```

::: warning
Be sure to not cache page templates that have elements with forms on it, like contact or comment forms. Rails' csrf protection token is placed inside the `<form`> tag and caching it will break form submissions.
:::

## Translate page layout names

Page layout names are passed through the `I18n` library.
You can translate them in your Alchemy locale files.

### Example

~~~ yaml
# config/locales/alchemy.de.yml
de:
  alchemy:
    page_layout_names:
      contact: Kontakt
      search: Suche
~~~

::: tip
All translation keys used by Alchemy are scoped under the `alchemy` namespace.
:::
