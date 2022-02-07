When writing a Vanilla JavaScript application, we don't have to host all the code we use on our own site. Most popular JavaScript libraries are available from a public Content Delivery Network (CDN). This can simplify deploying the application, and keeping dependencies up to date. In fact, if you've followed any JavaScript tutorial for a library then you've probably already used a public CDN but may not be aware of it.

## What is a CDN?

A CDN is a Content Delivery Network. These are file hosting services for multiple versions of common libraries. They are usually highly performant and offer location cached files so no matter where your users are, they receive the files from geo locations close to them. This can make your application faster than hosting files yourself.

CDN's also have the advantage that if you are using libraries common to multiple sites then your users may already have the file cached in their browser, which speeds up your site because the browser doesn't need to download the library again.

For example, JQuery has an [official JQuery CDN](https://releases.jquery.com/) and if most JQuery applications imported the JQuery library from this CDN then users are more likely to have JQuery in their cache already.

## `npm` driven CDNs

Not every library has their own CDN, most libraries deploy to [npmjs.com](https://npmjs.com) and rely on the programmer adding the library to their project via `npm` at build time. `npm` downloads the library from a CDN and adds it to the project locally.

We don't have to be using `npm` and JavaScript build processes to take advantage of the `npm` eco-system. We can use an 'npm driven CDN' as the host for libraries, without having to use `npm`.

An 'npm driven CDN' is one which hosts the distribution code for libraries which deploy to `npm`.

For example, [AG Grid](https://ag-grid.com) which I demonstrated in the FreeCodeCamp post "[How to Convert a Static HTML Table to a Dynamic JavaScript Data Grid](https://www.freecodecamp.org/news/convert-html-table-to-dynamic-javascript-data-grid/)" deploys to `npm` but does not have its own CDN. Instead, programmers can add a direct reference to AG Grid from a CDN like [unpkg.com](https://unpkg.com/).

```html
<script src=
"https://unpkg.com/ag-grid-community/dist/ag-grid-community.min.nostyle.js">
</script>
```

`npm` driven CDNs monitor the releases distributed via `npm`, and host the releases on their own site.

In the source code for my earlier FreeCodeCamp post I used the unpkg.com CDN to import AG Grid in my code using a `script` element. If you've followed any online tutorial for a JavaScript library then you've probably already used a public CDN but may not be aware of it.

As with all code that we copy and paste, it's worth understanding what it does so that we can handle any issues.

## What CDNs are available?

I've only ever used three CDNs:

- [Unpkg.com](https://unpkg.com)
- [jsDelivr.com](https://www.jsdelivr.com)
- [cdnjs.com](https://cdnjs.com)

These are all professional and well run sites and the main reason I pick one above the other is that the tutorial I first followed for a library used that particular CDN in the code.

Knowing that multiple CDNs are available is useful because:

- a particular version of a library you want to use might not be on every CDN
- if a CDN starts having issues then you can amend your code to use another
- some libaries may be more popular on one CDN than another and you may want to use the most popular CDN to increase the chance that the JavaScript library code is cached on your users's browser.


## `npm` CDNs distribute more than JavaScript

CDNs deliver more than JavaScript, for example, AG Grid deploys CSS files as well as JavaScript.

These would be referenced from the CDN as normal using `link` elements.

e.g. AG Grid uses two style sheets.

The Structural Style sheet provides the CSS that will layout the data as a Grid.

```
<link 
 rel="stylesheet"
 href="https://unpkg.com/ag-grid-community/dist/styles/ag-grid.css"
>
```    

The Theme Style sheet provides the visual aesthetics for the Grid.

```
<link 
 rel="stylesheet" 
 href="https://unpkg.com/ag-grid-community/dist/styles/ag-theme-alpine.css"
>
```    

Both of these CSS files are also deployed to npmjs.com and can be included into our project from a CDN.


## Controlling the Version of the library

In my open source table editing tool:

https://eviltester.github.io/grid-table-editor/#

I use three libraries: AG Grid, PapaParse, Faker

Faker recently had an issue where a recent version deployed to npm had issues, if my code had been defaulting to the latest version then my application would have failed.

Fortunately I had imported a specific version of faker from unpkg. As you can see from the `src` url below I included version `5.5.3`

```html
<script src=
    "https://unpkg.com/faker@5.5.3/dist/faker.min.js">
</script>
```

There are number of advantages to controlling the version:

- you will have tested your application on a specific version, if you deploy to production and allow the version to change with every new release then your application may gradually exhibit bugs or incompatibilities over time because you haven't tested your application against the new version of the library.
- when using multiple versions of libraries, future versions may conflict with each other, again causing your application to fail in production and possibly without you noticing.

I import the three libraries I use at specific versions:

```html
<script src="https://unpkg.com/ag-grid-community@26.2.1/dist/ag-grid-community.min.js"></script>
<script src="https://unpkg.com/papaparse@5.3.0/papaparse.min.js"></script>
<script src="https://unpkg.com/faker@5.5.3/dist/faker.min.js"></script>
```

This puts me in control of the testing and I'm more confident that any bugs in the application will be a result of my coding, rather than an update to a library used in the project.

The different CDNs may have a different syntax for controlling the version numbers but you will be able to see the format to use by searching for the library on the CDN and following the documentation provided by each CDN.

Here are the listings for AG Grid on each of the CDN sites:

- [Unpkg.com - AG Grid](https://unpkg.com/ag-grid-community/)
- [jsDelivr.com - AG Grid](https://www.jsdelivr.com/package/npm/ag-grid-community)
- [cdnjs.com - AG Grid](https://cdnjs.com/libraries/ag-grid)

If you follow the links for the AG Grid packages then you will see that each site has a slightly different interface but they all allow selecting a specific version of AG Grid and allow you to copy and paste the URL to add to your HTML file.

## CDN in Practice

Below is a simple html file which will render a Data Grid on the screen.

I only have to deploy the html file, because the AG Grid library is loaded into the browser from the CDN.

In the example below I'm loading version 26.2.1 of AG Grid and the CSS files from the **unpkg.com CDN**.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>CDN Added AG Grid Example</title>
    <script src="https://unpkg.com/ag-grid-community@26.2.1/dist/ag-grid-community.min.nostyle.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community@26.2.1/dist/styles/ag-grid.css">
    <link rel="stylesheet" href="https://unpkg.com/ag-grid-community@26.2.1/dist/styles/ag-theme-alpine.css">
</head>
<body>

    <div id="myGrid" style="height: 200px; width:500px;" class="ag-theme-alpine"></div>

<script>
document.addEventListener('DOMContentLoaded', () => {

    const columnDefs = [
        { field: "cdn" },
        { field: "url" },
    ];

    const rowData = [
        { cdn: "jsDelivr", url: "https://www.jsdelivr.com"},
        { cdn: "Unpkg", url: "https://Unpkg.com" },
        { cdn: "cdnJS", url: "https://cdnjs.com" }
    ];


    const gridOptions = {
        columnDefs: columnDefs,
        rowData: rowData,
        /* allow manual copy and paste */
        enableCellTextSelection:true,
        ensureDomOrder:true
    };


    const gridDiv = document.querySelector('#myGrid');
    new agGrid.Grid(gridDiv, gridOptions);
});
</script>
</body>
</html>
```

[Unpkg imports are demonstrated in this deployed html page.](https://eviltester.github.io/freecodecampexamples/javascript-cdns/adding-ag-grid-from-unpkg.html)

![cdns and their urls](https://www.freecodecamp.org/news/content/images/2022/02/cdn-urls.png)

I could easily use other CDNs by changing the `script` and `link` elements in the `head` section of my `html` file.

**JSDelivr** using version 26.2.1

- https://cdn.jsdelivr.net/npm/ag-grid-community@26.2.1/dist/ag-grid-community.min.noStyle.js
- https://cdn.jsdelivr.net/npm/ag-grid-community@26.2.1/dist/styles/ag-grid.css
- https://cdn.jsdelivr.net/npm/ag-grid-community@26.2.1/dist/styles/ag-theme-alpine.css

[JSDelivr imports are demonstrated in this deployed html page.](https://eviltester.github.io/freecodecampexamples/javascript-cdns/adding-ag-grid-from-jsdelivr.html)

**CdnJS** using version 26.2.1. CdnJS takes a slightly different approach to version naming so it is worth checking the version drop down on the [cdnJS AG Grid listing](https://cdnjs.com/libraries/ag-grid)

- https://cdnjs.cloudflare.com/ajax/libs/ag-grid/Docs-26.2.0-20211117/ag-grid-community.min.noStyle.min.js
- https://cdnjs.cloudflare.com/ajax/libs/ag-grid/Docs-26.2.0-20211117/styles/ag-grid.min.css
- https://cdnjs.cloudflare.com/ajax/libs/ag-grid/Docs-26.2.0-20211117/styles/ag-theme-alpine.min.css

[CdnJS imports are demonstrated in this deployed html page.](https://eviltester.github.io/freecodecampexamples/javascript-cdns/adding-ag-grid-from-cdnjs.html)

## Summary

We don't always have to deploy JavaScript libraries to our site along with our HTML files, we can include them such that they are delivered by a Content Delivery Network.

Many tutorials we follow will show examples of doing this.

If we do import JavaScript or CSS from a CDN we should add the version number of the library that we are using to protect our deployed example from future issues if the library updates.

A good habit to get into is adding the version evern when we follow a tutorial. If we notice that the CDN included files do not have a version then it is worth visiting the CDN to find out what the current version of the library is, and then adding that version number to our source.

It is small adjustments like this that will make any portfolio projects you create on Github that little bit more professional.