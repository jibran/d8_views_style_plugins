# Drupal <img src="images/d8-logo.png" alt="Drupal 8 logo" width="100" style="background:none; border:none; margin: 0; box-shadow: none">

## VIEWS STYLE PLUGINS

Jibran Ijaz

<jibran.ijaz@previousnext.com.au>

[@JibranIjaz](https://twitter.com/JibranIjaz)

Note:

- I am drupal core contributer.
- I have more then 150 core commit mentions in Drupal 8.
- I am maintainer of shortcuts modules in Drupal 8
- I am a senior Drupal developer at PreviousNext.
- I am very active in views issue queue in Drupal 8.
- I maintain various Drupal 8 and Drupal 7 views modules including views field view.

---

## What is the views module?

- Query Part<!-- .element: class="fragment" data-fragment-index="1" -->
<!-- .element: class="fragment" data-fragment-index="1" -->
- Output Part<!-- .element: class="fragment" data-fragment-index="2" -->
<!-- .element: class="fragment" data-fragment-index="2" -->

Note:
- Views is a query building tool which display the result of that query in a proper format.
- Query part, it filters, sorts and used to add relationships with other entities or table.
- Output part: it consists of header, footer, pager, fields or formatted html generated using query.

---

## Output Part

- Display<!-- .element: class="fragment" data-fragment-index="1" -->
- Style<!-- .element: class="fragment" data-fragment-index="2" -->

Note:
Let's talk more about output part.
- In views you can choose how you want to display your data you can select either page, block, attachment for other views displays, attachment to entities like entity views attach or even a panel pane.
- After choosing the display you have to choose how you want to style the result of the query, for that the views module uses style plugins

---

## Views style plugin in D7

- hook_views_api<!-- .element: class="fragment" data-fragment-index="1" -->
- $module.views.inc<!-- .element: class="fragment" data-fragment-index="2" -->
- hook_views_plugins<!-- .element: class="fragment" data-fragment-index="3" -->
- views_plugin_style_mystyle.inc<!-- .element: class="fragment" data-fragment-index="4" -->
- Oh! <!-- .element: class="fragment" data-fragment-index="5" -->
  - you have to add views_plugin_style_mystyle.inc<!-- .element: class="fragment" data-fragment-index="5" -->
 to info file for auto loading and rebuild the registry.<!-- .element: class="fragment" data-fragment-index="5" -->

---

## Views style plugin in D8

- Create a class <!-- .element: class="fragment" data-fragment-index="1" -->
```
// src/Plugin/views/style/MyStyle.php
```
- Annotation <!-- .element: class="fragment" data-fragment-index="2" -->
```php
/**
 * @ViewsStyle(
 * )
 */
```

---

## Markup of views style plugin

- Use plugin class to define style options.<!-- .element: class="fragment" data-fragment-index="1" -->
- Use theme to style the rows.<!-- .element: class="fragment" data-fragment-index="2" -->

---

## Drupal 7 plugin definition

```php
/**
 * Implements hook_views_plugins().
 */
function views_vertical_tabs_views_plugins() {
  $plugins = array();

  $plugins['style']['vertical_tabs'] = array(
    'title' => t('Vertical Tabs'),
    'help' => t('Renders the full views output in a vertical tabs'),
    'handler' => 'views_plugin_style_vertical_tabs',
    'theme' => 'views_view_vertical_tabs',
    'uses row plugin' => TRUE,
    'uses fields' => TRUE,
    'uses row class' => TRUE,
    'uses grouping' => FALSE,
    'uses options' => TRUE,
    'type' => 'normal',
  );

  return $plugins;
}
```

---

## Drupal 8 plugin definition

```php
/**
 * Defines a style plugin that renders the full view as vertical_tabs.
 *
 * @ingroup views_style_plugins
 *
 * @ViewsStyle(
 *   id = "vertical_tabs",
 *   title = @Translation("Vertical Tabs"),
 *   help = @Translation("Displays rows as Vertical tabs."),
 *   theme = "views_view_vertical_tabs",
 *   display_types = {"normal"}
 * )
 */
```

---

## Drupal 7 styling plugin class

```php
class views_plugin_style_vertical_tabs extends views_plugin_style_default { }
```

---

## Drupal 8 styling plugin class

```php
class VerticalTabsStyle extends StylePluginBase {

  /**
   * Does the style plugin allows to use style plugins.
   */
  protected $usesRowPlugin = TRUE;

  /**
   * Does the style plugin support custom css class for the rows.
   */
  protected $usesRowClass = TRUE;

  /**
   * Does the style plugin support grouping of rows.
   */
  protected $usesGrouping = FALSE;

  /**
   * Does the style plugin for itself support to add fields to it's output.
   */
  protected $usesFields = TRUE;

}
```

---

## Drupal 7 styling plugin methods in class

```php

  /**
   * Overrides views_plugin_style::options_definition().
   */
  function option_definition() { }

  /**
   * Overrides views_plugin_style::options_form().
   */
  function options_form(&$form, &$form_state) { }

```

---

## Drupal 8 styling plugin methods in class

```php
  /**
   * {@inheritdoc}
   */
  protected function defineOptions() { }

  /**
   * {@inheritdoc}
   */
  public function buildOptionsForm(&$form, FormStateInterface $form_state) { }
```

---

## How to theme views style plugin?

- In D7 we define the theme function in hook_views_plugins. <!-- .element: class="fragment" data-fragment-index="1" -->
```php
    'theme' => 'views_view_vertical_tabs',
```
- In D8 we define the theme function in Annotation. <!-- .element: class="fragment" data-fragment-index="2" -->
```php
/*
...
 *   theme = "views_view_vertical_tabs",
...
 */
```
- template_preprocess_THEME_NAME() is still the same.<!-- .element: class="fragment" data-fragment-index="3" -->
- But php template engine is replaced by twig template engine.<!-- .element: class="fragment" data-fragment-index="4" -->
    - views-view-vertical-tabs.tpl.php is now views-view-vertical-tabs.html.twig<!-- .element: class="fragment" data-fragment-index="5" -->

---

## Preprocess of views_view_vertical_tabs
- Process views style options.
- Process views rows.
- Create renderable array of vertical_tabs.
- Add proper JS.


Note:

- vertical_tabs is a fapi element so we have to convert it to proper fapi element.

---

## views-view-vertical-tabs.tpl.php in D7
```php
<?php

/**
 * @file
 * Template to display a views output in vertical_tabs.
 *
 * @ingroup views_templates
 */
?>
<?php print render($vertical_tabs); ?>

```

Note:

- @todo

---

## views-view-vertical-tabs.html.twig in D7
```twig
{#
/**
 * @file
 * Template to display a views output in vertical_tabs.
 *
 * Available variables:
 * - vertical_tabs: Render array of all the vertical_tabs.
 *
 * @see template_preprocess_views_view_vertical_tabs()
 *
 * @ingroup views_templates
 */
#}
{{ vertical_tabs }}


```

Note:

- @todo

---

## Views vertical tabs style plugin in action
1. After downloading and enabling the <a href="https://www.drupal.org/project/views_vertical_tabs" target="_blank">module</a>.
2. Select 'Vertical Tabs' option form wizard screen or form views style form.
<img src="" />
<img src="" />
3. Select styling options.
4. Configure view.
Note:

- @todo

---

## Thanks and credits
- @dawehner
- @larowlan
- @previousnext
- @
Note:

- @todo better title and labels

---

# Questions?

---

## Thank you!

<jibran.ijaz@previousnext.com.au>

[@JibranIjaz](https://twitter.com/JibranIjaz)
