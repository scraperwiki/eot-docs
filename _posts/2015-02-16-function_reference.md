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




### Bags

.filter(...)
.assert_one()
.shift(...)
.fill(...)
.excel_ref(...)
.is_number()  # is_not_number etc. not supported at moment
.is_date() # check property or not
.is_not_blank()
.is_not_italic()
.col()
.one_of()
.regex()
.value [singleton bag]

others, not yet used.
