HTML Tables are simple to use for rendering small amounts of data. When we want to work with a lot of data and offer the user features like sorting, filtering and pagination, then we need to consider adding a JavaScript component.

In this post we will use the free community edition of [AG Grid JavaScript Data Grid](https://www.ag-grid.com/) to convert from a long static HTML table to an easy to use interactive Data Grid. The amount of JavaScript we need to do this is minimal and very simple.

We will build the example code in three steps:

- Render a static list of Todo Items data in an HTML Table.
- Load a list of Todo Items from a REST API and render in the table.
- Convert the HTML Table to a Data Grid to allow sorting, filtering and pagination.


## Rendering Data With HTML Tables

The first version of our application will allow us to create the basic page structure and make sure we are rendering the right data for the user.

I create a simple `index.html` file as shown below:

```html
<!DOCTYPE html>
<html>

<head>
    <title>Table Example</title>
</head>

<body>

    <style>
        table {
            border-collapse: collapse;
            width: 100%;
        }

        td,
        th {
            border: 1px solid #000000;
            text-align: left;
            padding: 8px;
        }
    </style>

    <h1>TODO List</h1>

    <div id="data-table">
        <table id="html-data-table">
            <tr>
                <th>userId</th>
                <th>id</th>
                <th>title</th>
                <th>completed</th>
            </tr>
            <tr>
                <td>1</td>
                <td>1</td>
                <td>My todo 1</td>
                <td>false</td>
            </tr>
        </table>    
    </div>

</body>

</html>
```

This will render a single Todo Item in a table:

|userId|id|title|completed|
|-----|-----|-----|-----|
|1|1|My todo 1|false|

Here is the example [Static HTML Table Page](https://eviltester.github.io/freecodecampexamples/html-table-to-data-grid/static-html-table.html)

Rendered in a browser the page would look like the image below.

The `table` is styled to have a width of 100% of the page using `width:100%` and the border lines between cells in the table have been styled to show as one line with `border-collapse: collapse`.

Without the `border-collapse` value the table would look like the image below:

## Benefits of short HTML Tables

HTML Tables are a very fast way to render small amounts of data in a tabular form on a page.

Tables require styling to look nicer because the default styling of a `table` varies between browsers and often is shown with no borders making the data hard to read.

At the moment our list of Todo Items is statically coded into the page. For the next step we will `fetch` the list from a REST API using JavaScript.


## Reading JSON from an API to render in HTML Table

Since we will be loading the data from an API, I won't hard code any data in the table. To support dynamic loading I simply remove the line of data from the `table` because I'm going to create the data rows using JavaScript:

```html
    <div id="data-table">
        <table id="html-data-table">
            <tr>
                <th>userId</th>
                <th>id</th>
                <th>title</th>
                <th>completed</th>
            </tr>
        </table>    
    </div>
```

I will add the JavaScript in the `index.html` page imediately before the terminating `body` tag.

```html
    <script type="text/javascript" charset="utf-8">
    </script>
</body>
```

To start, I will write the code that reads the data.

I will use the "{JSON} Placeholder" application for this demonstration.

By making a `GET` request on the URL [https://jsonplaceholder.typicode.com/todos](https://jsonplaceholder.typicode.com/todos) we will receive a JSON response which is a list of Todo Items.

You can try it for yourself without JavaScript by clicking on the link above.

The easiest way to make a `GET` request on the API is by using the `fetch` function built into JavaScript.

```html
    <script type="text/javascript" charset="utf-8">

        fetch('https://jsonplaceholder.typicode.com/todos')
            .then(function (response) {
                return response.json();
            }).then(function (apiJsonData) {
                console.log(apiJsonData);
            })

    </script>
</body>
```

To explain the above code, I'll describe it in sections below:

- Issue a GET request to `https://jsonplaceholder.typicode.com/todos`

```javascript
fetch('https://jsonplaceholder.typicode.com/todos')
```

- Then when the request is finished convert the response to a JavaScript Object, in our case this will be an array containing all the Todo Items.

```javascript
.then(function (response) {
                return response.json();
            })
```

- Then write the JavaScript object to the console

```javascript
.then(function (apiJsonData) {
                console.log(apiJsonData);
            })
```

With this code in our application, we won't see anything in the table, but we would see the array rendered in the Browser Dev Tools Console where we could view the data.

```shell
(200) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, …]
```

The API call returns 200 items, and each item is a Todo Object:

```json
  {
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": false
  }
```

Our next step is to render the data in the table:

```html
    <script type="text/javascript" charset="utf-8">

        fetch('https://jsonplaceholder.typicode.com/todos')
            .then(function (response) {
                return response.json();
            }).then(function (apiJsonData) {
                console.log(apiJsonData);
                renderDataInTheTable(apiJsonData);
            })

        function renderDataInTheTable(todos) {
            const mytable = document.getElementById("html-data-table");
            todos.forEach(todo => {
                let newRow = document.createElement("tr");
                Object.values(todo).forEach((value) => {
                    let cell = document.createElement("td");
                    cell.innerText = value;
                    newRow.appendChild(cell);
                })
                mytable.appendChild(newRow);
            });
        }
    </script>
</body>
```

The `renderDataInTheTable` function loops over all the Todo Items returned from the API call, finds the table in the DOM so that we can append new rows to it.

For each Todo Item the creates a new `tr` element, then adds each value in the Todo Item to the table as a `td` element.

When the above code is added to our application and the page loads we will see the HTML table now has all 200 Todo Items rendered in the table.

Here is the example [Dynamic HTML Table Page](https://eviltester.github.io/freecodecampexamples/html-table-to-data-grid/table-index.html).

## Benefits and Disadvantages of Long HTML Tables

HTML Tables are an easy way to render data on a page but are not very usable for long lists of data.

The data items can be hard to find, although the user can search the data by using the built in 'find in the page' functionality of the browser.

By rendering in an HTML table, our users have no way to sort the data, or filter it to show only completed Todo Items, so we would have to add extra code to our application to implement that functionality.

HTML Tables will automatically grow as more rows are added to the table, this can make them harder to use in an application when a lot of data has been added. When we add a lot of data we probably want to have pagination to restrict the table of data to show only a certain number of rows and allow the user to click through to the next page to see more items. This again is functionality we would have to write extra code to handle.

When our application gets to the point that we need more user interaction,, we should consider using a Data Grid component.

e.g. add extra functionality like:

- sorting
- filtering
- column resizing
- pagination

## Data Grid Components and Libraries

There are many free Data Grid Components available, most of them are framework specific so they require coding in either React, Angular or Vue.

I'm using AG Grid for this example because the free version can be used with JavaScript, TypeScript, React, Angular or Vue. The "AG" stands for Agnostic so can be used with any framework. When you learn to use it in one framework, the same API is available for other frameworks making your knowledge transferrable to other projects.

Also, the free version can be used in commercial applications so if you manage to expand the demo application shown here into a commercial Todo Management Application, you'll still be able to use AG Grid for free. Many commercial applications have been built using the free version of AG Grid. AG Grid is frequently sought as a skill in job applications so is worth experimenting with.

The commercial version of AG Grid has extra features like Excel Exporting and creating Graphs but we don't need any of that functionality in this demo.

Using a Data Grid means that we configure the Data Grid, and give it the data to render, and the Grid handles all the other functionality like sorting, filtering and pagination.

We can convert our existing code to use AG Grid with just a few changes.

## Adding AG Grid JavaScript and CSS

AG Grid is a library so we include the JavaScript required.

If you are using build tools like `npm` then various `npm install` commands are listed in the [Getting Started with AG Grid]() Documentation.

We are using plain JavaScript therefore we can include the `script` in our `head` section.

```html
<head>
    <title>Data Grid Example</title>
    <script src="https://unpkg.com/ag-grid-community/dist/ag-grid-community.min.noStyle.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community/dist/styles/ag-grid.css">
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community/dist/styles/ag-theme-balham.css">
</head>
```

This includes the community edition of AG Grid and the CSS required to render the Grid properly.

Our `data-table` `div` no longer needs to have any `table` element:

```html
    <div id="data-table" class="ag-theme-balham">
    </div>
```

AG Grid code will create the HTML for the Data Grid when we set it up, we add the `class` to use an [AG Grid theme](https://www.ag-grid.com/javascript-data-grid/themes/), in this example we are using the theme `ag-theme-balham`.

AG Grid requires setting a width and height for the `div`, I chose to add this as the `style` section in the code:

```html
    <style>
        #data-table {
            height: 500px;
            width: 100%;
        }
    </style>
```

The grid will be shown as 500 pixels high and fill `100%` width of the screen, this replicates the basic styling we had with the HTML table. But also shows one of the benefits of using a Data Grid. The size of the table rendered can be easily controlled and scroll bars will be added automatically as necessary by the Grid itself.

## Configuring AG Grid and Rendering Data

The `script` section of the code changes because we need to:

- configure the data grid
- create a new data grid using the configuration
- fetch the data and add it to the grid

I'll show the initial amended `script` section below and then explain it in the following paragraphs.

```html
    <script type="text/javascript" charset="utf-8">

        const columnDefs = [
            { field: 'userId' },
            { field: 'id' },
            { field: 'title' },
            { field: 'completed' },
        ];

        const gridOptions = {
            columnDefs: columnDefs,
            onGridReady: (event) =>{renderDataInTheTable(event.api)}
        };

        const eGridDiv = document.getElementById('data-table');
        new agGrid.Grid(eGridDiv, gridOptions);

        function renderDataInTheTable(api) {
            fetch('https://jsonplaceholder.typicode.com/todos')
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    api.setRowData(data);
                    api.sizeColumnsToFit();
                })
        }
    </script>
```

A Data Grid is data and configuration driven, we don't have to write much code in order to create a functional Data Grid.

First we create an array of Column Objects which define the columns in the Data Grid, these columns map on to the data.

The data that we are receiving from the API call has four properties "userId", "id", "title" and "completed":

```json
  {
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": false
  }
```
To render these in the Data Grid as columns we create an Object with a `field` property where the value is the name of the property in the Data Object.

```javascript
        const columnDefs = [
            { field: 'userId' },
            { field: 'id' },
            { field: 'title' },
            { field: 'completed' },
        ];
```

Next we create the `gridOptions` object. This configures the Data Grid:

```javascript
        const gridOptions = {
            columnDefs: columnDefs,
            onGridReady: (event) =>{renderDataInTheTable(event.api)}
        };
```

The `columnDefs` property is assigned the array of Column Objects that we defined earlier.

The `onGridReady` property is assigned a function which will call the `renderDataInTheTable` function when the grid has been created and rendered in the DOM i.e. when the grid is ready.


To add the grid to the page we find the `div` element which will contain the grid, then instantiate a new AG Grid object for that element and using hte options we configured:


```javascript
        const eGridDiv = document.getElementById('data-table');
        new agGrid.Grid(eGridDiv, gridOptions);
```

The function to fetch the data and render the data in the grid is much the same `fetch` code that we used for the dynamic HTML table, the difference is that the `renderDataInTheTable` function receives the AG Grid Api object as a parameter allowing us to call AG Grid functionality to set the row data and size the columns to fit the grid:

```javascript
        function renderDataInTheTable(api) {
            fetch('https://jsonplaceholder.typicode.com/todos')
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    api.setRowData(data);
                    api.sizeColumnsToFit();
                })
        }
```

When this code runs we will have basically replicated the same functionality of the dynamic HTML table, but now all the data is shown in a table with a scroll bar.

To receive the benefits of using a Data Grid and allow the user to sort, filter and navigate through the data easily we amend the configuration.

## Sorting, Filtering, Pagination

All we configured in the Data Grid so far was:

- which fields from the data to display
- what data to use

To add sorting, filtering, resizable columns and pagination we amend the `gridOptions` configuration:

```javascript
        const gridOptions = {

            defaultColDef: {
                sortable: true,
                filter: 'agTextColumnFilter',
                resizable: true
            },

            pagination: true,

            columnDefs: columnDefs,
            onGridReady: (event) =>{renderDataInTheTable(event.api)}
        };
```

We can configure columns in AG Grid individually by adding additional properties to the `columnDefs` objects, or if the same functionality is required by default in all columns we can configure the `defaultColDef`.

Here we configure it to be sortable, filterable and resizable:

```javascript
            defaultColDef: {
                sortable: true,
                filter: 'agTextColumnFilter',
                resizable: true
            },
```

The default filter we defined for all the columns is the text filter.

To add automatic pagination to the grid we add the `pagination: true` property and AG Grid will automatically paginate the data for us.

## User Friendly Data Grid

With the above code we have created a User Friendly Data Grid that dynamically fetches the data, adds it to a data grid which supports sorting, filtering and pagination.

Here is the example [Data Grid HTML Page](https://eviltester.github.io/freecodecampexamples/html-table-to-data-grid/datagrid-index.html).

```html
<!DOCTYPE html>
<html>

<head>
    <title>Data Grid Example</title>
    <script src="https://unpkg.com/ag-grid-community/dist/ag-grid-community.min.noStyle.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community/dist/styles/ag-grid.css">
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community/dist/styles/ag-theme-balham.css">
</head>

<body>
    <style>
        #data-table {
            height: 500px;
            width: 100%;
        }
    </style>

    <h1>TODO List</h1>

    <div id="data-table" class="ag-theme-balham">
    </div>

    <script type="text/javascript" charset="utf-8">

        const columnDefs = [
            { field: 'userId' },
            { field: 'id' },
            { field: 'title' },
            { field: 'completed' },
        ];

        const gridOptions = {

            defaultColDef: {
                sortable: true,
                filter: 'agTextColumnFilter',
                resizable: true
            },

            pagination: true,
            
            columnDefs: columnDefs,
            onGridReady: (event) =>{renderDataInTheTable(event.api)}
        };

        const eGridDiv = document.getElementById('data-table');

        new agGrid.Grid(eGridDiv, gridOptions);

        function renderDataInTheTable(api) {
            fetch('https://jsonplaceholder.typicode.com/todos')
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    api.setRowData(data);
                    api.sizeColumnsToFit();
                })
        }
    </script>
</body>
</html>
```



## Number Filters

Since the `userId` and `id` columns are numeric, we could make then use a number filter by amending the `columnDefs`


```javascript
        const columnDefs = [
            { field: 'userId', filter: 'agNumberColumnFilter'},
            { field: 'id', filter: 'agNumberColumnFilter'},
            { field: 'title' },
            { field: 'completed' },
        ];
```

Here is the example [Data Grid Number Filters HTML Page](https://eviltester.github.io/freecodecampexamples/html-table-to-data-grid/datagrid-number-filters-index.html).

There are a lot of [configuration options for columns](https://www.ag-grid.com/javascript-data-grid/column-properties/) listed in the AG Grid Documentation, for example configuring the width, style and making the cells editable.

## Benefits of a Data Grid

For many web sites a simple HTML table will be perfectly sensible way to render tabular data. It's fast and easy to understand, with a little CSS the table can look good for your users.

When your pages become more complex, rendering more data, or requiring more interactivity for the user then it starts to make more sense to use a Data Grid component or library.

Data Grid's provide much of the functionality your users need, without having to write a lot of code. In the example presented we moved from a dynamic table which read the data from an API to a Data Grid reading from an API with sorting, filtering, pagination and column resizing but our html code was the same length and the JavaScript we wrote was less complicated because the Data Grid did all the work of rendering the data.

Data Grid's can handle hundreds of thousands of rows and update quickly so they are often used in real time financial trading systems with prices in cells updating every few milliseconds.

If you are using React, then in addition to [AG Grid](https://www.ag-grid.com/react-data-grid/) you can look at [Material UI](https://mui.com/components/data-grid/). [React Table](https://react-table.tanstack.com/) is a 'table' rather than a Data Grid so it requires a bit more code to render the table.

Both Material UI and React Table are only available for React. AG Grid is framework agnostic and will work with JavaScript, TypeScript, React, Angular, and Vue.

The source code for this post can be [found in this Github repo](https://github.com/eviltester/freecodecampexamples) in the folder [docs/html-table-to-data-grid](https://github.com/eviltester/freecodecampexamples/tree/main/docs/html-table-to-data-grid).