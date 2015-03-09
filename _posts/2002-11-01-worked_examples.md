---
layout: page
title: "Worked examples"
category: doc
date: 2002-11-01 12:00:00
order: 2
---

## Explaining the recipe a02.py
 
The spreadsheet we're going to extract data from is `a02.xls`. It's
available [here](https://github.com/scraperwiki/eot-recipes) together
with the recipe `a02.py` which we're going to walk through
step-by-step in this tutorial.

We want to extract all the observations from the tabs, *except* the combined
"all aged 16 & over" data and the change data.

We can run this by placing `a02.py` in the same directory as `a02.xls` and
running `bake a02.py a02.xls` while in that directory at the Windows
command line.

** Image: spreadsheet with regions that we want selected highlighted **

### Importing constants

```python
from databaker.constants import *
```

This line makes it possible to use any of the following in the recipe:

* dimensions (`DATAMARKER`, `GEOG`, `OBS`, `TIME`, `TIMEUNIT`);
* directions (`ABOVE`, `BELOW`, `LEFT`, `RIGHT`);
* selection specifiers (`CLOSEST`, `DIRECTLY`).

Without them, the recipe will fail with a "`NameError`" as the recipe
doesn't know how these special constants are defined.

### `per_file`

```python
def per_file(tableset):
    return "*"
```
 
This selects all tabs from the spreadsheet, so we will be extracting
data from both the `seasonally adjusted` and the `not seasonally
adjusted` tabs.

In this case, the layouts of the tabs is similar enough that we can use
this recipe for all the tabs.

### `per_tab`

```python
def per_tab(tab):
    obs = tab.filter("MGSL").assert_one().shift(DOWN).fill(RIGHT).fill(DOWN).is_number().is_not_italic()
    tab.col('A').one_of(['Male', 'Female', 'All Persons']).dimension('gender', CLOSEST, ABOVE)
    tab.col('A').is_date().dimension(TIME, DIRECTLY, LEFT)
    tab.regex("All aged .*").dimension('ages', CLOSEST, UP)
    tab.filter("Total economically active").fill(LEFT).fill(RIGHT).is_not_blank().dimension('indicator', DIRECTLY, ABOVE)
    tab.dimension('adjusted_yn', tab.name)
    return obs
```

For each tab we've selected, `per_tab` does the following things.

#### Selecting cells

##### Line 1

`tab.filter("MGSL")`
 
This only returns cells that match the text "MGSL". In this
spreadsheet, there's only one match at cell B7, so this filter returns
this cell.
 
We can confirm this by then doing `.assert_one()`. Why do this if we
already know there's only one cell that matches? It means that we can
reuse this recipe on new version of the spreadsheets but have it warn us
if it finds multiple entries - or none at all. It's possible that
whoever creates the next version of the spreadsheet might merge the two
tabs into one. `assert_one()` would warn us of this.
 
From cell B7, `shift(DOWN)` now gives us the one cell down from the one that
contained MGSL, so we've now selected cell B8.
 
`fill(RIGHT)` and `fill(DOWN)` then select all cells to the right and down of
cell B8, so we skip that first header row and the first column.
 
*Image showing current selection*
 
So, we've now selected the cells that are to the right and down of B7
which contain the data we want. But, we're only interested in the
observations, so we don't want to extract the four character codes or
the change values.

The character codes are easily distinguished from the observations as
they are text, not numbers. And the change values are in italics.

So, we can use `is_number()` and `is_not_italic()` to get only the
observation cells.

These selected cells are stored in a variable called `obs`.

#### Specifying the dimensions

For each observation, we want to add certain dimensions. The following
lines specify which dimensions we will retrieve for each observation.

`tab.col('A').one_of(['Male', 'Female', 'All Persons']).dimension('gender', CLOSEST, ABOVE)`
`tab.col('A').is_date().dimension(TIME, DIRECTLY, LEFT)`
`tab.regex("All aged .*").dimension('ages', CLOSEST, UP)`
`tab.filter("Total economically active").fill(LEFT).fill(RIGHT).is_not_blank().dimension('indicator', DIRECTLY, ABOVE)`
 
These all add dimensions to be retrieved for each cell in the selected
data, `obs` but use different approaches to locate the cell containing
the dimension information.

##### Line 2 

The `tab.col('A')` in L2 (and L3) selects cells in column A of the
tab.

Next, of the cells in column A, `one_of` selects those that match the
text `Male`, `Female` or `All Persons`.

`.dimension('gender', CLOSEST, ABOVE)` specifies what we want to call
the dimension in the output, along with how we get to the gender cell that
corresponds to an observation cell.

We look for the `CLOSEST` cell of this selection that's above the row
where the observation lies.

##### Line 3

First, we do the same as in L2, selecting all cells in column A.

This time we're looking for cells containing a date, which we select
using `is_date()`.

`.dimension(TIME, DIRECTLY, LEFT)`

We then specify that this corresponds to the special TIME dimension and
that this data lies `DIRECTLY` to the left of the current cell.

##### Line 4

`tab.regex("All aged .\*")` uses a regular expression to find cells
anywhere in the tab that contain `All aged ` (notice the space) followed
by any text.

The `.*` means match any character (represented by `.`) and we've specified
using the `*` that we don't mind how many characters follow `All aged `.

This way we match the cells in the spreadsheet that
contain `All aged 16 & over` and `All aged 16 to 64`.

`.dimension('ages', CLOSEST, UP)` labels this dimension as `ages` and
specifies that we want the closest cell above. So we're again finding
the closest cell to each one in our selection.

(UP and ABOVE mean the same thing.)

##### Line 5

`tab.filter("Total economically active").fill(LEFT).fill(RIGHT).is_not_blank().dimension('indicator', DIRECTLY, ABOVE)`

Here we first find cells that contain exactly `Total economically
active`. For all of the cells in this selection, we then select all cells
to the left and right of these with `.fill(LEFT)` and `.fill(RIGHT)`.

`is_not_blank()` removes any empty cells. After `.fill()` we may have
unwanted blank cells that we're not interested in. In this case, there
are empty cells between the indicator names that are part of our current
selection that we don't need.

`.dimension('indicator', DIRECTLY, ABOVE) labels the cell in this
selection directly above each cell of interest as `indicator`.

##### Line 6

`tab.dimension('adjusted_yn', tab.name)`

This line explicitly sets a dimension item to be the name of the
current tab, and doesn't actually consider any of the cells in the
spreadsheet.

This only works for text which doesn't depend on the
particular observation - so typically things which are the same
for all observations on the tab.
 
Adds a dimension with the label
`adjusted_yn` and an item that's set to the spreadsheet tab's name,
e.g. `seasonally adjusted` or `not seasonally adjusted`.

#### Extracting the data with the dimensions

##### Line 7

`return obs` then tells `databaker` that it's the end of the recipe
and we've finished selecting observations and dimensions. The output
data will be generated containing the observations and dimensions
we've chosen in the recipe.
