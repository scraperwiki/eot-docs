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
Return cells with a value of _string_

.filter(...) string, function, hamcrest(?)



.assert_one()
.shift(...)
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

