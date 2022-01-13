HTML Tables are simple to use for rendering small amounts of data. When we want to work with a lot of data and offer the user features like sorting, filtering and pagination, then we need to consider adding a JavaScript component.

In this post we will use the free community edition of AG Grid Data Grid to convert from a long static HTML table to an easy to use interactive Data Grid. The amount of JavaScript we need to do this is minimal and very simple.

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

Rendered in a browser the page would look like the image below.

The `table` is styled to have a width of 100% of the page using `width:100%` and the border lines between cells in the table have been styled to show as one line with `border-collapse: collapse`.

Without the `border-collapse` value the table would look like the image below:

## Benefits of short HTML Tables

HTML Tables are a very fast way to render small amounts of data in a tabular form on a page.

Tables require styling to look nicer because the default styling of a `table` varies between browsers and often is shown with no borders making the data hard to read.

At the moment our list of Todo Items is statically coded into the page. For the next step we will `fetch` the list from a REST API using JavaScript.


## Reading JSON from an API to render in HTML Table

Since we will be loading the data from an API, I won't hard code any data in the table. To support dynamic loading I simply remove the line of data from the `table` because I'm going to create the data rows using JavaScript:

```
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

```
    <script type="text/javascript" charset="utf-8">
    </script>
</body>
```

To start, I will write the code that reads the data.

I will use the "{JSON} Placeholder" application for this demonstration.

By making a `GET` request on the URL [https://jsonplaceholder.typicode.com/todos](https://jsonplaceholder.typicode.com/todos) we will receive a JSON response which is a list of Todo Items.

You can try it for yourself without JavaScript by clicking on the link above.

The easiest way to make a `GET` request on the API is by using the `fetch` function built into JavaScript.

```
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

```
fetch('https://jsonplaceholder.typicode.com/todos')
```

- Then when the request is finished convert the response to a JavaScript Object, in our case this will be an array containing all the Todo Items.

```
.then(function (response) {
                return response.json();
            })
```

- Then write the JavaScript object to the console

```
.then(function (apiJsonData) {
                console.log(apiJsonData);
            })
```

With this code in our application, we won't see anything in the table, but we would see the array rendered in the Browser Dev Tools Console.

```
(200) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}
, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, …]
```

The API call returns 200 items, and each item is a Todo Object:

```
  {
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": false
  }
```

Our next step is to render the data in the table:

```
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

There are many free Data Grid Components available, most of them are framework specific so they require coding in React, Angular or Vue.

I'm using AG Grid for this example because the free version can be used with JavaScript, TypeScript, React, Angular or Vue. The "AG" stands for Agnostic so can be used with any framework.

Also, the free version can be used in commercial applications so if you manage to expand the demo application shown here into a commercial Todo Management Application, you'll still be able to use AG Grid for free. Many commercial applications have been built using the free version of AG Grid.

The commercial version of AG GRid has extra features like Excel Exporting and creating Graphs but we don't need any of that functionality in this demo.

Using a Data Grid means that we configure the Data Grid, and give it the data to render, and the Grid handles all the other functionality like sorting, filtering and pagination.

We can convert our existing code to use AG Grid with just a few changes.
