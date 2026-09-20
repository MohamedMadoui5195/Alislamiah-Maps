<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps - خريطة الأماكن الإسلامية</title>
    
    <!-- مكتبة الخرائط المفتوحة Leaflet -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    
    <!-- أيقونات FontAwesome لتشابه واجهة جوجل -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        /* حاوية الخريطة لتملأ الشاشة تماماً مثل جوجل ماب */
        #map {
            width: 100%;
            height: 100%;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }

        /* شريط البحث العلوي العائم (مطابق لتصميم جوجل) */
        .search-container {
            position: absolute;
            top: 15px;
            right: 20px;
            z-index: 1000;
            display: flex;
            align-items: center;
            background: #fff;
            width: 400px;
            height: 48px;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.3);
            padding: 0 15px;
        }

        .search-container i {
            color: #5f6368;
            font-size: 18px;
            margin-left: 10px;
        }

        .search-container input {
            flex: 1;
            border: none;
            outline: none;
            font-size: 16px;
            color: #202124;
            background: transparent;
        }

        .search-btn {
            background: #1b4d3e;
            color: white;
            border: none;
            padding: 6px 14px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
            font-size: 14px;
        }

        .search-btn:hover {
            background: #14382d;
        }

        /* شعار التطبيق العلوي الأيسر */
        .brand-logo {
            position: absolute;
            top: 15px;
            left: 20px;
            z-index: 1000;
            background: #fff;
            padding: 10px 18px;
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.3);
            font-weight: bold;
            color: #1b4d3e;
            font-size: 16px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .brand-logo i {
            color: #1b4d3e;
        }

        /* لوحة النتائج الجانبية (تظهر عند البحث) */
        .sidebar-results {
            position: absolute;
            top: 75px;
            right: 20px;
            z-index: 999;
            width: 400px;
            max-height: 70vh;
            background: white;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
            overflow-y: auto;
            display: none;
            padding: 10px;
        }

        .result-item {
            padding: 12px;
            border-bottom: 1px solid #eee;
            cursor: pointer;
            transition: background 0.2s;
        }

        .result-item:hover {
            background: #f8f9fa;
        }

        .result-title {
            font-weight: bold;
            color: #1a73e8;
            font-size: 15px;
            margin-bottom: 4px;
        }

        .result-address {
            color: #5f6368;
            font-size: 13px;
        }

        /* تخصيص مظهر إشعارات الخريطة */
        .leaflet-popup-content-wrapper {
            border-radius: 8px;
            padding: 5px;
        }
    </style>
</head>
<body>

    <!-- شعار Alislamiah Maps (بديل لشعار جوجل) -->
    <div class="brand-logo">
        <i class="fa-solid fa-kaaba"></i> Alislamiah Maps
    </div>

    <!-- شريط البحث العلوي -->
    <div class="search-container">
        <i class="fa-solid fa-magnifying-glass"></i>
        <input type="text" id="searchQuery" placeholder="البحث في Alislamiah Maps" onkeypress="handleKeyPress(event)">
        <button class="search-btn" onclick="searchLocation()">بحث</button>
    </div>

    <!-- قائمة نتائج البحث الجانبية -->
    <div id="sidebarResults" class="sidebar-results"></div>

    <!-- حاوية الخريطة الأساسية -->
    <div id="map"></div>

    <script>
        // 1. تهيئة الخريطة بمركز افتراضي (مثلاً مكة المكرمة كافتتاحية إسلامية)
        const map = L.map('map', {
            zoomControl: false // سنقوم بإعادة نقله ليكون تماماً مثل جوجل في الزاوية
        }).setView([21.4225, 39.8262], 14);

        // إضافة أزرار التكبير والتصغير في الزاوية اليمنى السفلية (مثل جوجل ماب)
        L.control.zoom({
            position: 'bottomleft'
        }).addTo(map);

        // 2. استخدام طبقة خرائط مفتوحة المصدر (بدون أي ارتباط بشركة جوجل)
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; <a href="#" style="color:#1b4d3e; text-decoration:none;">Alislamiah Maps</a> | بيانات الخريطة المساهمون'
        }).addTo(map);

        // متغير لتخزين العلامة الحالية
        let currentMarker = null;

        // 3. إضافة علامة ترحيبية للمسجد الحرام
        currentMarker = L.marker([21.4225, 39.8262]).addTo(map)
            .bindPopup('<b>Alislamiah Maps</b><br>المسجد الحرام، مكة المكرمة.')
            .openPopup();

        // 4. تفعيل البحث بالضغط على مفتاح Enter
        function handleKeyPress(e) {
            if (e.key === 'Enter') {
                searchLocation();
            }
        }

        // 5. وظيفة البحث المتقدم المطابقة لتجربة جوجل ماب
        function searchLocation() {
            const query = document.getElementById('searchQuery').value;
            const resultsContainer = document.getElementById('sidebarResults');
            
            if (!query.trim()) return;

            // استخدام محرك البحث المفتوح المرتبط بـ OpenStreetMap (بدون جوجل)
            fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(query)}`)
                .then(response => response.json())
                .then(data => {
                    resultsContainer.innerHTML = '';
                    if (data && data.length > 0) {
                        resultsContainer.style.display = 'block';
                        
                        data.forEach(place => {
                            const item = document.createElement('div');
                            item.className = 'result-item';
                            item.innerHTML = `
                                <div class="result-title"><i class="fa-solid fa-location-dot" style="margin-left:5px; color:#1b4d3e;"></i>${place.display_name.split(',')[0]}</div>
                                <div class="result-address">${place.display_name}</div>
                            `;
                            
                            // عند النقر على النتيجة، الانتقال إليها وتحديث الخريطة
                            item.onclick = function() {
                                const lat = parseFloat(place.lat);
                                const lon = parseFloat(place.lon);
                                
                                map.setView([lat, lon], 16);
                                
                                if (currentMarker) {
                                    map.removeLayer(currentMarker);
                                }
                                
                                currentMarker = L.marker([lat, lon]).addTo(map)
                                    .bindPopup(`<b>Alislamiah Maps</b><br>${place.display_name}`)
                                    .openPopup();
                                
                                resultsContainer.style.display = 'none';
                            };
                            
                            resultsContainer.appendChild(item);
                        });
                    } else {
                        resultsContainer.style.display = 'block';
                        resultsContainer.innerHTML = '<div style="padding: 15px; text-align: center; color: #5f6368;">لم يتم العثور على نتائج مطابقة في Alislamiah Maps</div>';
                    }
                })
                .catch(error => {
                    console.error('خطأ في الاتصال:', error);
                });
        }

        // إخفاء نتائج البحث عند النقر خارجها
        document.addEventListener('click', function(e) {
            const searchBox = document.querySelector('.search-container');
            const results = document.getElementById('sidebarResults');
            if (!searchBox.contains(e.target) && !results.contains(e.target)) {
                results.style.display = 'none';
            }
        });
    </script>
</body>
</html>
