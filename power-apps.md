# Power Apps

References I want to remember for later. 

All Power Fx snippets have been separated out into the [power-fx.md](/power-fx.md) file

**In this article**

- [Power Apps](#power-apps)
  - [Embed canvas Power App in an iframe (without header)](#embed-canvas-power-app-in-an-iframe-without-header)
  - [Links](#links)

## Embed canvas Power App in an iframe (without header)

I'll just leave this here...nothing special to it but I have checked this page for the format half a dozen times...time to add it!

```
https://[applinkurl]?source=iframe
```

## Adding a canvas Power App to model-driven Power App navigation

This can be done with a URL, however that opens the link in a new window. To have the app open in the main panel of a model-driven app, the technique is to use a Web Resource for the navigation item. The code of that web resource generally looks like this:

``` html
<html>
  <head>
    <title>Embedded App</title>
    <style>
      body, html {
        margin: 0;
        padding: 0;
        height: 100%;
      }
      iframe {
        display: block;
        width: 100%;
        height: 100%;
        border: none;
      }
    </style>
  </head>
  <body>
    <iframe src="[CanvasAppUrlWithout]"></iframe>
  </body>
</html>
```

Source: [https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/embed-apps-dev](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/embed-apps-dev)

## Links

[Northwind Traders Sample Data](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/northwind-install)
