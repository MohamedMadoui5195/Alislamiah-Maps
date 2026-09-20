<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps</title>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css" />
    <script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; }

        #map { width: 100%; height: 100%; position: absolute; top: 0; left: 0; z-index: 1; }

        .brand-logo {
            position: absolute; top: 20px; left: 20px; z-index: 1000;
            background: #fff; padding: 12px 20px; border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); font-weight: bold; color: #1b4d3e; font-size: 16px;
        }

        .leaflet-control-geocoder {
            border-radius: 8px !important; box-shadow: 0 4px 15px rgba(0,0,0,0.2) !important;
            border: none !important; margin-top: 20px !important; margin-right: 20px !important; padding: 5px !important;
        }
        .leaflet-control-geocoder input {
            font-size: 16px !important; padding: 8px 12px !important; width: 300px !important; outline: none !important;
        }
    </style>
</head>
<body>

    <div class="brand-logo">Alislamiah Maps</div>
    <div id="map"></div>

    <script>
        const savedLat = localStorage.getItem('islamiah_lat');
        const savedLng = localStorage.getItem('islamiah_lng');
        const savedZoom = localStorage.getItem('islamiah_zoom');

        let initialLat = savedLat ? parseFloat(savedLat) : 36.7538; // الجزائر العاصمة افتراضياً
        let initialLng = savedLng ? parseFloat(savedLng) : 3.0588;
        let initialZoom = savedZoom ? parseInt(savedZoom) : (savedLat ? 15 : 6);

        const map = L.map('map', { zoomControl: false }).setView([initialLat, initialLng], initialZoom);

        L.control.zoom({ position: 'bottomleft' }).addTo(map);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; Alislamiah Maps'
        }).addTo(map);

        let currentMarker = null;

        if (!savedLat && navigator.geolocation) {
            navigator.geolocation.getCurrentPosition((position) => {
                const lat = position.coords.latitude;
                const lon = position.coords.longitude;
                map.setView([lat, lon], 15);
                
                if (currentMarker) map.removeLayer(currentMarker);
                currentMarker = L.marker([lat, lon]).addTo(map).bindPopup('<b>موقعك الحالي</b>').openPopup();
                
                localStorage.setItem('islamiah_lat', lat);
                localStorage.setItem('islamiah_lng', lon);
                localStorage.setItem('islamiah_zoom', 15);
            });
        } else if (savedLat && savedLng) {
            currentMarker = L.marker([initialLat, initialLng]).addTo(map).bindPopup('<b>آخر موقع محفوظ</b>').openPopup();
        }

        map.on('moveend', function() {
            const center = map.getCenter();
            localStorage.setItem('islamiah_lat', center.lat);
            localStorage.setItem('islamiah_lng', center.lng);
            localStorage.setItem('islamiah_zoom', map.getZoom());
        });

        const geocoder = L.Control.geocoder({
            defaultMarkGeocode: false,
            placeholder: "ابحث عن أي مكان، شارع، أو مبنى...",
            errorMessage: "لم يتم العثور على الموقع.",
            geocoder: L.Control.Geocoder.nominatim()
        }).addTo(map);

        geocoder.on('markgeocode', function(e) {
            const bbox = e.geocode.bbox;
            const center = e.geocode.center;
            
            map.fitBounds(bbox);

            if (currentMarker) map.removeLayer(currentMarker);
            currentMarker = L.marker([center.lat, center.lng]).addTo(map)
                .bindPopup(`<b>${e.geocode.name}</b>`)
                .openPopup();

            localStorage.setItem('islamiah_lat', center.lat);
            localStorage.setItem('islamiah_lng', center.lng);
            localStorage.setItem('islamiah_zoom', map.getZoom());
        });
    </script>
</body>
</html>
