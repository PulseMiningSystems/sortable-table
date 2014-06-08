&lt;sortable-table&gt;
================

Polymer Web Component that generates a sortable &lt;table&gt; from JSON.

Maintained by [Steven Skelton](https://github.com/stevenrskelton)

[Additional Documentation on Table Sorting](http://stevenskelton.ca/sortable-table-with-polymer-web-components/)

[Additional Documentation on Templates](http://stevenskelton.ca/advanced-uses-polymer-templates/)

## Live Demos
 
> [Auto-Generated Columns](http://files.stevenskelton.ca/sortable-table/examples/autogenerated-columns.html)

> [Header, and Footer Templates](http://files.stevenskelton.ca/sortable-table/examples/table-templates.html)

> [Row Templates](http://files.stevenskelton.ca/sortable-table/examples/row-templates.html)

> [Cell Templates](http://files.stevenskelton.ca/sortable-table/examples/cell-templates.html)

> [Dynamically Changing Columns and Templates](http://files.stevenskelton.ca/sortable-table/examples/dynamic-columns.html)

> [Larger Datasets](http://files.stevenskelton.ca/sortable-table/examples/large-dataset.html)

> [Nesting Tables in Cells](http://files.stevenskelton.ca/sortable-table/examples/nested-tables.html)

> [Paging, Top-N Rows](http://files.stevenskelton.ca/sortable-table/examples/paging.html)

> [Refreshing Data](http://files.stevenskelton.ca/sortable-table/examples/refreshing-data.html)

> [Row Editor](http://files.stevenskelton.ca/sortable-table/examples/row-editor.html)

> [Selected Rows, Multi-Select](http://files.stevenskelton.ca/sortable-table/examples/selected-rows.html)

> [Themes](http://files.stevenskelton.ca/sortable-table/examples/themes.html)

## Usage

1. Add the library using the Javascript package manager [Bower](http://bower.io/):

	```bower install --save sortable-table```

2. Import Web Components' polyfill:

	```html
	<script src="bower_components/platform/platform.js"></script>
	```

3. Import Custom Element:

	```html
	<link rel="import" href="bower_components/sortable-table/sortable-table.html">
	```

4. Start using it!

	```html
	<sortable-table>
		<!-- add templates here -->
	</sortable-table>
	```

## Options

Attribute				| Options		| Default									| Description
---						| ---			| ---										| ---
`data`	 				| *array*		| `[]`										| Data rows
`columns`				| *array*		| `[]`										| Columns to display, with options. If [], columns will be computed from `data`
`sortColumn`			| *string*		| `null`									| Current sorted `column.name`
`sortDescending`		| *boolean*		| `false`									| Current sorted column sort direction
`checkbox`				| *boolean*		| `false`									| Renders a checkbox column as first column, allowing selection of elements.
`rowSelection`			| *boolean*		| `false`									| Enable user interactive row selection
`multiSelect`			| *boolean*		| `false`									| Multiple rows can be selected
`selected`				| *object*		| `null`									| Element of `data` (`!multiSelect`)
`selected`				| *array*		| `[]`										| Elements of `data` (`multiSelect`)
`selectedRowStyle`		| *string*		| `background-color:` `rgba(0,96,200,0.2);`	| CSS style to apply to `selected` row
`cellTemplate`   		| *string*		| `null`									| Renderer for entire `<td></td>` cell. Access to `{{column}}`, cell `{{value}}` and original `{{row}}` object from `data`.  Will be overwritten if specified in `columns`.
`headerTemplate`		| *string*		| `null`									| Renderer for entire `<th></th>` cell. Access to `{{column}}`.  Will be overwritten if specified in `columns`.
`rowTemplate`			| *string*		| `null`									| Renderer for contents of `<tr></tr>` row. Access to row's data fields are through `{{row.fields.****.value}}`, where `***` are the column names.
`rowEditorTemplate`		| *string*		| `null`									| Renderer for contents of `<tr></tr>` row when in edit mode (_double clicked_). Access to row's data fields are through `{{row.fields.****.value}}`, where `***` are the column names.
`pageSize`				| *int*			| `-1`										| Maximum number of records to display, `-1` is all records.
`page`					| *int*			| `1`										| Number of pages to skip, `pageSize * (page-1)` records skipped.


### Data

Input format for `data` rows is an array of objects, where data for each column is a property of the row object.

### Columns

Attribute  			| Options		| Default		| Description
---					| ---			| ---			| ---
`name`	  			| *string*		| _required_	| Name of row property
`title`	  			| *string*	   	| `name`		| Text to display in column header
`formula`			| *function*	| `null`		| Single parameter `row`, return will override any value for property in `data`, as well as be used for sorting
`cellTemplate`   	| *string*		| `null`		| Renderer for entire `<td></td>` cell. Access to `{{column}}`, cell `{{value}}` and original `{{row}}` object from `data`
`headerTemplate`	| *string*		| `null`		| Renderer for entire `<th></th>` cell. Access to `{{column}}`
`footerTemplate`	| *string*		| `null`		| Renderer for entire `<td></td>` cell. Access to `{{column}}`, array of all `{{values}}` of cells in the column, and array of all original `{{rowValues}}` object from `data` _(if they are defined)_

### Templates

All templates must be nested inside the `<sortable-table>` tag to be accessible to the polymer element.

Any filter used (eg: `sum` in a following example) must be a member of `PolymerExpressions.prototype`.  See the [Polymer Filters](#polymer-filters) section for more details.

As always, only a very limited subset of Javascript is allowed within `{{ }}` expressions. See the [Polymer documentation](http://www.polymer-project.org/docs/polymer/expressions.html) on Expression syntax.


Example of a `headerTemplate` using images to indicate sorting:

```html
<template id="myHeaderTemplate">
	<th>
		{{!(column.title) ? column.name : column.title}}
		<img hidden?="{{!(sortColumn==column.name && sortDescending)}}" alt="up" />
		<img hidden?="{{!(sortColumn==column.name && !sortDescending)}}" alt="down" />
	</th>
</template>
```

Example of a `rowTemplate` that prints out column values directly from the raw `row` of the `data` array.
This is useful for rows that need to recalculate when values change:

```html
<template id="myRowTemplate_1">
	<td><input type="text" value="{{record.row.number}}"></td>
	<td>{{record.row.price}}</td>
	<td>{{record.row.number * record.row.price}}</td>
</template>
```

Example of a `rowTemplate` that prints out column values based on internally calculated field values.
The `field` names are the names of the table columns, and useful if there are `column` formulas being applied:

```html
<template id="myRowTemplate_2">
	<td>{{record.fields.alice.value}}</td>
	<td>{{record.fields.bill.value}}</td>
	<td>{{record.fields.casey.value}}</td>
</template>
```

Example of a `rowTemplate` that uses a template (and a filter `toKeyValueArray` that turns an object into an array):

```html
<template id="myRowTemplate_3">
	<template repeat="{{kv in record.fields | toKeyValueArray}}" bind>
		<td>{{kv.value.value}}</td>
	</template>
</template>
```

Example of a `cellTemplate` that displays an image beside the column value:

```html
<template id="myCellTemplate">
	<td>
		<img src="{{row.img}}" alt="{{row.title}}"/>{{value}}
	</td>
</template>
```
__Note:__  Normally `row[column.name] == value`, but `value` can be manually set by specifying a `formula`. This is useful if `value` won't sort correctly but you need access to the original value.

Example of a `footerTemplate` that computes the sum of a column using a filter named `sum`:

```html
<template id="sumTemplate">
	<td class="sortable-table-header" style="text-align:right">
		{{values | sum}}
	</td>
</template>
```

## Polymer Filters

Referencing the [polymer documentation](http://www.polymer-project.org/docs/polymer/expressions.html#filters), filters can be used in expressions to transform data:
```
{{ expression | filterName([optional parameters]}}
```
To use a filter in one of your _sortable-table_ templates, it must be registered into the _PolymerExpressions_ scope, which is accessible after polymer has loaded:
```javascript
window.addEventListener('polymer-ready', function(){
	PolymerExpressions.prototype.myFilter = function(){ ... }
});
```

If a filter is being used in a `rowTemplate` or `rowEditorTemplate` and references more than one field, it might be useful to pass the `row` as an argument:
```{{ record.row | myFilter }}```
however, this won't automatically observe the individual fields of the `row`.  To tell polymer to watch individual fields, they must all be sent as optional parameters:
```{{ record.row | myFilter(record.row.myField1, record.row.myField2) }}```

The added benefit is that the function can be reused in `rowTemplate`s and as a `column.formula`:
```javascript
function myFilter(row){
	return [your logic here];
}
...
PolymerExpressions.prototype.myFilter = myFilter
...
//in the columns array
{ name:'field1', formula: myFilter }
```

## Todo

- better support for internal row field change observers
- better CSS theming
- integration with IndexedDB
- ensure stable sort (orders can change within duplicates on sorted column)
- maybe: max and fixed table sizing / scrolling
- maybe: cell selection
- maybe: figure out how to sort by selected (click on header of checkbox column?)
- maybe: column grouping
- maybe: reload data if individual row fields change

## Bugs
- __Internet Explorer is completely broken__
- header templates in don't work with Native Support browsers
- header templates suffer slow performance with polyfil

## History

For detailed changelog, check [Releases](https://github.com/stevenrskelton/sortable-table/releases).

## License

[MIT License](http://opensource.org/licenses/MIT) © Steven Skelton