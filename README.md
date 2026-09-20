<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps - النظام الشامل</title>
    
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; }

        #map { width: 100%; height: 100%; position: absolute; top: 0; left: 0; }

        /* شعار التطبيق */
        .brand-logo {
            position: absolute; top: 20px; left: 20px; z-index: 5;
            background: #fff; padding: 12px 20px; border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); font-weight: bold; color: #1b4d3e; font-size: 16px;
        }

        /* شريط بحث جوجل الاحترافي (يدعم العمارات، الشركات، وأدق التفاصيل) */
        #search-input {
            position: absolute; top: 20px; right: 20px; z-index: 5;
            width: 420px; height: 50px; padding: 0 15px; font-size: 16px;
            border: none; border-radius: 8px; box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            outline: none; background: #fff;
        }

        /* نافذة تفاصيل المكان (الصور، أوقات العمل، أرقام الهواتف) تماماً مثل جوجل */
        #place-details {
            position: absolute; bottom: 30px; right: 20px; z-index: 5;
            width: 380px; background: white; padding: 20px; border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3); display: none;
        }
        #place-details h3 { color: #1b4d3e; margin-bottom: 8px; font-size: 18px; }
        #place-details p { color: #5f6368; font-size: 14px; margin-bottom: 6px; }
        #place-details img { width: 100%; height: 160px; object-fit: cover; border-radius: 8px; margin-top: 10px; }
    </style>
</head>
<body>

    <div class="brand-logo">Alislamiah Maps</div>

    <!-- شريط البحث المتقدم -->
    <input id="search-input" type="text5" placeholder="ابحث عن عمارة، شركة، مطعم، أو معلم...">

    <!-- حاوية الخريطة -->
    <div id="map"></div>

    <!-- نافذة معلومات المكان الاحترافية -->
    <div id="place-details">
        <h3 id="place-name"></h3>
        <p id="place-address"></p>
        <p id="place-phone"></p>
        <p id="place-hours" style="font-weight: bold;"></p>
        <div id="place-image-container"></div>
    </div>

    <!-- استدعاء خرائط جوجل وتفعيل مكتبة Places لجلب الصور والهواتف وأوقات العمل -->
    <script>
        function initMap() {
            // موقع افتراضي (مكة المكرمة)
            const defaultLocation = { lat: 21.4225, lng: 39.8262 };

            const map = new google.maps.Map(document.getElementById("map"), {
                center: defaultLocation,
                zoom: 15,
                disableDefaultUI: false
            });

            // استرجاع الموقع المحفوظ مسبقاً في ذاكرة المتصفح
            const savedLat = localStorage.getItem('islamiah_lat');
            const savedLng = localStorage.getItem('islamiah_lng');

            if (savedLat && savedLng) {
                map.setCenter({ lat: parseFloat(savedLat), lng: parseFloat(savedLng) });
            } else if (navigator.geolocation) {
                // طلب الموقع الجغرافي (GPS) فوراً وتحديد مكان المستخدم
                navigator.geolocation.getCurrentPosition((position) => {
                    const userPos = { lat: position.coords.latitude, lng: position.coords.longitude };
                    map.setCenter(userPos);
                    new google.maps.Marker({ position: userPos, map: map, title: "موقعك الحالي" });
                    localStorage.setItem('islamiah_lat', userPos.lat);
                    localStorage.setItem('islamiah_lng', userPos.lng);
                });
            }

            // حفظ الموقع عند تحريك الخريطة
            map.addListener('center_changed', () => {
                const center = map.getCenter();
                localStorage.setItem('islamiah_lat', center.lat());
                localStorage.setItem('islamiah_lng', center.lng());
            });

            // تفعيل البحث الشامل (للعمارات، الشركات، وكل الأماكن العالمية بدقة جوجل)
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

                // إظهار تفاصيل المكان الاحترافية (الصور، أوقات العمل، الهواتف) تماماً مثل جوجل
                const detailsBox = document.getElementById("place-details");
                detailsBox.style.display = "block";

                document.getElementById("place-name").innerText = place.name || "";
                document.getElementById("place-address").innerText = place.formatted_address || "";
                document.getElementById("place-phone").innerText = place.formatted_phone_number ? `📞 ${place.formatted_phone_number}` : "";
                
                // حالة العمل (مفتوح / مغلق)
                if (place.opening_hours) {
                    const isOpen = place.opening_hours.isOpen();
                    document.getElementById("place-hours").innerText = isOpen ? "🟢 مفتوح الآن" : "🔴 مغلق حالياً";
                    document.getElementById("place-hours").style.color = isOpen ? "green" : "red";
                } else {
                    document.getElementById("place-hours").innerText = "";
                }

                // جلب صورة المكان إن وجدت
                const imgContainer = document.getElementById("place-image-container");
                imgContainer.innerHTML = "";
                if (place.photos && place.photos.length > 0) {
                    const img = document.createElement("img");
                    img.src = place.photos[0].getUrl({ maxWidth: 400, maxHeight: 200 });
                    imgContainer.appendChild(img);
                }
            });
        }
    </script>
    
    <!-- ضع مفتاح API الخاص بجوجل هنا لتشغيل الصور والأماكن والعمارات بدقة مطلقة -->
    <script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places&callback=initMap"></script>

</body>
</html>
