<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps</title>
    
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; }

        #map { width: 100%; height: 100%; position: absolute; top: 0; left: 0; }

        .brand-logo {
            position: absolute; top: 20px; left: 20px; z-index: 5;
            background: #fff; padding: 12px 20px; border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); font-weight: bold; color: #1b4d3e; font-size: 16px;
        }

        #search-input {
            position: absolute; top: 20px; right: 20px; z-index: 5;
            width: 400px; height: 50px; padding: 0 15px; font-size: 16px;
            border: none; border-radius: 8px; box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            outline: none; background: #fff;
        }
    </style>
</head>
<body>

    <div class="brand-logo">Alislamiah Maps</div>
    <input id="search-input" type="text" placeholder="البحث في Alislamiah Maps...">
    <div id="map"></div>

    <script>
        function initMap() {
            const defaultLocation = { lat: 9.0820, lng: 8.6753 }; // نيجيريا (أبوجا)

            const map = new google.maps.Map(document.getElementById("map"), {
                center: defaultLocation,
                zoom: 13,
            });

            // استرجاع الموقع المحفوظ أو تحديد موقع المستخدم
            const savedLat = localStorage.getItem('islamiah_lat');
            const savedLng = localStorage.getItem('islamiah_lng');

            if (savedLat && savedLng) {
                map.setCenter({ lat: parseFloat(savedLat), lng: parseFloat(savedLng) });
            } else if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition((position) => {
                    const pos = { lat: position.coords.latitude, lng: position.coords.longitude };
                    map.setCenter(pos);
                    new google.maps.Marker({ position: pos, map: map, title: "موقعك الحالي" });
                    localStorage.setItem('islamiah_lat', pos.lat);
                    localStorage.setItem('islamiah_lng', pos.lng);
                });
            }

            map.addListener('center_changed', () => {
                const center = map.getCenter();
                localStorage.setItem('islamiah_lat', center.lat());
                localStorage.setItem('islamiah_lng', center.lng());
            });

            const input = document.getElementById("search-input");
            const autocomplete = new google.maps.places.Autocomplete(input);
            autocomplete.bindTo("bounds", map);

            const marker = new google.maps.Marker({ map: map });

            autocomplete.addListener("place_changed", () => {
                marker.setVisible(false);
                const place = autocomplete.getPlace();

                if (!place.geometry || !place.geometry.location) return;

                if (place.geometry.viewport) {
                    map.fitBounds(place.geometry.viewport);
                } else {
                    map.setCenter(place.geometry.location);
                    map.setZoom(17);
                }

                marker.setPosition(place.geometry.location);
                marker.setVisible(true);
            });
        }
    </script>
    
    <script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places&callback=initMap"></script>
</body>
</html>
