# OpenTok Annotations Widget for JavaScript

This document describes how to develop a web-based application that uses the OpenTok JavaScript Annotations Widget.

This guide has the following sections:

- [Prerequisites](#prerequisites): A checklist of everything you need to get started.
- [Download the widget](#download-the-widget): Download and use the OpenTok JavaScript Annotations Widget for use in your own development.
- [Deploy](#deploying-the-widget): Deploy your own application that hosts the OpenTok JavaScript Annotations Widget.
- [Explore the code](#explore-the-code): This describes the sample app code design, which uses recommended best practices to create a working implementation that uses the OpenTok JavaScript Annotations Widget.


Once you have learned the basics of deploying an application that uses the OpenTok JavaScript Annotations Widget, explore these advanced topics:

- [Customize the toolbar](#customize-the-toolbar): Customize the annotation toolbar by creating menu items and your own color palette.
- [Annotations with screen sharing](#annotations-with-screen-sharing): Add screen sharing capabilities to your application.
- [Cross-platform compatibility notes](#cross-platform-compatibility-notes): Learn best practices to ensure compatibility of your application across device platforms.


## Prerequisites

To be prepared to develop a web-based application that uses the OpenTok JavaScript Annotations Widget:

1. Review the basic requirements for [OpenTok](https://tokbox.com/developer/requirements/) and [OpenTok.js](https://tokbox.com/developer/sdks/js/#browsers).
2. Your web page must load [OpenTok.js](https://tokbox.com/developer/sdks/js/) first, and then load [opentok-annotations.js](https://github.com/opentok/annotation-widg
et/tree/js/web/script).  
3. Your app will need a **Session ID**, **Token**, and **API key**, which you can get at the [OpenTok Developer Dashboard](https://dashboard.tokbox.com). 

_**NOTE**: The OpenTok Developer Dashboard allows you to quickly run this sample program. For production deployment, you must generate the **Session ID** and **Token** values using the [OpenTok Server SDK](https://tokbox.com/developer/sdks/server/)._


## Download the widget

[Download](https://github.com/opentok/annotation-widget/releases/tag/1.0.0-js-beta) the latest release.

**PRO TIP**: Pull requests are welcome! If you think you may want to contribute back to this project, please feel free to fork or clone the repo. 


## Deploy

The web page that loads the Annotations Widget for JavaScript must be served over HTTP/HTTPS. Browser security limitations prevent you from publishing video using a `file://` path, as discussed in the OpenTok.js [Release Notes](https://www.tokbox.com/developer/sdks/js/release-notes.html#knownIssues). 

To support clients running [Chrome 47 or later](https://groups.google.com/forum/#!topic/discuss-webrtc/sq5CVmY69sc), HTTPS is required, though localhost Chrome clients are considered secure and HTTP is permitted in such cases. 

A web server such as [MAMP](https://www.mamp.info/) or [Apache](https://httpd.apache.org/) will work, or you can use a cloud service such as [Heroku](https://www.heroku.com/) to host the widget. Click the following button for a very quick deploy to Heroku.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/opentok/annotation-widget/tree/js)

## Explore the code

This section describes how the sample app code design uses recommended best practices to create a working implementation that uses the annotation widget. 

To develop your own application with the annotation widget, follow this section to learn how to add the toolbar to your container and create an annotation canvas for both the publisher and subscriber. 

The following steps will help you get started, and you can refer to the [complete code example](https://github.com/opentok/annotation-widget/blob/js/web/index.html):

1. [Web page design](#web-page-design)
2. [Link and Add the Toolbar](#link-and-add-the-toolbar)
3. [Attach the Toolbar to a Publisher](#attach-the-toolbar-to-a-publisher)
4. [Attach the Toolbar to a Subscriber](#attach-the-toolbar-to-a-subscriber)
5. [Clean Up](#clean-up)


### Web page design

While TokBox hosts [OpenTok.js](https://tokbox.com/developer/sdks/js/), you must host the JavaScript Annotations widget yourself. This allows you to modify the widget as desired. The sample app has the following design

* **[opentok-annotations.js](web/script/opentok-annotations.js)**: This is the OpenTok Annotation Plugin, and includes the CSS. If you already have a website making calls against the OpenTok JavaScript client, you can simply download this file and the image files.

* **[Image files](https://github.com/opentok/annotation-widget/tree/js/web/image)**: Images used for the toolbar icons. 

* **[screenshare.html](web/screenshare.html)** The screen sharing container, which has its own toolbar and is displayed in a separate browser window.

* **[index.html](web/index.html)**: this quick start provides you with a fully functioning web page so you can immediately begin making calls against OpenTok.js. You can refer to this example to see how to implement the toolbar on your own page.



### Link and Add the Toolbar

As you create the annotation toolbar instance, link the active OpenTok session to it and add the toolbar to the parent container: 

1. Assign the OpenTok session variable to the `Toolbar` object's `session` property.
2. Assign the element ID for the toolbar to the `Toolbar` object's `container` property.


```javascript
toolbar = new OTSolution.Annotations.Toolbar({
    session: session,
    container: document.getElementById('toolbar')
});
```

For more information, see [Initialize, Connect, and Publish to a Session](https://tokbox.com/developer/concepts/connect-and-publish/).


### Attach the Toolbar to a Publisher

To use the annotation toolbar, you will need a *canvas* on which the publisher can place annotations.

Once you create the publisher instance, create an annotation canvas for the publisher and add it to the toolbar:

1. Create an annotation canvas by instantiating an `Annotations` object:
  - Assign the publisher variable to the `Annotations` object's `feed` property.
  - Assign the publisher div to the `Annotations` object's `container` property.
2. Add the annotation canvas to the annotation toolbar by calling the `Toolbar` object's `addCanvas` method.

```javascript
var canvas = new OTSolution.Annotations({
    feed: publisher,
    container: publisherDiv
});
toolbar.addCanvas(canvas);
```

For more information, see [Publishing Streams](https://www.tokbox.com/developer/guides/publish-stream/js/).


### Attach the Toolbar to a Subscriber
As you create new streams, attach the annotation canvases for each subscriber and add those to the toolbar.

```javascript
var subscriber = session.subscribe(stream, subscriberDiv.id);

...

var canvas = new OTSolution.Annotations({
    feed:  subscriber,
    container: subscriberDiv
});
toolbar.addCanvas(canvas);
```

For more information, see [Subscribing to streams](https://www.tokbox.com/developer/guides/subscribe-stream/js/).

### Clean Up

To remove a single annotation canvas, call the `Toolbar` object's `removeCanvas` method and provide the connection ID for the stream:

```javascript
toolbar.removeCanvas(publisher.stream.connection.connectionId);
```

To remove all annotation canvases and the annotation toolbar in a single invocation, call the `Toolbar` object's `remove` method.

```javascript
toolbar.remove();
```



## Customize the toolbar

You can customize the annotation toolbar by specifying menu items and their associated actions, as well as your own color palette:

1. [Customize Menu Items](#customize-menu-items)
2. [Add a Custom Color Palette](#add-a-custom-color-palette)

See the [quick start](https://github.com/opentok/annotation-widget/blob/js/web/index.html) for a complete code example.

### Customize Menu Items

You can make use of the [default menu items](#default-menu-items) available in the toolbar, or you can add customized menu items and their associated click events:

1. [Add Menu Items](#add-menu-items)
2. [Handle Menu Item Click Events](#handle-menu-item-click-events)

#### Default Menu Items

Below is a list of default menu items that can be used in your custom toolbar menu. These come pre-built with the action already specified.
If no custom items are added to the toolbar initializer, these will be automatically added to your toolbar.

| id            | Action        |
| :------------ | :------------- |
| `OT_pen` | Freehand/Pen tool |
| `OT_line` | Line tool |
| `OT_shapes` | Shapes group/submenu |
| `OT_arrow` | Arrow tool |
| `OT_rect` | Rectangle tool |
| `OT_oval` | Oval tool |
| `OT_colors` | Color picker submenu |
| `OT_line_width` | Line width picker submenu |
| `OT_clear` | Clears active user annotations |
| `OT_capture` | Tap a video frame to capture a screenshot |


#### Add menu items

To add menu items and submenu items, assign them to the `Toolbar` object's `items` property. To create submenu items, add an `items` property to an `item` and continue recursively:

```javascript
toolbar = new OTSolution.Annotations.Toolbar({
    ...
    items: [
        {
            id: 'OT_pen',
            title: 'Pen',
            icon: 'image/freehand.png',
            selectedIcon: 'image/freehand_selected.png'
        },
        {
            id: 'OT_line',
            title: 'Line',
            icon: 'image/line.png'
        },
        {
            id: 'OT_shapes',
            title: 'Shapes',
            icon: 'image/shapes.png',
            items: [
                {
                    id: 'OT_arrow',
                    title: 'Arrow',
                    icon: 'image/arrow.png'
                },

                . . .  

                {
                    id: 'OT_oval',
                    title: 'Oval',
                    icon: 'image/oval.png'
                }
             ]
         }
    ]
});
```

#### Handle Menu Item Click Events

To associate a click event handler with a menu item, define an `itemClicked` method for the `Toolbar` object. Specify the menu item ID as the method parameter:


```javascript
toolbar.itemClicked(function (id) {
// Handle the event
});
```


### Add a Custom Color Palette

To add a set of custom color choices to your annotation toolbar, initialize the `Toolbar` object's `colors` property with an array of color values:

```javascript
toolbar = new OTSolution.Annotations.Toolbar({
    ...
    colors: [
        "#1abc9c",
        "#2ecc71",
        "#3498db",
        "#9b59b6",
        "#34495e",
        "#16a085"
    ]
});
```


## Annotations with screen sharing

For information on setting up your own screen sharing extension, see our sample on [Github](https://github.com/opentok/screensharing-extensions). Once you have
screen sharing set up, follow one of the recommended steps to install the extension on [Firefox](https://github.com/opentok/screensharing-extensions/tree/master/firefox/ScreenSharing#installing-your-extension) or [Chrome](https://github.com/opentok/screensharing-extensions/tree/master/chrome/ScreenSharing#packaging-and-deploying-your-extension-for-use-at-your-website).

The following steps are recommended for setting up screen sharing:

1. [Open a separate screen sharing window](#open-a-separate-screen-sharing-window)
2. [Set up the screen sharing container](#set-up-the-screen-sharing-container)
3. [Create the toolbar](#attach-the-toolbar-to-the-canvas)
4. [Publish the screen sharing window](#publish-the-screen-sharing-window)


### Open a separate screen sharing window

You must open a new, separate window in order to implement annotations with screen sharing. This is required because the entire browser is displayed when sharing a screen, and an external window is needed to account for the differences in browser sizes and configurations. In addition, the user can only share the browser, though it is still possible to switch between tabs. The code snippet below is used to create a new window that points to the URL of the
screen sharing sample with annotations ([screenshare.html](web/screenshare.html)), and specifies height and width values for the new window.

```javascript
function popupCenter(url, w, h) {
    var left = (screen.width/2)-(w/2);
    var top = (screen.height/2)-(h/2);
    var win = window.open(url, '', 'toolbar=no, location=no, directories=no, status=no, menubar=no, scrollbars=no, resizable=no, copyhistory=no, width='+w+', height='+h+', top='+top+', left='+left);

    win.toolbar = toolbar;
}
```

To handle the various screen sharing capabilities and support in the browser, the `startScreenshare()` function in [index.html](web/index.html) is assigned to the screen sharing button’s `onClick` event handler. It contains logic to check the screen sharing support and available extensions, and publishes the screen sharing window. The following portion of its code publishes the screen sharing window:

```javascript
createToolbar(win);
win.toolbar.createPanel(win);
var elements = win.createContainerElements();
startPublishing('screen', elements);
show('stopScreenshareLink');

```

### Set up the screen sharing container

The screen sharing container and the toolbar that will be attached to its canvas are set up in [screenshare.html](web/screenshare.html), as shown in these portions of its code:

```javascript
<script type="text/javascript" charset="utf-8">
  var toolbar;

  function createContainerElements() {
    var parentDiv = document.getElementById('screenshareContainer');
    var publisherContainer = document.createElement('div');
    publisherContainer.setAttribute('id', 'screenshare_publisher');
    publisherContainer.classList.add('publisher-wrap');
    parentDiv.appendChild(publisherContainer);
    return {
       annotation: parentDiv,
       publisher: publisherContainer
    };
  }

</script>

<body>
    <div id="screenshareContainer" style="width:800px;height:600px"></div>
    <div id="toolbar"></div>
</body>
```

### Create the toolbar

Annotations are set up for screen sharing in a similar way as with a video publisher (see [Attaching the Toolbar to a Publisher](#attaching-the-toolbar-to-a-publisher)). In this example, taken from the `createToolbar()` function in [index.html](web/index.html), the toolbar is attached to the screen sharing window. In this portion of the logic, `windowRef` is the screen sharing window. The toolbar is linked to the canvas, which has not yet been created, using the toolbar’s `addCanvas()` method:

```javascript
var windowRef = type === 'camera' ? window : screenshareWindow;

. . .

var toolbarRef = windowRef.toolbar;
toolbarRef.addCanvas(canvas);
```

### Publish the screen sharing window

Now that you have set up the screen sharing window and its toolbar, you can create the canvas, add it to the window, and link the toolbar with the canvas, as shown in this portion of the `startPublishing()` function in [index.html](web/index.html):

```javascript
var windowRef = type === 'camera' ? window : screenshareWindow;
var context = windowRef.document;
var parentDiv;
var publisherDiv;

. . .

publisherDiv = elements.publisher;

. . .

 var canvas = new OTSolution.Annotations({
   feed: publisher,
   container: type === 'camera' ? publisherDiv : elements.annotation
});
                
var toolbarRef = windowRef.toolbar;
toolbarRef.addCanvas(canvas);  
```

_**NOTE**: The above example uses fixed-sized containers for video and canvas containers.  However, it is possible for the containers to be dynamically sized, so long as they maintain aspect ratio.  By default, HTML5 canvas elements are cleared when they are resized. If you would like users to be able to resize the canvas, you can set an `onResize` event listener on the window and call `canvas.onResize` in the event handler. Since the  canvas is redrawn on each resize, which is a computationally expensive operation, it is recommended that you throttle calls to `canvas.onResize` in  order to avoid negatively impacting browser performance._

See the [OpenTok.js screen sharing documentation](https://tokbox.com/developer/guides/screen-sharing/js) for more information about screen sharing.


## Cross-platform compatibility notes

To ensure that all annotations aren't cut off across devices, we recommend:

* Using predefined aspect ratios for your video frames.
* Using the same aspect ratio across device platforms.

The following sample illustrates one way to do this in JavaScript.

```javascript
    function startPublishing() {
        if (!publisher) {
            var parentDiv = document.getElementById('myCamera');
            var publisherDiv = document.createElement('div'); // Create a div for the publisher to replace
            var publisherProperties = {
                name: 'A web-based OpenTok client',
                width: '360px',
                height: '480px'
            };
```

See the [iOS](https://github.com/opentok/annotation-widget/tree/ios#cross-platform-compatibility-notes) and [Android](https://github.com/opentok/annotation-widget/tree/android#cross-platform-compatibility-notes) branches for code samples specific to these platforms.
