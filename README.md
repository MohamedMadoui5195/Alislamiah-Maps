<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

  <title>Alislamiah Maps</title>

  <link rel="icon" href="icon.png" type="image/png">

  <!-- Leaflet -->
  <link
    rel="stylesheet"
    href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
  />

  <style>
    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
    }

    html,
    body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: Arial, Tahoma, sans-serif;
      background: #061426;
      color: white;
    }

    #map {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
      background: #09182b;
    }

    /* طبقة علوية */
    .top-area {
      position: fixed;
      top: 0;
      right: 0;
      left: 0;
      z-index: 1000;
      padding: 15px;
      pointer-events: none;
    }

    .top-bar {
      display: flex;
      align-items: center;
      gap: 10px;
      pointer-events: auto;
    }

    .menu-btn {
      width: 48px;
      height: 48px;
      border: 1px solid rgba(255,255,255,.12);
      border-radius: 16px;
      background: rgba(5, 20, 38, .94);
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 23px;
      box-shadow: 0 8px 25px rgba(0,0,0,.35);
      cursor: pointer;
      flex-shrink: 0;
    }

    .search-box {
      height: 52px;
      flex: 1;
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 0 15px;
      background: rgba(5, 20, 38, .96);
      border: 1px solid rgba(255,255,255,.12);
      border-radius: 17px;
      box-shadow: 0 8px 30px rgba(0,0,0,.4);
    }

    .search-icon {
      font-size: 19px;
      opacity: .85;
    }

    #searchInput {
      width: 100%;
      height: 100%;
      border: 0;
      outline: 0;
      background: transparent;
      color: white;
      font-size: 15px;
      text-align: right;
    }

    #searchInput::placeholder {
      color: #91a4bb;
    }

    .clear-btn {
      display: none;
      border: 0;
      background: transparent;
      color: #9fb0c5;
      font-size: 18px;
      cursor: pointer;
    }

    /* نتائج البحث */
    .search-results {
      position: absolute;
      top: 78px;
      right: 73px;
      left: 15px;
      max-height: 55vh;
      overflow-y: auto;
      display: none;
      background: rgba(5, 20, 38, .98);
      border: 1px solid rgba(255,255,255,.1);
      border-radius: 17px;
      box-shadow: 0 15px 40px rgba(0,0,0,.5);
      pointer-events: auto;
    }

    .result-item {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 14px;
      border-bottom: 1px solid rgba(255,255,255,.07);
      cursor: pointer;
    }

    .result-item:last-child {
      border-bottom: 0;
    }

    .result-item:active {
      background: rgba(255,255,255,.06);
    }

    .result-icon {
      width: 39px;
      height: 39px;
      border-radius: 12px;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #0b2f5c;
      color: #61a8ff;
      font-size: 18px;
      flex-shrink: 0;
    }

    .result-content {
      min-width: 0;
      flex: 1;
    }

    .result-title {
      font-size: 14px;
      font-weight: bold;
      margin-bottom: 5px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .result-address {
      font-size: 11px;
      color: #8ea2ba;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* أزرار الخريطة */
    .map-controls {
      position: fixed;
      left: 15px;
      bottom: 100px;
      z-index: 900;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .control-btn {
      width: 48px;
      height: 48px;
      border-radius: 16px;
      border: 1px solid rgba(255,255,255,.12);
      background: rgba(5,20,38,.95);
      color: white;
      font-size: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 8px 25px rgba(0,0,0,.35);
      cursor: pointer;
    }

    .control-btn.active {
      background: #0b4e9b;
    }

    /* بطاقة الموقع */
    .location-card {
      position: fixed;
      left: 15px;
      right: 15px;
      bottom: 78px;
      z-index: 950;
      display: none;
      background: rgba(5,20,38,.98);
      border: 1px solid rgba(255,255,255,.1);
      border-radius: 22px;
      padding: 16px;
      box-shadow: 0 15px 45px rgba(0,0,0,.55);
    }

    .location-header {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .place-icon {
      width: 48px;
      height: 48px;
      border-radius: 15px;
      background: #0b3d77;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 22px;
      flex-shrink: 0;
    }

    .place-info {
      flex: 1;
      min-width: 0;
    }

    .place-name {
      font-size: 16px;
      font-weight: bold;
      margin-bottom: 5px;
    }

    .place-address {
      font-size: 12px;
      color: #9aacc1;
      line-height: 1.5;
    }

    .close-card {
      width: 35px;
      height: 35px;
      border: 0;
      border-radius: 11px;
      background: rgba(255,255,255,.07);
      color: white;
      cursor: pointer;
      font-size: 17px;
    }

    .card-actions {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 9px;
      margin-top: 15px;
    }

    .action-btn {
      height: 43px;
      border: 0;
      border-radius: 13px;
      color: white;
      background: #0b315d;
      cursor: pointer;
      font-size: 13px;
      font-weight: bold;
    }

    .action-btn.primary {
      background: #0b63ce;
    }

    /* الشريط السفلي */
    .bottom-nav {
      position: fixed;
      bottom: 0;
      right: 0;
      left: 0;
      height: 68px;
      z-index: 1000;
      display: flex;
      align-items: center;
      justify-content: space-around;
      padding: 5px 8px calc(5px + env(safe-area-inset-bottom));
      background: rgba(4,16,31,.97);
      border-top: 1px solid rgba(255,255,255,.08);
      box-shadow: 0 -10px 30px rgba(0,0,0,.3);
    }

    .nav-item {
      width: 25%;
      height: 58px;
      border: 0;
      background: transparent;
      color: #8296ad;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 4px;
      cursor: pointer;
      font-size: 11px;
    }

    .nav-icon {
      font-size: 20px;
    }

    .nav-item.active {
      color: #5aa9ff;
    }

    /* شعار */
    .brand {
      position: fixed;
      top: 78px;
      right: 15px;
      z-index: 800;
      background: rgba(5,20,38,.9);
      border: 1px solid rgba(255,255,255,.1);
      padding: 8px 11px;
      border-radius: 13px;
      display: flex;
      align-items: center;
      gap: 7px;
      box-shadow: 0 7px 20px rgba(0,0,0,.3);
    }

    .brand img {
      width: 25px;
      height: 25px;
      object-fit: contain;
      border-radius: 7px;
    }

    .brand span {
      font-weight: bold;
      font-size: 12px;
    }

    /* رسالة الحالة */
    .status {
      position: fixed;
      top: 145px;
      right: 50%;
      transform: translateX(50%);
      z-index: 1200;
      padding: 10px 15px;
      border-radius: 13px;
      background: rgba(5,20,38,.97);
      color: #dce9f7;
      font-size: 12px;
      display: none;
      box-shadow: 0 10px 30px rgba(0,0,0,.35);
    }

    /* تحسين Leaflet */
    .leaflet-control-zoom {
      display: none;
    }

    .leaflet-control-attribution {
      background: rgba(4,16,31,.7) !important;
      color: #8497ad !important;
      font-size: 8px !important;
    }

    .leaflet-control-attribution a {
      color: #9ab8d7 !important;
    }

    /* علامة المستخدم */
    .user-marker {
      width: 20px;
      height: 20px;
      background: #1683ff;
      border: 4px solid white;
      border-radius: 50%;
      box-shadow:
        0 0 0 7px rgba(22,131,255,.22),
        0 3px 12px rgba(0,0,0,.45);
    }

    /* الوضع الفارغ للبحث */
    .no-results {
      padding: 22px;
      text-align: center;
      color: #899db5;
      font-size: 13px;
    }

    @media (min-width: 700px) {
      .top-area {
        max-width: 850px;
        margin: auto;
      }

      .search-results {
        right: calc(50% - 352px);
        left: calc(50% - 425px);
      }

      .location-card {
        max-width: 500px;
        left: 25px;
        right: auto;
      }

      .bottom-nav {
        max-width: 600px;
        left: 50%;
        right: auto;
        transform: translateX(-50%);
        border-radius: 20px 20px 0 0;
      }
    }
  </style>
</head>

<body>

  <!-- الخريطة -->
  <div id="map"></div>

  <!-- المنطقة العلوية -->
  <div class="top-area">

    <div class="top-bar">

      <button class="menu-btn" id="menuBtn" aria-label="القائمة">
        ☰
      </button>

      <div class="search-box">

        <span class="search-icon">⌕</span>

        <input
          id="searchInput"
          type="search"
          placeholder="ابحث عن مكان أو عنوان..."
          autocomplete="off"
        >

        <button class="clear-btn" id="clearBtn">×</button>

      </div>

    </div>

    <div class="search-results" id="searchResults"></div>

  </div>

  <!-- شعار Alislamiah -->
  <div class="brand">
    <img src="icon.png" alt="Alislamiah">
    <span>Alislamiah Maps</span>
  </div>

  <!-- أدوات الخريطة -->
  <div class="map-controls">

    <button class="control-btn" id="locationBtn" title="موقعي">
      ◎
    </button>

    <button class="control-btn" id="zoomIn" title="تكبير">
      +
    </button>

    <button class="control-btn" id="zoomOut" title="تصغير">
      −
    </button>

  </div>

  <!-- بطاقة المكان -->
  <div class="location-card" id="locationCard">

    <div class="location-header">

      <div class="place-icon" id="placeIcon">
        📍
      </div>

      <div class="place-info">

        <div class="place-name" id="placeName">
          الموقع
        </div>

        <div class="place-address" id="placeAddress">
          اختر مكانًا من الخريطة
        </div>

      </div>

      <button class="close-card" id="closeCard">
        ×
      </button>

    </div>

    <div class="card-actions">

      <button class="action-btn primary" id="directionsBtn">
        🧭 الاتجاهات
      </button>

      <button class="action-btn" id="shareBtn">
        ↗ مشاركة
      </button>

    </div>

  </div>

  <!-- رسالة الحالة -->
  <div class="status" id="status"></div>

  <!-- شريط التنقل -->
  <nav class="bottom-nav">

    <button class="nav-item active" id="homeNav">
      <span class="nav-icon">⌂</span>
      <span>الخريطة</span>
    </button>

    <button class="nav-item" id="exploreNav">
      <span class="nav-icon">✦</span>
      <span>استكشاف</span>
    </button>

    <button class="nav-item" id="savedNav">
      <span class="nav-icon">♡</span>
      <span>المحفوظات</span>
    </button>

    <button class="nav-item" id="settingsNav">
      <span class="nav-icon">⚙</span>
      <span>الإعدادات</span>
    </button>

  </nav>

  <!-- Leaflet -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <script>

    /* =========================
       إعداد الخريطة
    ========================= */

    const map = L.map("map", {
      zoomControl: false,
      attributionControl: true,
      minZoom: 2,
      maxZoom: 19
    }).setView([36.7538, 3.0588], 12);

    L.tileLayer(
      "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      {
        maxZoom: 19,
        attribution: '&copy; OpenStreetMap contributors'
      }
    ).addTo(map);


    /* =========================
       العناصر
    ========================= */

    const searchInput = document.getElementById("searchInput");
    const clearBtn = document.getElementById("clearBtn");
    const searchResults = document.getElementById("searchResults");

    const locationBtn = document.getElementById("locationBtn");

    const locationCard = document.getElementById("locationCard");
    const closeCard = document.getElementById("closeCard");

    const placeName = document.getElementById("placeName");
    const placeAddress = document.getElementById("placeAddress");

    const directionsBtn = document.getElementById("directionsBtn");
    const shareBtn = document.getElementById("shareBtn");

    const statusBox = document.getElementById("status");

    let userMarker = null;
    let selectedLat = null;
    let selectedLon = null;


    /* =========================
       أدوات مساعدة
    ========================= */

    function showStatus(message, duration = 2500) {

      statusBox.textContent = message;
      statusBox.style.display = "block";

      clearTimeout(window.statusTimer);

      window.statusTimer = setTimeout(() => {
        statusBox.style.display = "none";
      }, duration);
    }


    function escapeHTML(text) {

      return String(text || "")
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");
    }


    /* =========================
       زر التكبير والتصغير
    ========================= */

    document.getElementById("zoomIn").onclick = () => {
      map.zoomIn();
    };

    document.getElementById("zoomOut").onclick = () => {
      map.zoomOut();
    };


    /* =========================
       البحث في OpenStreetMap
    ========================= */

    let searchTimer = null;

    searchInput.addEventListener("input", () => {

      const query = searchInput.value.trim();

      clearBtn.style.display = query ? "block" : "none";

      clearTimeout(searchTimer);

      if (!query) {
        searchResults.style.display = "none";
        searchResults.innerHTML = "";
        return;
      }

      searchTimer = setTimeout(() => {
        searchPlaces(query);
      }, 600);

    });


    async function searchPlaces(query) {

      searchResults.style.display = "block";

      searchResults.innerHTML =
        '<div class="no-results">جاري البحث...</div>';

      try {

        const url =
          "https://nominatim.openstreetmap.org/search" +
          "?format=jsonv2" +
          "&q=" + encodeURIComponent(query) +
          "&limit=8" +
          "&accept-language=ar";

        const response = await fetch(url, {
          headers: {
            "Accept": "application/json"
          }
        });

        if (!response.ok) {
          throw new Error("Search failed");
        }

        const data = await response.json();

        if (!data.length) {

          searchResults.innerHTML =
            '<div class="no-results">لم يتم العثور على نتائج</div>';

          return;
        }

        searchResults.innerHTML = "";

        data.forEach(place => {

          const item = document.createElement("div");

          item.className = "result-item";

          item.innerHTML = `
            <div class="result-icon">📍</div>

            <div class="result-content">

              <div class="result-title">
                ${escapeHTML(
                  place.name ||
                  place.display_name.split(",")[0]
                )}
              </div>

              <div class="result-address">
                ${escapeHTML(place.display_name)}
              </div>

            </div>
          `;

          item.addEventListener("click", () => {

            const lat = parseFloat(place.lat);
            const lon = parseFloat(place.lon);

            map.setView([lat, lon], 16);

            selectedLat = lat;
            selectedLon = lon;

            showPlace(
              place.name || "المكان",
              place.display_name
            );

            searchResults.style.display = "none";

          });

          searchResults.appendChild(item);

        });

      } catch (error) {

        searchResults.innerHTML =
          '<div class="no-results">تعذر إجراء البحث. تحقق من اتصال الإنترنت.</div>';

      }

    }


    /* =========================
       تنظيف البحث
    ========================= */

    clearBtn.onclick = () => {

      searchInput.value = "";

      clearBtn.style.display = "none";

      searchResults.style.display = "none";

      searchResults.innerHTML = "";

      searchInput.focus();

    };


    /* =========================
       الضغط على الخريطة
    ========================= */

    map.on("click", async function(e) {

      const lat = e.latlng.lat;
      const lon = e.latlng.lng;

      selectedLat = lat;
      selectedLon = lon;

      showPlace(
        "جارٍ تحديد المكان...",
        "..."
      );

      try {

        const url =
          "https://nominatim.openstreetmap.org/reverse" +
          "?format=jsonv2" +
          "&lat=" + lat +
          "&lon=" + lon +
          "&accept-language=ar";

        const response = await fetch(url);

        const data = await response.json();

        showPlace(
          data.name ||
          "موقع على الخريطة",
          data.display_name ||
          `${lat.toFixed(5)}, ${lon.toFixed(5)}`
        );

      } catch {

        showPlace(
          "موقع على الخريطة",
          `${lat.toFixed(5)}, ${lon.toFixed(5)}`
        );

      }

    });


    /* =========================
       عرض بطاقة المكان
    ========================= */

    function showPlace(name, address) {

      placeName.textContent = name || "المكان";

      placeAddress.textContent = address || "";

      locationCard.style.display = "block";

    }


    closeCard.onclick = () => {

      locationCard.style.display = "none";

    };


    /* =========================
       تحديد موقع المستخدم
    ========================= */

    locationBtn.onclick = () => {

      if (!navigator.geolocation) {

        showStatus("المتصفح لا يدعم تحديد الموقع");

        return;
      }

      showStatus("جارٍ تحديد موقعك...", 5000);

      navigator.geolocation.getCurrentPosition(

        position => {

          const lat = position.coords.latitude;
          const lon = position.coords.longitude;

          selectedLat = lat;
          selectedLon = lon;

          map.setView([lat, lon], 16);

          if (userMarker) {
            map.removeLayer(userMarker);
          }

          const userIcon = L.divIcon({
            className: "",
            html: '<div class="user-marker"></div>',
            iconSize: [20,20],
            iconAnchor: [10,10]
          });

          userMarker = L.marker(
            [lat, lon],
            { icon: userIcon }
          ).addTo(map);

          showPlace(
            "موقعك الحالي",
            `خط العرض: ${lat.toFixed(5)} • خط الطول: ${lon.toFixed(5)}`
          );

          locationBtn.classList.add("active");

          showStatus("تم تحديد موقعك");

        },

        error => {

          locationBtn.classList.remove("active");

          if (error.code === 1) {

            showStatus(
              "لم يتم السماح بالوصول إلى موقعك"
            );

          } else {

            showStatus(
              "تعذر تحديد موقعك"
            );

          }

        },

        {
          enableHighAccuracy: true,
          timeout: 10000,
          maximumAge: 0
        }

      );

    };


    /* =========================
       الاتجاهات
    ========================= */

    directionsBtn.onclick = () => {

      if (
        selectedLat === null ||
        selectedLon === null
      ) {

        showStatus("حدد مكانًا أولًا");

        return;
      }

      if (!userMarker) {

        showStatus(
          "حدد موقعك أولًا للحصول على الاتجاهات"
        );

        locationBtn.click();

        return;
      }

      const userPosition =
        userMarker.getLatLng();

      const url =
        "https://www.openstreetm
ap.org/directions" +
        "?engine=fossgis_osrm_car" +
        "&route=" +
        userPosition.lat + "," +
        userPosition.lng + ";" +
        selectedLat + "," +
        selectedLon;

      window.open(url, "_blank");

    };


    /* =========================
       مشاركة المكان
    ========================= */

    shareBtn.onclick = async () => {

      if (
        selectedLat === null ||
        selectedLon === null
      ) {

        showStatus("حدد مكانًا أولًا");

        return;
      }

      const shareUrl =
        `https://www.openstreetmap.org/?mlat=${selectedLat}&mlon=${selectedLon}#map=17/${selectedLat}/${selectedLon}`;

      try {

        if (navigator.share) {

          await navigator.share({
            title: "Alislamiah Maps",
            text: "موقع من Alislamiah Maps",
            url: shareUrl
          });

        } else {

          await navigator.clipboard.writeText(shareUrl);

          showStatus("تم نسخ رابط المكان");

        }

      } catch (error) {

        // تم إلغاء المشاركة
      }

    };


    /* =========================
       زر القائمة
    ========================= */

    document.getElementById("menuBtn").onclick = () => {

      showStatus("قائمة Alislamiah Maps");

    };


    /* =========================
       التنقل السفلي
    ========================= */

    const navItems =
      document.querySelectorAll(".nav-item");

    navItems.forEach(item => {

      item.addEventListener("click", () => {

        navItems.forEach(nav => {
          nav.classList.remove("active");
        });

        item.classList.add("active");

      });

    });


    /* =========================
       الخريطة
    ========================= */

    document.getElementById("homeNav").onclick = () => {

      map.setView(
        [36.7538, 3.0588],
        12
      );

    };


    /* =========================
       الاستكشاف
    ========================= */

    document.getElementById("exploreNav").onclick = () => {

      searchInput.focus();

      showStatus(
        "اكتب اسم المكان الذي تريد استكشافه"
      );

    };


    /* =========================
       المحفوظات
    ========================= */

    document.getElementById("savedNav").onclick = () => {

      showStatus(
        "المحفوظات ستكون متاحة قريبًا"
      );

    };


    /* =========================
       الإعدادات
    ========================= */

    document.getElementById("settingsNav").onclick = () => {

      showStatus(
        "إعدادات Alislamiah Maps"
      );

    };


    /* =========================
       إغلاق نتائج البحث
    ========================= */

    document.addEventListener("click", event => {

      const searchBox =
        document.querySelector(".search-box");

      if (
        !searchBox.contains(event.target) &&
        !searchResults.contains(event.target)
      ) {

        searchResults.style.display = "none";

      }

    });


  </script>

</body>
</html>