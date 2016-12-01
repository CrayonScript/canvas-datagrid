Canvas Data Grid
================

Lightweight canvas based data grid.

[Demo](https://tonygermaneri.github.io/canvas-datagrid/sample/index.html)

Instantiation
=============

Works with require framework or without.
If used without require, `canvasDataGrid` is declared in the global scope.

Simple creation and data set.

    var grid = canvasDataGrid({
        parentNode: document.getElementById('blockElement'),
        data: data
    });


Attributes
==========

Attributes can be set during instantiation or after
by accessing `grid.attributes`.  If changed after instantiation some attributes
require calling `grid.draw()` to see changes.

showNewRow: true
----------------
When true, a row will appear at the bottom of the data set.  `schema[].defaultValue`
will define a default value for each cell.  `defaultValue` can be a `string` or a
`function`.  When a function is used, the arguments `header` and `index` will be passed
to the function.  The value returned by the function will be the value in the new cell.

saveAppearance: true 
----------------
When true, and the attribute `name` is set, column and row sizes will be saved to the
browser's localStore.

selectionFollowsActiveCell: false 
----------------
When true, moving the active cell with keystrokes will also change the selection.

multiLine: true 
----------------
When true, edit cells will be textareas, when false edit cells will be inputs.

editable: true 
----------------
When true cells can be edited.  When false, grid is read only to the user.

allowColumnReordering: true 
----------------
When true columns can be reordered.  NOT IMPLEMENTED.

showFilter: true 
----------------
When true, filter will be an option in the context menu.

pageUpDownOverlap: 1 
----------------
Amount of rows to overlap when pageup/pagedown is used.

persistantSelectionMode: false 
----------------
When true, selections will behave as if the command/control key is held down at all times.

rowSelectionMode: false 
----------------
When true, clicking on any cell will select the entire row that cell belongs to.

autoResizeColumns: false 
----------------
When true, all columns will be automatically resized to fit the data in them.
Warning!  Expensive for large (>10k ~2 seconds) datasets.

allowRowHeaderResize: true 
----------------
When true, the user can resize the width of the row headers.

allowColumnResize: true 
----------------
When true, the user can resize the width of the columns.

allowRowResize: true 
----------------
When true, the user can resize the row headers increasing the height of the row.


allowRowResizeFromCell: false 
----------------
When true, the user can resize the height of the row from the border of the cell.

allowColumnResizeFromCell: false 
----------------
When true, the user can resize the width of the column from the border of the cell.

showPerformance: false 
----------------
When true, the amount of time taken to draw the grid is shown.

borderResizeZone: 10 
----------------
Number of pixels in total around a border that count as resize zones.

showHeaders: true 
----------------
When true, headers are shown.

showRowNumbers: true 
----------------
When true, row numbers are shown in the row headers.

showRowHeaders: true 
----------------
When true, row headers are shown.



Properties
==========

changes
-------
Array of changes and additions made to the grid since last time data was loaded.
The data property will change with changes as well, but this is a convince list of all the
changes in one spot.  Calling `clearChangeLog` will clear this list.

input
-----
Reference to the the edit cell when editing.  Undefined when not editing.

controlInput
------------
Input used for key controls on the grid.  Any clicks on the grid will cause
this input to be focused.  This input is hidden behind the canvas.

currentCell
-------------
Convenience object that represents the object that the mouse moved over last.

height
------
Height of the grid.

width
-----
Width of the grid.

visibleCells
------------
Array of cell drawn.

visibleRows
-----------
Array of visible row indexes.

selections
----------
Matrix array of selected cells.

selectionBounds
---------------
`rect` object, bounds of current selection.

attributes
----------
Object that contains the properties listed in the attributes section.

sizes
-----
Mutable object that contains `sizes.columns` and `sizes.rows` arrays.
These arrays control the sizes of the columns and rows.
If there is not an entry for the row or column index it will fall back to
the style default.

style
-----
Object that contains the properties listed in the style section.

resizeMode
----------
Represents the currently displayed resize cursor.  Can be `ns-resize`, `ew-resize`, `pointer`, or `inherit`.

cellFormaters
-------------
Object that contains a list of formatters for displaying text.
The properties in this object match the `schema[].type` property.
For example, if the schema for a given column was of the type `date`
the grid would look for a formatter called `cellFormatters.date`
if a formatter cannot be found for a given data type a warning will
be logged and the string formatter will be used.

Cell formatter function should contain the following arguments.

    cellFormatters.date = function (ctx, cell) { return new Date(cell.value).toISOString(); }

| Argument | Description |
|-----|------|
| ctx | Canvas context. |
| cell | Current cell. |

Formatters must return a string value to be displayed in the cell.

filters
-------
Object that contains a list of filters for filtering data.
The properties in this object match the `schema[].type` property.
For example, if the schema for a given column was of the type `date`
the grid would look for a filter called `filters.date`
if a filter cannot be found for a given data type a warning will
be logged and the string/RegExp filter will be used.

    filters.number = function (value, filterFor) {
        if (!filterFor) { return true; }
        return value === filterFor;
    };

| Argument | Description |
|-----|------|
| value | Value of the data. |
| filterFor | Value to filter for. |

data
----
This is how data is set in the grid.
Data must be an array of objects that conform to a schema.

    [
        {col1: 'row 1 column 1', col2: 'row 1 column 2', col3: 'row 1 column 3'},
        {col1: 'row 2 column 1', col2: 'row 2 column 2', col3: 'row 2 column 3'}
    ]

schema
------
Schema is optional.  Schema is an array of column objects.
If no schema is provided one will be generated from the
data, in that case all data will be assumed to be string data.

Each column object can have the following properties:

| Property | Description |
|-----|------|
| name | The name of the column.  This is used to match with data and is the only required property. |
| type | The data type of this column |
| title | What will be displayed to the user.  If not present, name will be used. |
| width | The default width in pixels of this column.|
| hidden | When true the column will be hidden. |
| filter | The filter function to use to filter this column.  If no function is provided, type will determine filer. |
| formatter | The formatter function used display this column.  If no function is provided, type will determine formatter.|
| defaultValue | The default value of this column for new rows.  This is a function that takes the arguments `header` and `index` and must return a value for new default cells in this column.|

    [
        {
            name: 'col1'
        },
        {
            name: 'col2'
        },
        {
            name: 'col3'
        }
    ]

Methods
==========

beginEditAt(x, y)
-----------------
Begins editing at cell x, row y

endEdit(abort)
--------------
Ends editing, optionally aborting the edit.

clearChangeLog()
----------------
Clears the change log `grid.changes` that keeps track of changes to the data set.
This does not undo changes or alter `grid.data` it is simply a convince array to
keep track of changes made to the data.

setActiveCell(x, y)
-------------------
Sets the active cell.  Requires redrawing.

scrollIntoView(x, y)
--------------------
Scrolls the cell at cell x, row y into view if it is not already.

gotoToCell(x, y)
------------------
Scrolls to the cell at cell x, row y.

gotoToRow(y)
------------
Scrolls to the row y.

findColumnScrollLeft(columnIndex)
---------------------------------
Returns the number of pixels to scroll to the left to line up with column `columnIndex`.

findRowScrollTop(rowIndex)
--------------------------
Returns the number of pixels to scroll down to line up with row `rowIndex`.

getSelectionBounds()
--------------------
Gets the bounds of current selection.  Returns a `rect` object.

fitColumnToValues(name)
-----------------------
Resizes a column to fit the longest value in the column.  Call without a value to resize all columns.
Warning, can be slow on very large record sets (100k records ~3-5 seconds on an i7).

getHeaderByName(name)
---------------------
Returns a header from the schema by name.

findColumnMaxTextLength(name)
-----------------------------
Returns the maximum text width for a given column by column name.

disposeContextMenu()
--------------------
Removes the context menu if it is present.

getCellAt(x, y)
-----------------
Gets the cell at grid pixel coordinate x and y.

order(columnName, direction)
----------------------------
Sets the order of the data.

draw()
------
Redraws the grid.  No matter what the change, this is the only method required to refresh everything.

selectArea()
------------
Accepts a `rect` object that defines the desired selected area.

getSchemaFromData()
-------------------
Returns schema with auto generated schema based on data structure.

setFilterValue(column, value)
----------------------------
Sets the value of the filter.

Events
======

selectionchanged
----------------
Fires when the selection changes.

    grid.addEventListener('selectionchanged', function (data, matrix, bounds) { });

| Argument | Description |
|-----|------|
| data | Selected data. |
| matrix | Selections object.  2D matrix of selections. |
| bounds | `Rectangle` object describing the selection bounds. |

beforerendercell
----------------
Fired just before a cell is drawn onto the canvas.  `e.preventDefault();` prevents the cell from being drawn.
You would only use this if you want to completely stop the cell from being drawn and generally muck up everything.

    grid.addEventListener('beforerendercell', function (ctx, value, row, header, x, y) { });

| Argument | Description |
|-----|------|
| ctx | Canvas context. |
| value | Current cell value. |
| row | Current row data. |
| header | Current header object. |
| x | The current cells x coordinate. |
| y | The current cells y coordinate. |

rendercell
----------
Fires when a cell is drawn.  If you want to change colors, sizes, add content, etc. this is the event to attach to.
Altering what is drawn by changing the cell object's height and width is allowed.  Drawing on the canvas is allowed.
Altering the context of the canvas is allowed.

    grid.addEventListener('rendercell', function (ctx, cell) { });

| Argument | Description |
|-----|------|
| ctx | Canvas context. |
| cell | Current cell. |

rendertext
----------
Fires when text is drawn into a cell.  If you want to change the color of the text, this is the event to attach to.
To alter what text finally appears in the cell, change the value of `cell.formattedValue`.  Keep in mind this
text will still be subject to the ellipsis function that truncates text when the width is too long for the cell.

You cannot alter the cell's height or width from this event, use `rendercell` event instead.

    grid.addEventListener('rendertext', function (ctx, cell) { });

| Argument | Description |
|-----|------|
| ctx | Canvas context. |
| cell | Current cell. |


renderorderbyarrow
------------------
Fires when the order by arrow is drawn onto the canvas.  This is the only way
to completely replace the order arrow graphic.  Call `e.preventDefault()` to stop the default arrow from being drawn.

    grid.addEventListener('renderorderbyarrow', function (ctx, cell) { });

| Argument | Description |
|-----|------|
| ctx | Canvas context. |
| cell | Current cell. |


mousemove
---------
Fires when the mouse moves over the grid.

    grid.addEventListener('mousemove', function (e, cell) { });

| Argument | Description |
|-----|------|
| 0 | Mouse event. |
| 1 | Cell under mouse. |


contextmenu
-----------
Fires when a context menu is requested.  The menu item array can be altered to change what items appear on the menu.

    grid.addEventListener('contextmenu', function (e, cell, menuItems, contextMenu) { });

| Argument | Description |
|-----|------|
| 0 | Mouse event. |
| 1 | Cell under mouse. |
| 2 | Mutable list of menu items. |
| 3 | Context menu HTML element. |

You can add items to the context menu but they must conform to this object type.

| Property | Description |
|-----|------|
| title | The title that will appear in the context menu.  If title is a `string` then the string will appear.  If title is a `HTMLElement` then it will be appended via `appendChild()` to the context menu row maintaining any events and references. |
| onclick | Optional function to invoke when this context menu item is clicked.  Neglecting to call `e.stopPropagation();` in your function will result in the mouse event bubbling up to the canvas undesirably.|

Removing all items from the list of menu items will cause the context menu to not appear.
Calling `e.preventDefault();` will cause the context menu to not appear as well.

beforeendedit
-------------
Fires just before edit is complete giving you a chance to validate the input.
`e.preventDefault();` will cause the edit to not end and row data will not be written back to the `data` array.

    grid.addEventListener('beforeendedit', function (value, originalValue, abort, cell, textarea) { });

| Argument | Description |
|-----|------|
| value | New value. |
| originalValue | Original value. |
| abort | Abort edit function.  Call this function to abort the edit. |
| cell | Cell object. |
| textarea | Textarea or input HTMLElement depending on `attributes.multiLine`. |


endedit
-------
Fires when the edit has ended.  This event gives you a chance to abort the edit
preserving original row data, or modify the value of the row data prior to being written.

    grid.addEventListener('endedit', function (value, abort, cell, textarea) { });

| Argument | Description |
|-----|------|
| value | New value. |
| abort | When true, the edit was aborted. |
| cell | Cell object. |
| textarea | Textarea HTMLElement. |



beforebeginedit
---------------
Fires before a edit cell has been created giving you a chance to abort it.
`e.preventDefault();` will abort the edit cell from being created.

    grid.addEventListener('beforebeginedit', function (cell) { });

| Argument | Description |
|-----|------|
| cell | Cell object. |


beginedit
---------
Fires when an editor textarea (or input) has been created.

    grid.addEventListener('beginedit', function (cell, textarea) { });

| Argument | Description |
|-----|------|
| cell | Cell object. |
| textarea | Textarea HTMLElement. |


click
-----
Fires when the grid is clicked.

    grid.addEventListener('click', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Mouse event. |
| cell | Cell object. |


resizecolumn
------------
Fires when a column is about to be resized.
`e.preventDefault();` will abort the resize.

    grid.addEventListener('resizecolumn', function (x, y, cell) { });

| Argument | Description |
|-----|------|
| x | x pixel position of the resize. |
| y | y pixel position of the resize. |
| cell | The mutable cell to be resized. |


mousedown
---------
Fires when the mouse button is pressed down on the grid.
`e.preventDefault();` will abort the default grid event.

    grid.addEventListener('mousedown', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Mouse event. |
| cell | Cell object. |

mouseup
-------
Fires when the mouse button is pressed down on the grid.
`e.preventDefault();` will abort the default grid event.

    grid.addEventListener('mouseup', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Mouse event. |
| cell | Cell object. |

dblclick
--------
Fires when the mouse button is double clicked on the grid.
`e.preventDefault();` will abort the default grid event.
Note that this will necessarily require 2*`mousedown`, 2*`mouseup` and 2*`click` events to fire prior to the double click.

    grid.addEventListener('dblclick', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Mouse event. |
| cell | Cell object. |


keydown
-------
Fires when the keyboard button is pressed down on the grid.
`e.preventDefault();` will abort the default grid event.

    grid.addEventListener('keydown', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Key event. |
| cell | Cell object. |


keyup
-----
Fires when the keyboard button is released on the grid.
`e.preventDefault();` will abort the default grid event.

    grid.addEventListener('keyup', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Key event. |
| cell | Cell object. |



keypress
--------
Fires when the keyboard press is completed on the grid.
`e.preventDefault();` will abort the default grid event.

    grid.addEventListener('keypress', function (e, cell) { });

| Argument | Description |
|-----|------|
| e | Key event. |
| cell | Cell object. |



resize
------
Fires when grid is being resized.
`e.preventDefault();` will abort the resizing.

    grid.addEventListener('resize', function (e, cell) { });

| Argument | Description |
|-----|------|
| 0 | height. |
| 1 | width. |


Common Objects
==============

cell
----

A cell on the grid and all data associated with it.

| Property | Description |
|-----|------|
| type | Data type used by this cell as dictated by the column. |
| style | Visual style of cell.  Can be any one of `cell`, `activeCell`, `headerCell`, `cornerCell`, or `headerRowCell`.  Prefix of each style name. |
| x | The x coordinate of this cell on the canvas. |
| y | The y coordinate of this cell on the canvas. |
| hovered | When true, this cell is hovered. |
| selected | When true, this cell is selected. |
| width | Width of the cell on the canvas. |
| height | Height of the cell on the canvas. |
| data | The row of data this cell belongs to. |
| header | The schema column this cell belongs to. |
| columnIndex | The column index of the cell. |
| rowIndex | The row index of the cell. |
| value | The value of the cell. |
| formattedValue | The value after passing through any formatters. |

rect
----

A selection area represented by a `rect`.
This object is returned by a number of events, methods and properties, and is passed to the `selectArea` method.

| Property | Description |
|-----|------|
| top | First row index. |
| bottom | Last row index. |
| left | First column index. |
| right | Last column index. |


Styles
==========

| Property | Default Value |
|-----|------|
| filterTextPrefix | (filtered) |
| editCellFontSize | 16px |
| editCellFontFamily | sans-serif |
| editCellPaddingLeft | 4.5 |
| contextMenuStyleSheet | false |
| contextMenuItemMargin | 2px |
| contextMenuItemBorderRadius | 3px |
| contextMenuLabelDisplay | inline-block |
| contextMenuLabelMinWidth | 75px |
| contextMenuHoverBackground | rgba(182, 205, 250, 1) |
| contextMenuColor | rgba(43, 48, 43, 1) |
| contextMenuHoverColor | rgba(43, 48, 153, 1) |
| contextMenuFontSize | 16px |
| contextMenuFontFamily | sans-serif |
| contextMenuBackground | rgba(222, 227, 233, 0.94) |
| contextMenuBorder | solid 1px rgba(158, 163, 169, 1) |
| contextMenuPadding | 2px |
| contextMenuBorderRadius | 3px |
| contextMenuOpacity | 0.98 |
| contextMenuFilterInvalidExpresion | rgba(237, 155, 156, 1) |
| contextMenuMarginTop | 0 |
| contextMenuMarginLeft | 5 |
| autosizePadding | 5 |
| minHeight | 250 |
| minRowHeight | 10 |
| minColumnWidth | 10 |
| columnWidth | 250 |
| backgroundColor | rgba(240, 240, 240, 1) |
| headerOrderByArrowHeight | 8 |
| headerOrderByArrowWidth | 13 |
| headerOrderByArrowColor | rgba(185, 185, 185, 1) |
| headerOrderByArrowBorderColor | rgba(195, 199, 202, 1) |
| headerOrderByArrowBorderWidth | 1 |
| headerOrderByArrowMarginRight | 5 |
| headerOrderByArrowMarginLeft | 0 |
| headerOrderByArrowMarginTop | 6 |
| cellHeight | 24 |
| cellFont | 16px sans-serif |
| cellPaddingTop | 5 |
| cellPaddingLeft | 5 |
| cellPaddingRight | 7 |
| cellAlignment | left |
| cellColor | rgba(0, 0, 0, 1) |
| cellBackgroundColor | rgba(240, 240, 240, 1) |
| cellHoverColor | rgba(0, 0, 0, 1) |
| cellHoverBackgroundColor | rgba(240, 240, 240, 1) |
| cellSelectedColor | rgba(43, 48, 153, 1) |
| cellSelectedBackgroundColor | rgba(182, 205, 250, 1) |
| cellBorderWidth | 0.5 |
| cellBorderColor | rgba(195, 199, 202, 1) |
| activeCellFont | 16px sans-serif |
| activeCellPaddingTop | 5 |
| activeCellPaddingLeft | 5 |
| activeCellPaddingRight | 7 |
| activeCellAlignment | left |
| activeCellColor | rgba(43, 48, 153, 1) |
| activeCellBackgroundColor | rgba(111, 160, 255, 1) |
| activeCellHoverColor | rgba(43, 48, 153, 1) |
| activeCellHoverBackgroundColor | rgba(110, 168, 255, 1) |
| activeCellSelectedColor | rgba(43, 48, 153, 1) |
| activeCellSelectedBackgroundColor | rgba(110, 168, 255, 1) |
| activeCellBorderWidth | 0.5 |
| activeCellBorderColor | rgba(151, 173, 190, 1) |
| headerCellPaddingTop | 5 |
| headerCellPaddingLeft | 5 |
| headerCellPaddingRight | 7 |
| headerCellHeight | 25 |
| headerCellBorderWidth | 0.5 |
| headerCellBorderColor | rgba(172, 175, 179, 1) |
| headerCellFont | 16px sans-serif |
| headerCellColor | rgba(50, 50, 50, 1) |
| headerCellBackgroundColor | rgba(222, 227, 233, 1) |
| headerCellHoverColor | rgba(43, 48, 153, 1) |
| headerCellHoverBackgroundColor | rgba(181, 201, 223, 1) |
| headerRowWidth | 57 |
| headerRowCellPaddingTop | 5 |
| headerRowCellPaddingLeft | 5 |
| headerRowCellPaddingRight | 7 |
| headerRowCellHeight | 25 |
| headerRowCellBorderWidth | 0.5 |
| headerRowCellBorderColor | rgba(172, 175, 179, 1) |
| headerRowCellFont | 16px sans-serif |
| headerRowCellColor | rgba(50, 50, 50, 1) |
| headerRowCellBackgroundColor | rgba(222, 227, 233, 1) |
| headerRowCellHoverColor | rgba(43, 48, 153, 1) |
| headerRowCellHoverBackgroundColor | rgba(181, 201, 223, 1) |
| headerRowCellSelectedColor | rgba(43, 48, 153, 1) |
| headerRowCellSelectedBackgroundColor', 'rgba(182, 205, 250, 1)' |