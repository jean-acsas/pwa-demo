This is a showcase of a basic PWA as well as some background and instruction to build one yourself. Check out the live demo [here](https://pwa-dem0.netlify.app)

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
    "short_name": "PWADemos",
    "start_url": "/?home=true",
    "icons": [],
    "theme_color": "#317efb",
    "background_color": "#317efb",
    "display": "standalone",
    "orientation": "portrait"
}
```

We place a reference to our manifest above the closing `</head>` tag of our index.html:

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

The service worker your be implemented by hand, this example however uses [Workbox](https://developers.google.com/web/tools/workbox) to help us with this. Workbox is a set of libraries that help you build PWAs. Should you use React, Vue or a similar framework, chances are it already comes packed with tooling to set up your service worker.

For this showcase we import Workbox from a CDN:

```js
importScripts('https://storage.googleapis.com/workbox-cdn/releases/6.2.0/workbox-sw.js');
```

Then we can use the routing module to register a route that matches all the image files in our project.
Then matched all the files, we need to decide on a caching strategy.

```js
workbox.routing.registerRoute(
     ({request}) => request.destination === 'image',
     new workbox.strategies.CacheFirst()
)
```

CacheFirst() means the PWA will try to read from the cache first, which is useful for content that don't change often. Alternatively, you might want to use the NetworkFirst() strategy.

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

Open a browser and navigate to `localhost:5000`.

## Check out Lighthouse to see the PWA badge

Either use Chrome/Chromium or add Lighthouse as a plugin to our browser.

In case of Chrome/Chromium: 

- open up Chrome Dev Tools
- go to the Application tab 
- see how the icons appear in our manifest (Application->Manifest).
- check the status of the service worker, which should be running. 
- finally go to the chrome dev tools Lighthouse tab and run an audit on the app. There, you will see a PWA achievement badge. This means the app is now installable on most mobile devices. 

## Deploy and share your app

You can now deploy your app, so you or others can download it and use it on their devices. This can be done via the installation popup that appears automatically or the context menu when visiting the site. 

For hosting a PWA based on a static website any static hosting should do. For this [live demo](https://pwa-dem0.netlify.app) [Netlify](https://www.netlify.com) was used.