---
layout: page
title: "Function Reference"
category: doc
date: 2001-01-01 12:00:00
order: 2
---

## Learning from examples

Examples of recipes can be found at http://github.com/scraperwiki/eot-recipes

### Core 

bag.dimension(label, ?, ?)     # dynamic

table.dimension(label, value)  # static

return value ... obs

per_file per_tab

# Per file hints

UP/ABOVE/DOWN/LEFT etc.

OBS/DATAMARKER etc.

### Bags

##### bag.filter(_string_)
Return only the cells in the bag with a value of _string_. Typically there's only one, so this is often followed by `.assert_one()`. See later for other options to pass to filter.

##### bag.one\_of(_list-of-filters_) [db]
Return cells in the bag for which any of the filters match. Filters are usually strings but could also be _cell-functions_ (see the backend of bag.filter, below)

##### bag.regex(_regular-expression_) [db]
Return cells whose values match the regular expression.

##### bag.assert_one()
Return the bag unchanged, so long as it contains exactly one cell.
Otherwise crash.

##### bag.filter(_cell-function_) [backend]
Return only the cells in the bag for which the _cell-function_ is true. These are typically written
```python
bag.filter(lambda cell: cell.value > 20 and cell.y < 40)
```
or

```python
def large_value_near_top(cell):
    return cell.value > 20 and cell.y < 40
    
bag.filter(large_value_near_top)
```

Optionally, if you install *hamcrest* (`pip install pyhamcrest`) you can use their matching functions on the cell value - see the [hamcrest documentation](http://pyhamcrest.readthedocs.org/en/latest/library/) for the available functions.

```python
bag.filter(hamcrest.equal_to("dog"))
```

Of specific interest is when the _cell-function_ is `re.compile(`_regular-expression_`)`: this is `True` when the regular expression matches the cell's value.  

##### bag.shift(_x_, _y_)
##### bag.shift(_direction_)

Return only cells which are exactly _x_ cells to the right, and _y_ cells down from a cell in the current bag. Either _x_ or _y_ can be negative to look left and up respectively.

There's no guarantee that you'll have the same number of cells afterwards; there might not be a cell at the new position.

You can also use any direction: `UP`, `DOWN`, `LEFT`, `RIGHT`, `ABOVE`, `BELOW`; or pass an `(`_x_`, `_y_`)` tuple.

##### bag.fill(_direction_)
##### bag.expand(_direction_)

From each of the cells in the bag, get all cells that are in the right _direction_. `fill` doesn't include the original cells; `expand` does.
Note that this might get a lot of blank cells: consider using `is_not_blank()`.

##### bag.is_number() [DB]
Return only the cells in the bag which have a numeric value. 

##### bag.is_date() [DB]
Return only the cells in the bag which are in one of the following string formats:
```
1999
1999 Q1
Jan 1999
Jan-Mar 1999
```
Note that currently this won't detect cells which are actually recognised by Excel as dates!

##### bag.is_XXX()
##### bag.is\_not\_XXX()
Return only the cells which are / are not a thing.

Options which make sense: `bold`, `italic`, `strikeout`, `underline`, `blank`, `any_border`, `all_border`, `richtext`.
(is any border lined? are all four borders lined? is this cell richtext?)

##### bag.XXX\_is(_value_)
##### bag.XXX\_is\_not(_value_)
Return only the cells for which the property has / doesn't have a particular value.

Options which make sense: `size` (in points), `font_name` (a string).

`bag.font_name_is_not("Comic Sans MS")`

##### bag.value
Returns the value of a single cell.

##### bag.parent()
For any spanned cells, get the top-left cell (which contains all the info)
Non-spanned cells are passed unchanged.

##### table.debug_dimensions() [db]
Adds debug dimensions - giving the table name and excel cell reference - to the output CSV.

##### bag.group(_regular-expression_) [db]
For a single cell, get the matching group from the regular expression.
e.g. if the regular expression was `-(.*)-` and the cell's value was `cat-dog-fish`; this would return `dog`.

##### bag.waffle(_other-bag_)
Get all cells which have a cell from one bag above them, and the other bag to the side. Note that the two bags are interchangable without changing the output. You can change the direction from its default (DOWN) by specifying `direction=LEFT` or similar.

##### bag.extrude(_x_, _y_)
Get this cell, and all cells within _x_ squares right of it, and _y_ squares below it. _x_ and _y_ can be negative for left and up respectively.

##### bag.same\_row(_other-bag_)
##### bag.same\_col(_other-bag_)
Get cells in this bag which are in the same row/column as a cell in the second.

##### bag.table
The table which these cells are from.

### Table only

##### table.excel\_ref(_ref_) [db]
Get cells from the table via an excel cell referencce: e.g. "G4", "C", "9".


### Shallow magic

These are reasonably detailed internals, but might be useful to know.

##### bag.select(_function_)
Get cells from the same table as these cells, based on a function.

The function must take two arguments: a bag cell, and a table cell.

If the function is `True`, the table cell will be in the output.

##### bag.select_other(lambda bag_cell, other_cell: foo, other_bag)
As `bag.select` except it inspects another bag rather than the bag's table.

This is useful because you can return cells from only a subset of the table, or use a different table entirely.

##### bag.junction(otherbag)
Similar to `bag.waffle`, except it outputs a list of the form
```python
[
    [bag_cell, otherbag_cell, cell_at_junction],
    [bag_cell, otherbag_cell, cell_at_junction],
    ...
]
```

### Other things that might be useful, maybe.

scan.py looks for duplicated sets of dimension values

bag.pprint, .aslist and .excel_locations might be useful ways of displaying the data when debugging.
