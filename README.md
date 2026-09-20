<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps</title>
    
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; }

        /* حاوية الخريطة الخاصة بجوجل */
        #map { width: 100%; height: 100%; position: absolute; top: 0; left: 0; }

        /* شعار التطبيق (بديل لاسم وشعار جوجل) */
        .brand-logo {
            position: absolute;
            top: 15px;
            left: 20px;
            z-index: 5;
            background: #fff;
            padding: 12px 20px;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.3);
            font-weight: bold;
            color: #1b4d3e;
            font-size: 16px;
        }

        /* شريط البحث المدمج من جوجل */
        #search-input {
            position: absolute;
            top: 15px;
            right: 20px;
            z-index: 5;
            width: 400px;
            height: 48px;
            padding: 0 15px;
            font-size: 16px;
            border: none;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.3);
            outline: none;
            background: #fff;
        }
    </style>
</head>
<body>

    <!-- شعار هويتك -->
    <div class="brand-logo">Alislamiah Maps</div>

    <!-- شريط البحث (مدعوم بخدمة الأماكن من جوجل) -->
    <input id="search-input" type="text" placeholder="البحث في Alislamiah Maps...">

    <!-- حاوية الخريطة -->
    <div id="map"></div>

    <!-- استدعاء مكتبة وسكريبت خرائط جوجل الرسمي (يعتمد على خوادمهم بدون أن تمتلك خادم) -->
    <script>
        function initMap() {
            // الإحداثيات الافتراضية المبدئية قبل تحديد موقع المستخدم
            const defaultLocation = { lat: 21.4225, lng: 39.8262 }; // مكة المكرمة

            // إنشاء الخريطة بنفس خصائص وشكل جوجل ماب
            const map = new google.maps.Map(document.getElementById("map"), {
                center: defaultLocation,
                zoom: 14,
                disableDefaultUI: false, // إظهار أزرار التحكم الافتراضية لجوجل
            });

            // 1. طلب موقع المستخدم (GPS) فوراً وتوجيه الخريطة إليه تماماً مثل جوجل
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        const userLocation = {
                            lat: position.coords.latitude,
                            lng: position.coords.longitude,
                        };
                        map.setCenter(userLocation);
                        
                        // وضع علامة لموقع المستخدم الحالي
                        new google.maps.Marker({
                            position: userLocation,
                            map: map,
                            title: "أنت هنا (Alislamiah Maps)",
                            icon: "http://maps.google.com/mapfiles/ms/icons/blue-dot.png" // علامة زرقاء لموقعك
                        });
                    },
                    () => {
                        console.log("تعذر تحديد الموقع الجغرافي تلقائياً.");
                    }
                );
            }

            // 2. تفعيل شريط البحث الذكي (Places Autocomplete) الخاص بجوجل لجلب جميع الأماكن والتقييمات والبيانات
            const input = document.getElementById("search-input");
            const autocomplete = new google.maps.places.Autocomplete(input);
            autocomplete.bindTo("bounds", map);

            const marker = new google.maps.Marker({
                map: map,
                anchorPoint: new google.maps.Point(0, -29),
            });

            autocomplete.addListener("place_changed", () => {
                marker.setVisible(false);
                const place = autocomplete.getPlace();

                if (!place.geometry || !place.geometry.location) {
                    window.alert("لم يتم العثور على تفاصيل لهذا المكان");
                    return;
                }

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

    <!-- استدعاء سكريبت جوجل الرسمي (استبدل YOUR_API_KEY بمفتاحك الخاص من منصة مطوري جوجل) -->
    <script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places&callback=initMap"></script>

</body>
</html>
