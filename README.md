# ember-light-table-cell-type-multi-value

![Ember Version](https://embadge.io/v1/badge.svg?start=2.4.0)
[![Build Status](https://travis-ci.org/buschtoens/ember-light-table-cell-type-multi-value.svg)](https://travis-ci.org/buschtoens/ember-light-table-cell-type-multi-value)
[![npm version](https://badge.fury.io/js/ember-light-table-cell-type-multi-value.svg)](http://badge.fury.io/js/ember-light-table-cell-type-multi-value)
[![Download Total](https://img.shields.io/npm/dt/ember-light-table-cell-type-multi-value.svg)](http://badge.fury.io/js/ember-light-table-cell-type-multi-value)
[![Ember Observer Score](https://emberobserver.com/badges/ember-light-table-cell-type-multi-value.svg)](https://emberobserver.com/addons/ember-light-table-cell-type-multi-value)
[![Greenkeeper badge](https://badges.greenkeeper.io/buschtoens/ember-light-table-cell-type-multi-value.svg)](https://greenkeeper.io/)
[![dependencies Status](https://david-dm.org/buschtoens/ember-light-table-cell-type-multi-value/status.svg)](https://david-dm.org/buschtoens/ember-light-table-cell-type-multi-value)
[![devDependencies Status](https://david-dm.org/buschtoens/ember-light-table-cell-type-multi-value/dev-status.svg)](https://david-dm.org/buschtoens/ember-light-table-cell-type-multi-value?type=dev)

> Pass an array of values to your cell  

This is an addon for [**ember-light-table**][elt]. It adds the
[cell type][cellType] `multi-value`, which enables you to specify an array of `valuePaths` instead of a single [`valuePath`][valuePath].

[elt]: https://github.com/offirgolan/ember-light-table
[cellType]: https://offirgolan.github.io/ember-light-table/docs/classes/Column.html#property_cellType
[valuePath]: https://offirgolan.github.io/ember-light-table/docs/classes/Column.html#property_valuePath

## Installation

```
ember install ember-light-table-cell-type-multi-value
```

## Usage

In your column definition, just replace `valuePath` with `valuePaths`, which can
now be an array of paths. The `value` property passed to your cell will now be
an array of values. Simple as that! :wink:

For maximized backwards compatibility, `valuePaths` may also be a string. In
that case, you'll get a single `value`, not an array. If no `valuePaths` were
specified, `multi-value` falls back to the default behavior of the `base` cell
and attempts to read `valuePath`.

*However*, `valuePath` *must* be a `String`. This means no fancy multiple
values in that case. This limitation and the need to use `valuePaths` instead of
`valuePath` in the first place, stem from the fact that ember-light-table uses
the `{{get}}` helper like so: [`{{get row valuePath}}`](https://github.com/offirgolan/ember-light-table/blob/71b76d2e5c5e737dd8072787d1484155db2883f7/addon/templates/components/lt-row.hbs#L4)

We might change that in the future.

### Example

```js
function format(value) {
  return JSON.stringify(value);
}

export default Component.extends({
  columns: computed(function() {
    return [{
      label: 'First Name',
      valuePath: 'firstName', // fallback to `valuePath`
      cellType: 'multi-value',
      format
    }, {
      label: 'Last Name',
      valuePaths: 'lastName', // singular string is also accepted
      cellType: 'multi-value',
      format
    }, {
      label: 'Favorite colors',
      valuePaths: ['firstColor', 'secondColor', 'thirdColor']
      cellType: 'multi-value',
      format
    }, {
      label: 'Youngest Siblings',
      valuePaths: ['siblings.youngestSister', 'siblings.youngestBrother']
      cellType: 'multi-value',
      format
    }];
  }),

  rows: computed(function() {
    return [{
      firstName: 'Jan',
      lastName:  'Buschtöns',

      firstColor:  'pink',
      secondColor: 'green',
      thirdColor:  'blue',

      siblings: {
        youngestBrother: 'Robin'
      }
    }];
  }),

  init() {
    this._super(...arguments);

    const columns = get(this, 'columns');
    const rows = get(this, 'rows');
    set(this, 'table', new Table(columns, rows));
  }
});
```

```html
<tr>
  <td>"Jan"</td>
  <td>"Buschtöns"</td>
  <td>["pink", "green", "blue"]</td>
  <td>[null, "Robin"]</td>
</tr>
```
