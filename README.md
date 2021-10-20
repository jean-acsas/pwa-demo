[![Netlify Status](https://api.netlify.com/api/v1/badges/f01cc588-705a-413d-b569-e0e44dc14e46/deploy-status)](https://app.netlify.com/sites/pwa-dem0/deploys)

This is a showcase of a basic Progressive Web App (PWA) as well as some background info and instructions on how to build one yourself. Check out the live demo [here](https://pwa-dem0.netlify.app).

# Build a PWA yourself

## Getting Started

We need to create four files to get started:

- a barebone index.html
- a logo (logo.png in this case)
- an empty js file for the service worker (service-worker.js)
- an empty json file for the manifest (manifest.json)

## Setting up the manifest

Within our manifest.json we can define  info about our app. Here is some example content to get started:
```json
{
    "name": "PWADemo",
    "short_name": "PWADemo",
    "start_url": "/index.html",
    "icons": [],
    "theme_color": "#317efb",
    "background_color": "#317efb",
    "display": "standalone",
    "orientation": "portrait"
}
```

Within our index.html, we place a reference to our manifest above the closing `</head>` tag:

```html
<link rel="manifest" href="manifest.json">
```

We now create/generate icons of various sizes based on our logo.png and add references to these icons to the icons array within the manifest.json. The icons could be created manually, but here we use the [pwa-asset-generartor](https://github.com/onderceylan/pwa-asset-generator) the automate this process. 

We call the generator like so:

```npx pwa-asset-generator logo.png ./icons -m ./manifest.json -i ./index.html```

We call the generator with four arguments: 
- `logo.png`, the logo to generate the icons from
- `./icons`, the target directory for the generated icon files
- our `manifest.json`, in which the tool will populate the icons array with relevant icon information
- our `index.html`, which the tool will populate with `<link>`s and `<meta>` tags with iOS related content

## Setting up the service worker

Next up is the implementation of the service worker. The service workers role is to make the website/app ready for offline use by enabling caching, dealing with push notifications, and more. 

The service worker could be implemented by hand, however, in this example we use [Workbox](https://developers.google.com/web/tools/workbox) to help us with this. Workbox is a set of libraries that help you build PWAs. Should you use React, Vue or a similar framework, chances are it already comes pre-packed with tooling for setting up your service worker.

For this showcase we import Workbox from a CDN:

```js
importScripts('https://storage.googleapis.com/workbox-cdn/releases/6.2.0/workbox-sw.js');
```

Then we can use the routing module to register a route that matches all the image files in our project.
We need to decide on a caching strategy here:

```js
workbox.routing.registerRoute(
     ({request}) => request.destination === 'image',
     new workbox.strategies.CacheFirst()
)
```

CacheFirst() means the PWA will try to read from the cache first, which is useful for content that doesn't change often. Alternatively, you might want to use the NetworkFirst() strategy.

Finally, we register our service worker within a `<script>` above the closing `</body>` tag of our index.html.

```html
<script>
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('/service-worker.js');
    }
</script>
```

## Run it

Now let's serve the app. For this, either open the index.html with a live server from your IDE/editor of choice or run:

```bash
npx serve
```

Open a browser and navigate to http://localhost:5000.

## Check out Lighthouse to see the PWA badge

Lighthouse not only analyzes your sites performance, accessibility, and SEO, but also determines whether or not your app is installable as a PWA.

Lighthouse comes preinstalled with Chrome/Chromium, other browsers often provide a plugin.

In case of Chrome/Chromium: 

- open up Dev Tools
- go to the Application tab 
- see how the icons appear in our manifest (Application->Manifest).
- check the status of the service worker, which should be running. 
- finally go to the chrome dev tools Lighthouse tab and run an audit on the app. There, you will see a PWA achievement badge. This means the app is now installable on most mobile devices.

## Deploy and share your app

You can now deploy your app. For hosting a PWA based on a static website any static hosting should do. This [live demo](https://pwa-dem0.netlify.app) is deployed on [Netlify](https://www.netlify.com).

Others can now download and use the app by visiting the site. The app can be installed either via the installation popup that appears automatically or via the browsers context menu.

## Note

This might not work on (your version of) iOS.
