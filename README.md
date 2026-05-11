<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Compare PWA</title>
    <link rel="manifest" href="manifest.json">
    <style>
        body { font-family: 'Segoe UI', sans-serif; padding: 20px; background: #f0f2f5; color: #333; }
        .container { max-width: 600px; margin: auto; }
        h1 { text-align: center; color: #2980b9; }
        input { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #ccc; margin-bottom: 20px; box-sizing: border-box; }
        .card { background: white; padding: 15px; border-radius: 10px; margin-bottom: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); border-left: 5px solid #ccc; }
        .best-price { border-left-color: #27ae60; background: #fafffa; } /* Verde per il risparmio */
        .fastest { border-left-color: #2980b9; background: #f0f7ff; } /* Blu per la velocità */
        .badge { font-size: 0.8em; padding: 3px 8px; border-radius: 4px; color: white; font-weight: bold; margin-left: 10px; }
        .bg-green { background: #27ae60; }
        .bg-blue { background: #2980b9; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Smart Compare 🏷️</h1>
        <input type="text" id="search" placeholder="Cerca prodotto (es. iPhone 15)...">
        <div id="results"></div>
    </div>

    <script src="app.js"></script>
    <script>
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js');
        }
    </script>
</body>
</html>
// Dati simulati (Mock Data)
const products = [
    { site: "Amazon", price: 799, delivery: 1, link: "#" },
    { site: "eBay", price: 749, delivery: 5, link: "#" },
    { site: "MediaWorld", price: 810, delivery: 2, link: "#" },
    { site: "Unieuro", price: 755, delivery: 3, link: "#" }
];

function compareProducts() {
    const container = document.getElementById('results');
    
    // Trova i valori migliori
    const minPrice = Math.min(...products.map(p => p.price));
    const minDelivery = Math.min(...products.map(p => p.delivery));

    container.innerHTML = products.map(item => {
        const isBestPrice = item.price === minPrice;
        const isFastest = item.delivery === minDelivery;
        
        return `
            <div class="card ${isBestPrice ? 'best-price' : ''} ${isFastest ? 'fastest' : ''}">
                <strong>${item.site}</strong>
                ${isBestPrice ? '<span class="badge bg-green">MIGLIOR PREZZO</span>' : ''}
                ${isFastest ? '<span class="badge bg-blue">PIÙ VELOCE</span>' : ''}
                <p>Prezzo: <b>${item.price}€</b><br>
                Consegna stimata: <b>${item.delivery} giorni</b></p>
                <a href="${item.link}" style="color: #2980b9; text-decoration: none;">Vai al sito →</a>
            </div>
        `;
    }).join('');
}

// Avvia il confronto al caricamento
compareProducts();
{
  "name": "Smart Price Comparison",
  "short_name": "SmartCompare",
  "start_url": "index.html",
  "display": "standalone",
  "background_color": "#f0f2f5",
  "theme_color": "#2980b9",
  "icons": [
    {
      "src": "https://cdn-icons-png.flaticon.com/512/263/263142.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
const CACHE_NAME = 'compare-v1';
const assets = ['./', './index.html', './app.js', './manifest.json'];

self.addEventListener('install', (e) => {
    e.waitUntil(
        caches.open(CACHE_NAME).then((cache) => cache.addAll(assets))
    );
});

self.addEventListener('fetch', (e) => {
    e.respondWith(
        caches.match(e.request).then((res) => res || fetch(e.request))
    );
});
