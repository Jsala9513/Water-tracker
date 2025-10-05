const CACHE = 'water-tracker-v1';
const ASSETS = [
  './',
  './index.html',
  './manifest.webmanifest'
  // If you add icons later:
  // './icon-192.png',
  // './icon-512.png'
];

self.addEventListener('install', e => {
  e.waitUntil(caches.open(CACHE).then(c => c.addAll(ASSETS)));
});

self.addEventListener('activate', e => {
  e.waitUntil(
    caches.keys().then(keys =>
      Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k)))
    )
  );
});

self.addEventListener('fetch', e => {
  const url = new URL(e.request.url);
  // Cache-first for same-origin GET requests
  if (e.request.method === 'GET' && url.origin === location.origin) {
    e.respondWith(
      caches.match(e.request).then(cached => 
        cached || fetch(e.request).then(resp => {
          // Optional: runtime cache
          return resp;
        })
      )
    );
  }
});
