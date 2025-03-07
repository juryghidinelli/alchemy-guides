---
prev: /elements.html
next: /render_images.html
---

# Essences

## Overview

Essences store the actual content of your site. They are stored as instances of `Alchemy::Content` on each `Alchemy::Element` instance.

Alchemy comes with a lot of predefined essences for the regular needs of a website project. Combine them like a chemestry kit into [elements](/elements.html).

Essences are Rails models. It is pretty easy to [add your own essence class](/create_essences.html) as well.

## Definition

Essences are defined as `contents` on an [element definition](/elements.html#defining-elements).

### Global content settings

When defining contents, you need to provide a `name` and essence `type`. You can set hints and default values as well.

~~~ yaml
# config/alchemy/elements.yml
- name: article
  contents:
    - name: headline
      type: EssenceText
      hint: This is the headline
    - name: copy
      type: EssenceRichtext
      default: Lorem ipsum dolor
      as_element_title: true
~~~

* **name** `String` _required_

  A lowercased **unique** (per element) name of the content. Separated words needs to be underscored.

* **type** `String` _required_

  An essence type this content is from. Alchemy has lots of built in essences for [simple text](#essencetext), [rich text](#essencerichtext), [images](#essencepicture), [booleans](#essenceboolean) and more.

* **hint** `String|Symbol|Boolean`

  A hint for the user in the admin frontend that describes what the essence is used for. The hint is translatable if you provide an I18n translation `Symbol` instead of a `String`. You may also set it to `true` to default to the I18n key `alchemy.content_hints.your-content-name`.

* **default** `String`

  The default text to prefill newly created elements. You may also use a symbol to set it to the I18n key `alchemy.default_content_texts.your-symbol-name`

* **as_element_title** `Boolean`

  For the displayed element title, the first content essence is used. Use this setting to override this behaviour and show other content as element title.

### Individual essence settings

Each essence type can have its own type of settings.

To configure these settings you have to pass them into its `settings` key in the `elements.yml`.

~~~ yaml {5-6}
- name: my_element
  contents:
    - name: headline
      type: EssenceText
      settings:
        linkable: true
~~~

## EssenceText

Stores plain text of 255 chars max.

Use this for a headline, or a product name. The editor is renderd as a single lined input field. The view output will be sanitized and HTML escaped.

### Settings


* **linkable** `Boolean`

  If set to `true`, the user can add a link to the text.

### Example

~~~ yaml
- name: button
  type: EssenceText
  settings:
    linkable: true
~~~

## EssenceRichtext

Used to store paragraphs of formatted text.

The editor is rendered as a textarea with embedded Tinymce Editor.

### Settings

You can customize the Tinymce editor of a single element instance.

* **tinymce** `Hash`

### Example

~~~ yaml
- name: text
  type: EssenceRichtext
  settings:
    tinymce:
      style_formats:
        - title: 'Subheadline'
          block: 'h3'
~~~

::: tip INFO
See the [Tinymce customization guide](customize_tinymce.html) for all available options
:::

## EssencePicture

Store references to pictures the editor assigns from the library.

The editor partial is rendered as a picture editor with a lot of options (i.e. image cropper).

The view partial renders the assigned picture, resizes it and crops it if needed.

::: tip INFO
See the [rendering images](render_images.html) guide for further information on the powerful image rendering engine of Alchemy.
:::

## EssenceDate

Use this to store a `DateTime` value. Renders a datepicker in the editor partial.

The view output is passed through Rails' I18n Library, so it is fully localizable.

## EssenceHtml

Useful to store HTML code (i.e. a embed, or tracking code).

::: warning
The view renders the raw, not sanitized or escaped output.
**So be careful!**
:::

## EssenceBoolean

Stores a `Boolean` value in the database. Renders a checkbox in the editor partial.

## EssenceSelect

Renders a select box in the editor partial and stores the value as `String`.

Useful for letting your user select from a limited set of choices.

* **select_values** `Array`

### Example

~~~ yaml
- name: width
  type: EssenceSelect
  settings:
    select_values: ['200', '300', '400']
~~~

## EssenceLink

Stores a url in the database. Useful for linking things, where the editor should not set the linked text itself.

::: tip
If you want that the linked text is editable by the editor use the [**EssenceText**](#essencetext) with `linkable: true` option instead.
:::
