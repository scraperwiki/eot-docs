---
layout: page
title: "Function Reference"
category: doc
date: 2015-02-16 15:36:00
order: 2
---

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
Return only the cells in the bag with a value of _string_. Typically there's only one, so this is often followed by `.assert_one()`

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

Optionally, if you install _hamcrest_ (`pip install pyhamcrest`) you can use their matching functions on the cell value.

```python
bag.filter(hamcrest.equal_to("dog"))
```

##### bag.shift(_x_, _y_)
##### bag.shift(_direction_)

Return only cells which are exactly _x_ cells to the right, and _y_ cells down from a cell in the current bag. _X_ and _y_ can be negative to look left and up.

There's no guarantee that you'll have the same number of cells afterwards; there might not be a cell at the new position.

You can also use any direction: `UP`, `DOWN`, `LEFT`, `RIGHT`, `ABOVE`, `BELOW`; or pass an `(`_x_`, `_y_`)` tuple.

.fill(...)
.expand(...) x.fill | x
.is_number()  db # is_not_number etc. not supported at moment
.is_date() db
.is_/is_not_/_is(...)/_is_not(...)
.is_not_italic()
.one_of() db
.regex() db
.value [singleton bag]

others, not yet used.
.debug_dimensions() db
.group(regex) db
.waffle(other)
.extrude(x, y)
.same_row, .same_col

bag.table

### Table only

.excel_ref(...) db
.col()


### existance of scan

.pprint(?) -- do we care
.aslist(?) -- do we care
.filter_one(func) -- do we care?
.excel_locations(?) -- do we care?


### Shallow magic
bag.table
bag.select(lambda bag_cell, table_cell)
bag.select_other(lambda bag_cell, other_cell: foo, other_bag)
bag.junction(otherbag)


-- document as xypath

