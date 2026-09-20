<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps - الخريطة الذكية</title>
    
    <!-- مكتبة الخرائط Leaflet -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    
    <!-- محرك البحث العالمي -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css" />
    <script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>

    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; }

        /* الخريطة تملأ الشاشة في الرئيسية */
        #map { width: 100%; height: 100%; position: absolute; top: 0; left: 0; z-index: 1; }

        /* شعار التطبيق */
        .brand-logo {
            position: absolute;
            top: 20px;
            left: 20px;
            z-index: 1000;
            background: #fff;
            padding: 12px 20px;
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            font-weight: bold;
            color: #1b4d3e;
            font-size: 16px;
        }

        /* تخصيص شريط البحث */
        .leaflet-control-geocoder {
            border-radius: 8px !important;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2) !important;
            border: none !important;
            margin-top: 20px !important;
            margin-right: 20px !important;
            padding: 5px !important;
        }
        .leaflet-control-geocoder input {
            font-size: 16px !important;
            padding: 8px 12px !important;
            width: 300px !important;
            outline: none !important;
        }
    </style>
</head>
<body>

    <div class="brand-logo">Alislamiah Maps</div>
    <div id="map"></div>

    <script>
        // التحقق مما إذا كان هناك موقع محفوظ مسبقاً في ذاكرة المتصفح (Local Storage)
        const savedLat = localStorage.getItem('islamiah_lat');
        const savedLng = localStorage.getItem('islamiah_lng');
        const savedZoom = localStorage.getItem('islamiah_zoom');

        // تحديد الإحداثيات الافتتاحية (إما المحفوظة أو مكة المكرمة كافتتاحية أولى)
        let initialLat = savedLat ? parseFloat(savedLat) : 21.4225;
        let initialLng = savedLng ? parseFloat(savedLng) : 39.8262;
        let initialZoom = savedZoom ? parseInt(savedZoom) : (savedLat ? 15 : 6);

        // 1. تهيئة الخريطة وعرضها فوراً في الرئيسية
        const map = L.map('map', { zoomControl: false }).setView([initialLat, initialLng], initialZoom);

        L.control.zoom({ position: 'bottomleft' }).addTo(map);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; Alislamiah Maps'
        }).addTo(map);

        let currentMarker = null;

        // إذا لم يكن هناك موقع محفوظ مسبقاً، نطلب موقع المستخدم (GPS) فوراً
        if (!savedLat && navigator.geolocation) {
            navigator.geolocation.getCurrentPosition((position) => {
                const lat = position.coords.latitude;
                const lon = position.coords.longitude;
                map.setView([lat, lon], 15);
                
                if (currentMarker) map.removeLayer(currentMarker);
                currentMarker = L.marker([lat, lon]).addTo(map)
                    .bindPopup('<b>موقعك الحالي</b>')
                    .openPopup();
                
                // حفظ الموقع الحالي تلقائياً
                saveLocationState(lat, lon, 15);
            });
        } else if (savedLat && savedLng) {
            // وضع علامة على آخر موقع محفوظ
            currentMarker = L.marker([initialLat, initialLng]).addTo(map)
                .bindPopup('<b>آخر موقع محفوظ</b>')
                .openPopup();
        }

        // دالة لتخزين الموقع في ذاكرة المتصفح عند تحريك الخريطة أو تغييرها
        function saveLocationState(lat, lng, zoom) {
            localStorage.setItem('islamiah_lat', lat);
            localStorage.setItem('islamiah_lng', lng);
            localStorage.setItem('islamiah_zoom', zoom);
        }

        // تحديث الموقع المحفوظ كلما قام المستخدم بتحريك الخريطة أو تغيير العرض
        map.on('moveend', function() {
            const center = map.getCenter();
            const zoom = map.getZoom();
            saveLocationState(center.lat, center.lng, zoom);
        });

        // 2. البحث العالمي الشامل
        const geocoder = L.Control.geocoder({
            defaultMarkGeocode: false,
            placeholder: "ابحث عن أي مكان في العالم...",
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

            // حفظ موقع المكان الذي تم البحث عنه والوصول إليه
            saveLocationState(center.lat, center.lng, map.getZoom());
        });
    </script>
</body>
</html>
