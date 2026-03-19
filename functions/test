const CACHE = 'trainershub-v1';
const ASSETS = [
  '/',
  '/index.html',
  'https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&display=swap'
];

// Installeer service worker en cache assets
self.addEventListener('install', e => {
  e.waitUntil(
    caches.open(CACHE).then(cache => cache.addAll(ASSETS))
  );
  self.skipWaiting();
});

// Activeer en verwijder oude caches
self.addEventListener('activate', e => {
  e.waitUntil(
    caches.keys().then(keys =>
      Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k)))
    )
  );
  self.clients.claim();
});

// Fetch — network first, cache als fallback
self.addEventListener('fetch', e => {
  // Netlify functions altijd via netwerk
  if (e.request.url.includes('/.netlify/functions/') ||
      e.request.url.includes('supabase.co') ||
      e.request.url.includes('stripe.com')) {
    return;
  }

  e.respondWith(
    fetch(e.request)
      .then(res => {
        // Sla succesvolle responses op in cache
        if (res.ok && e.request.method === 'GET') {
          const copy = res.clone();
          caches.open(CACHE).then(cache => cache.put(e.request, copy));
        }
        return res;
      })
      .catch(() => caches.match(e.request))
  );
});
