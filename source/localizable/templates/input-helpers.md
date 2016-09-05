The [`{{input}}`](http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_input)
and [`{{textarea}}`](http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_textarea)
helpers in Ember.js are the easiest way to create common form controls. The
`{{input}}` helper wraps the built-in [Ember.TextField][1] and [Ember.Checkbox][2]
views, while `{{textarea}}` wraps [Ember.TextArea][3]. Using these helpers,
you can create these views with declarations almost identical
to how you'd create a traditional `<input>` or `<textarea>` element.

[1]: http://emberjs.com/api/classes/Ember.TextField.html
[2]: http://emberjs.com/api/classes/Ember.Checkbox.html
[3]: http://emberjs.com/api/classes/Ember.TextArea.html

## Text fields

```handlebars
{{input value="http://www.facebook.com"}}
```

Will become:

```html
<input type="text" value="http://www.facebook.com"/>
```

You can pass the following standard `<input>` attributes within the input
helper:

<table>
  <tr><td>`readonly`</td><td>`required`</td><td>`autofocus`</td></tr>
  <tr><td>`value`</td><td>`placeholder`</td><td>`disabled`</td></tr>
  <tr><td>`size`</td><td>`tabindex`</td><td>`maxlength`</td></tr>
  <tr><td>`name`</td><td>`min`</td><td>`max`</td></tr>
  <tr><td>`pattern`</td><td>`accept`</td><td>`autocomplete`</td></tr>
  <tr><td>`autosave`</td><td>`formaction`</td><td>`formenctype`</td></tr>
  <tr><td>`formmethod`</td><td>`formnovalidate`</td><td>`formtarget`</td></tr>
  <tr><td>`height`</td><td>`inputmode`</td><td>`multiple`</td></tr>
  <tr><td>`step`</td><td>`width`</td><td>`form`</td></tr>
  <tr><td>`selectionDirection`</td><td>`spellcheck`</td><td>`type`</td></tr>
</table>

If these attributes are set to a quoted string, their values will be set
directly on the element, as in the previous example. However, when left
unquoted, these values will be bound to a property on the template's current
rendering context. For example:

```handlebars
{{input type="text" value=firstName disabled=entryNotAllowed size="50"}}
```

Will bind the `disabled` attribute to the value of `entryNotAllowed` in the
current context.

## Actions

To dispatch an action on specific events, such as `enter` or `key-press`, use the following

```handlebars
{{input value=firstName key-press="updateFirstName"}}
```

[Event Names](http://emberjs.com/api/classes/Ember.View.html#toc_event-names) must be dasherized.

## Checkboxes

You can also use the
[`{{input}}`](http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_input)
helper to create a checkbox by setting its `type`:

```handlebars
{{input type="checkbox" name="isAdmin" checked=isAdmin}}
```

Checkboxes support the following properties:

* `checked`
* `disabled`
* `tabindex`
* `indeterminate`
* `name`
* `autofocus`
* `form`


Which can be bound or set as described in the previous section.

## Text Areas

```handlebars
{{textarea value=name cols="80" rows="6"}}
```

Will bind the value of the text area to `name` on the current context.

[`{{textarea}}`][1] supports binding and/or setting the following properties:

[1]: http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_textarea

* `value`
* `name`
* `rows`
* `cols`
* `placeholder`
* `disabled`
* `maxlength`
* `tabindex`
* `selectionEnd`
* `selectionStart`
* `selectionDirection`
* `wrap`
* `readonly`
* `autofocus`
* `form`
* `spellcheck`
* `required`

### Binding dynamic attribute

You might need to bind a property dynamically to an input if you're building a flexible form, for example. To achieve this you need to use the [`{{get}}`](http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_get) and [`{{mut}}`](http://emberjs.com/api/classes/Ember.Templates.helpers.html#method_mut) in conjunction like shown in the following example:

```handlebars
{{input value=(mut (get person field))}}
```

The `{{get}}` helper allows you to dynamically specify which property to bind, while the `{{mut}}` helper allows the binding to be updated from the input. See the respective helper documentation for more detail.
