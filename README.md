# pwa
pwa installation 


// Add this on main js file

// Register service worker to control making site work offline

if ('serviceWorker' in navigator) {
    navigator.serviceWorker
        .register('{{ asset('sw.js') }}')
        .then(() => { console.log('Service Worker Registered'); });
    }

    // Code to handle install prompt on desktop

    let deferredPrompt;
    const addBtn = document.querySelector('.add-button');

    window.addEventListener('beforeinstallprompt', (e) => {
    // Prevent Chrome 67 and earlier from automatically showing the prompt
    e.preventDefault();
    // Stash the event so it can be triggered later.
    deferredPrompt = e;
    // Update UI to notify the user they can add to home screen
    addBtn.style.display = 'block';

    addBtn.addEventListener('click', () => {
        // hide our user interface that shows our A2HS button
        addBtn.style.display = 'none';
        // Show the prompt
        deferredPrompt.prompt();
        // Wait for the user to respond to the prompt
        deferredPrompt.userChoice.then((choiceResult) => {
        if (choiceResult.outcome === 'accepted') {
            console.log('User accepted the A2HS prompt');
        } else {
            console.log('User dismissed the A2HS prompt');
        }
        deferredPrompt = null;
        });
    });
});




sw.js

var staticCacheName = "offer-pwa-" + new Date().getTime();
var filesToCache = [
    '/images/ico/apple-icon-72x72.png',
    '/images/ico/favicon-96x96.png',
    '/images/ico/android-icon-144x144.png',
    // '/images/ico/android-icon-144x144.png',
    '/images/ico/apple-icon-152x152.png',
    '/images/ico/android-icon-192x192.png',
    // '/images/ico/android-chrome-512x512.png',
    '/images/ico/android-chrome-512x512.png',
    '/images/ico/splash-640x1136.png',
    '/images/ico/splash-750x1334.png',
    '/images/ico/splash-1242x2208.png',
    '/images/ico/splash-1125x2436.png',
    '/images/ico/splash-828x1792.png',
    '/images/ico/splash-1242x2688.png',
    '/images/ico/splash-1536x2048.png',
    '/images/ico/splash-1668x2224.png',
    '/images/ico/splash-1668x2388.png',
    '/images/ico/splash-2048x2732.png'
];

// Cache on install
self.addEventListener("install", event => {
    this.skipWaiting();
    event.waitUntil(
        caches.open(staticCacheName)
            .then(cache => {
                //return cache.addAll(filesToCache);

                (async() => {
                    try {
                        return cache.addAll(filesToCache);
                    }
                    catch{
                        console.log("error occured while caching...")
                    }
                })()

            })
    )
});

// Clear cache on activate
self.addEventListener('activate', event => {
    event.waitUntil(
        caches.keys().then(cacheNames => {
            return Promise.all(
                cacheNames
                    .filter(cacheName => (cacheName.startsWith("offer-pwa-")))
                    .filter(cacheName => (cacheName !== staticCacheName))
                    .map(cacheName => caches.delete(cacheName))
            );
        })
    );
});

// Serve from Cache
self.addEventListener("fetch", event => {
    event.respondWith(
        caches.match(event.request)
            .then(response => {
                return response || fetch(event.request);
            })
            .catch(() => {
                return caches.match('offline');
            })
    )
});






manifest.json 


{
    "background_color": "purple",
    "description": "Shows random fox pictures. Hey, at least it isn't cats.",
    "display": "fullscreen",
    "icons": [
      {
        "src": "icon/fox-icon.png",
        "sizes": "192x192",
        "type": "image/png"
      }
    ],
    "name": "Rokomari",
    "short_name": "Rokomari",
    "start_url": "https://test.glowme.com.bd/"
  }
  





