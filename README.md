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
      width: 100%;
      height: 100%;
      margin: 0;
      padding: 0;
      overflow: hidden;
      font-family: Arial, "Tajawal", sans-serif;
      background: #06101f;
    }

    body {
      position: relative;
    }

    /* =========================
       الخريطة
    ========================= */

    #map {
      position: absolute;
      inset: 0;
      z-index: 1;
      background: #101820;
    }

    .leaflet-control-attribution {
      font-size: 9px !important;
      background: rgba(5, 13, 25, 0.78) !important;
      color: #b8c7d9 !important;
    }

    .leaflet-control-attribution a {
      color: #80bfff !important;
    }

    /* =========================
       الرأس
    ========================= */

    .top-area {
      position: absolute;
      top: 0;
      right: 0;
      left: 0;
      z-index: 1000;
      padding: 12px;
      pointer-events: none;
    }

    .top-bar {
      width: 100%;
      max-width: 720px;
      margin: auto;
      display: flex;
      align-items: center;
      gap: 9px;
      pointer-events: auto;
    }

    .brand {
      width: 51px;
      height: 51px;
      flex-shrink: 0;
      border-radius: 16px;
      padding: 6px;
      background: rgba(5, 17, 34, 0.94);
      border: 1px solid rgba(77, 153, 255, 0.32);
      box-shadow: 0 8px 25px rgba(0, 0, 0, 0.35);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .brand img {
      width: 100%;
      height: 100%;
      object-fit: contain;
      border-radius: 11px;
    }

    .search-box {
      flex: 1;
      min-width: 0;
      height: 51px;
      display: flex;
      align-items: center;
      background: rgba(5, 17, 34, 0.95);
      border: 1px solid rgba(77, 153, 255, 0.35);
      border-radius: 17px;
      box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
      overflow: hidden;
    }

    .search-icon {
      width: 48px;
      height: 100%;
      border: 0;
      background: transparent;
      color: #8fb8e8;
      font-size: 20px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }

    #searchInput {
      flex: 1;
      min-width: 0;
      height: 100%;
      border: 0;
      outline: none;
      background: transparent;
      color: white;
      font-size: 15px;
      text-align: right;
      padding: 0 5px;
    }

    #searchInput::placeholder {
      color: #8c9db3;
    }

    .clear-btn {
      width: 40px;
      height: 100%;
      border: 0;
      background: transparent;
      color: #91a3b8;
      font-size: 20px;
      display: none;
      cursor: pointer;
    }

    /* =========================
       لوحة البحث
    ========================= */

    #searchResults {
      position: absolute;
      top: 75px;
      right: 12px;
      left: 12px;
      max-width: 720px;
      margin: auto;
      max-height: 48vh;
      overflow-y: auto;
      background: rgba(5, 16, 31, 0.97);
      border: 1px solid rgba(77, 153, 255, 0.25);
      border-radius: 18px;
      box-shadow: 0 15px 45px rgba(0, 0, 0, 0.5);
      display: none;
      pointer-events: auto;
      scrollbar-width: thin;
    }

    .result-item {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 14px;
      border-bottom: 1px solid rgba(255, 255, 255, 0.07);
      cursor: pointer;
      color: white;
    }

    .result-item:last-child {
      border-bottom: 0;
    }

    .result-item:active {
      background: rgba(55, 125, 220, 0.14);
    }

    .result-icon {
      width: 43px;
      height: 43px;
      flex-shrink: 0;
      border-radius: 13px;
      background: #0c2b52;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
    }

    .result-text {
      min-width: 0;
      flex: 1;
    }

    .result-name {
      font-weight: bold;
      font-size: 14px;
      margin-bottom: 5px;
      color: #fff;
    }

    .result-address {
      color: #9fb0c5;
      font-size: 12px;
      line-height: 1.5;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    /* =========================
       أزرار الخريطة
    ========================= */

    .map-tools {
      position: absolute;
      z-index: 900;
      left: 13px;
      bottom: 110px;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .tool-btn {
      width: 49px;
      height: 49px;
      border: 1px solid rgba(78, 153, 255, 0.28);
      border-radius: 16px;
      background: rgba(5, 17, 34, 0.94);
      color: white;
      box-shadow: 0 7px 24px rgba(0, 0, 0, 0.35);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      cursor: pointer;
    }

    .tool-btn:active {
      background: #0d3768;
    }

    /* =========================
       لوحة الاتجاهات
    ========================= */

    #directionsPanel {
      position: absolute;
      z-index: 1100;
      right: 12px;
      left: 12px;
      bottom: 15px;
      max-width: 720px;
      margin: auto;
      background: rgba(5, 16, 31, 0.97);
      border: 1px solid rgba(75, 153, 255, 0.28);
      border-radius: 21px;
      box-shadow: 0 15px 45px rgba(0, 0, 0, 0.55);
      padding: 15px;
      display: none;
      color: white;
    }

    .direction-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 12px;
    }

    .direction-title {
      font-weight: bold;
      font-size: 16px;
    }

    .close-direction {
      width: 34px;
      height: 34px;
      border: 0;
      border-radius: 11px;
      background: rgba(255, 255, 255, 0.07);
      color: white;
      cursor: pointer;
      font-size: 18px;
    }

    .route-inputs {
      display: flex;
      flex-direction: column;
      gap: 8px;
      margin-bottom: 11px;
    }

    .route-input {
      height: 43px;
      border-radius: 12px;
      border: 1px solid rgba(100, 160, 230, 0.2);
      background: #091d36;
      color: white;
      outline: none;
      padding: 0 12px;
      font-size: 13px;
      width: 100%;
    }

    .route-input::placeholder {
      color: #8193a9;
    }

    .route-actions {
      display: flex;
      gap: 8px;
    }

    .route-btn {
      flex: 1;
      height: 44px;
      border: 0;
      border-radius: 13px;
      cursor: pointer;
      font-size: 14px;
      font-weight: bold;
    }

    .route-main {
      background: #1264c4;
      color: white;
    }

    .route-secondary {
      background: #102a49;
      color: #bcd7f5;
    }

    #routeInfo {
      display: none;
      margin-top: 12px;
      padding: 12px;
      border-radius: 13px;
      background: #091c34;
    }

    .route-stats {
      display: flex;
      gap: 10px;
    }

    .route-stat {
      flex: 1;
      text-align: center;
    }

    .route-stat strong {
      display: block;
      font-size: 16px;
      color: white;
    }

    .route-stat span {
      display: block;
      margin-top: 4px;
      color: #849ab4;
      font-size: 11px;
    }

    /* =========================
       بطاقة الموقع
    ========================= */

    .location-card {
      position: absolute;
      z-index: 800;
      right: 13px;
      bottom: 17px;
      max-width: 330px;
      padding: 12px 14px;
      background: rgba(5, 17, 34, 0.94);
      border: 1px solid rgba(77, 153, 255, 0.25);
      border-radius: 15px;
      color: white;
      box-shadow: 0 8px 28px rgba(0, 0, 0, 0.4);
      display: none;
    }

    .location-card strong {
      font-size: 13px;
    }

    .location-card div {
      margin-top: 4px;
      color: #91a6bf;
      font-size: 11px;
    }

    /* =========================
       Marker
    ========================= */

    .user-marker {
      width: 20px;
      height: 20px;
      border-radius: 50%;
      background: #198cff;
      border: 4px solid white;
      box-shadow:
        0 0 0 6px rgba(25, 140, 255, 0.18),
        0 3px 12px rgba(0, 0, 0, 0.45);
    }

    /* =========================
       تحميل
    ========================= */

    #loading {
      position: fixed;
      inset: 0;
      z-index: 3000;
      background: #050f1d;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      color: white;
    }

    #loading img {
      width: 76px;
      height: 76px;
      object-fit: contain;
      border-radius: 20px;
      margin-bottom: 18px;
    }

    #loading strong {
      font-size: 18px;
    }

    #loading span {
      margin-top: 8px;
      color: #7e93ad;
      font-size: 12px;
    }

    .hidden {
      display: none !important;
    }

    /* =========================
       رسالة صغيرة
    ========================= */

    #toast {
      position: fixed;
      z-index: 4000;
      left: 50%;
      bottom: 25px;
      transform: translateX(-50%);
      background: rgba(5, 17, 34, 0.96);
      color: white;
      border: 1px solid rgba(78, 153, 255, 0.3);
      border-radius: 13px;
      padding: 11px 16px;
      font-size: 12px;
      box-shadow: 0 8px 30px rgba(0,0,0,.4);
      display: none;
      white-space: nowrap;
    }

    /* =========================
       سطح المكتب
    ========================= */

    @media (min-width: 800px) {
      .top-area {
        padding: 18px;
      }

      .brand {
        width: 56px;
        height: 56px;
      }

      .search-box {
        height: 56px;
      }

      #searchResults {
        top: 88px;
      }

      .map-tools {
        bottom: 35px;
      }

      #directionsPanel {
        right: 20px;
        left: auto;
        width: 380px;
        bottom: 25px;
        margin: 0;
      }
    }
  </style>
</head>

<body>

  <!-- شاشة التحميل -->
  <div id="loading">
    <img src="icon.png" alt="Alislamiah">
    <strong>Alislamiah Maps</strong>
    <span>جارٍ تحميل الخريطة...</span>
  </div>

  <!-- الخريطة -->
  <div id="map"></div>

  <!-- الرأس -->
  <div class="top-area">

    <div class="top-bar">

      <div class="brand">
        <img src="icon.png" alt="Alislamiah Maps">
      </div>

      <div class="search-box">

        <button class="search-icon" id="searchButton" aria-label="بحث">
          🔍
        </button>

        <input
          id="searchInput"
          type="search"
          placeholder="ابحث عن مكان أو شارع أو حي..."
          autocomplete="off"
        >

        <button
          class="clear-btn"
          id="clearButton"
          aria-label="مسح"
        >
          ×
        </button>

      </div>

    </div>

    <!-- نتائج البحث -->
    <div id="searchResults"></div>

  </div>

  <!-- أدوات الخريطة -->
  <div class="map-tools">

    <button
      class="tool-btn"
      id="myLocation"
      title="موقعي"
      aria-label="موقعي"
    >
      📍
    </button>

    <button
      class="tool-btn"
      id="zoomIn"
      title="تكبير"
      aria-label="تكبير"
    >
      ＋
    </button>

    <button
      class="tool-btn"
      id="zoomOut"
      title="تصغير"
      aria-label="تصغير"
    >
      −
    </button>

    <button
      class="tool-btn"
      id="routeButton"
      title="المسار"
      aria-label="المسار"
    >
      🧭
    </button>

  </div>

  <!-- معلومات الموقع -->
  <div class="location-card" id="locationCard">
    <strong>موقعك الحالي</strong>
    <div id="locationCoordinates"></div>
  </div>

  <!-- لوحة الاتجاهات -->
  <div id="directionsPanel">

    <div class="direction-header">
      <div class="direction-title">🧭 الاتجاهات</div>

      <button
        class="close-direction"
        id="closeDirection"
        aria-label="إغلاق"
      >
        ×
      </button>
    </div>

    <div class="route-inputs">

      <input
        id="startInput"
        class="route-input"
        type="text"
        placeholder="نقطة الانطلاق"
      >

      <input
        id="destinationInput"
        class="route-input"
        type="text"
        placeholder="الوجهة"
      >

    </div>

    <div class="route-actions">

      <button
        class="route-btn route-secondary"
        id="useMyLocation"
      >
        📍 موقعي
      </button>

      <button
        class="route-btn route-main"
        id="calculateRoute"
      >
        عرض المسار
      </button>

    </div>

    <div id="routeInfo">

      <div class="route-stats">

        <div class="route-stat">
          <strong id="routeDistance">—</strong>
          <span>المسافة</span>
        </div>

        <div class="route-stat">
          <strong id="routeDuration">—</strong>
          <span>الوقت التقريبي</span>
        </div>

      </div>

    </div>

  </div>

  <div id="toast"></div>

  <!-- Leaflet -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <script>
    /*
      ==========================================
      Alislamiah Maps
      OpenStreetMap + Leaflet + Nominatim + OSRM
      ==========================================
    */

    const map = L.map("map", {
      zoomControl: false,
      attributionControl: true,
      preferCanvas: true
    });

    /*
      طبقة الخريطة
    */
    L.tileLayer(
      "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      {
        maxZoom: 20,
        minZoom: 2,
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap</a>'
      }
    ).addTo(map);


    /*
      الموقع الافتراضي:
      الجزائر العاصمة
    */
    const defaultLocation = [36.7538, 3.0588];

    map.setView(defaultLocation, 13);


    /*
      المتغيرات
    */
    let userMarker = null;
    let userCircle = null;

    let searchMarker = null;

    let routeLayer = null;

    let currentUserLocation = null;

    let searchTimer = null;


    /*
      إزالة شاشة التحميل
    */
    setTimeout(() => {
      document.getElementById("loading").classList.add("hidden");
    }, 900);


    /*
      عناصر الواجهة
    */
    const searchInput =
      document.getElementById("searchInput");

    const searchResults =
      document.getElementById("searchResults");

    const clearButton =
      document.getElementById("clearButton");

    const searchButton =
      document.getElementById("searchButton");

    const directionsPanel =
      document.getElementById("directionsPanel");

    const startInput =
      document.getElementById("startInput");

    const destinationInput =
      document.getElementById("destinationInput");


    /*
      Toast
    */
    function showToast(message) {

      const toast =
        document.getElementById("toast");

      toast.textContent = message;
      toast.style.display = "block";

      clearTimeout(window.toastTimer);

      window.toastTimer = setTimeout(() => {
        toast.style.display = "none";
      }, 3000);
    }


    /*
      زر التكبير
    */
    document.getElementById("zoomIn")
      .addEventListener("click", () => {
        map.zoomIn();
      });


    /*
      زر التصغير
    */
    document.getElementById("zoomOut")
      .addEventListener("click", () => {
        map.zoomOut();
      });


    /*
      البحث
    */
    searchInput.addEventListener("input", () => {

      const value =
        searchInput.value.trim();

      clearButton.style.display =
        value ? "block" : "none";

      clearTimeout(searchTimer);

      if (!value) {
        searchResults.style.display = "none";
        searchResults.innerHTML = "";
        return;
      }

      /*
        انتظار بسيط حتى لا نرسل طلباً
        مع كل حرف يكتبه المستخدم.
      */
      searchTimer = setTimeout(() => {
        searchPlaces(value);
      }, 500);

    });


    /*
      Enter
    */
    searchInput.addEventListener("keydown", (event) => {

      if (event.key === "Enter") {

        event.preventDefault();

        const value =
          searchInput.value.trim();

        if (value) {
          searchPlaces(value);
        }

      }

    });


    /*
      زر البحث
    */
    searchButton.addEventListener("click", () => {

      const value =
        searchInput.value.trim();

      if (value) {
        searchPlaces(value);
      }

    });


    /*
      مسح البحث
    */
    clearButton.addEventListener("click", () => {

      searchInput.value = "";

      clearButton.style.display = "none";

      searchResults.innerHTML = "";

      searchResults.style.display = "none";

      if (searchMarker) {
        map.removeLayer(searchMarker);
        searchMarker = null;
      }

    });


    /*
      Nominatim
    */
    async function searchPlaces(query) {

      searchResults.style.display = "block";

      searchResults.innerHTML = `
        <div style="
          padding:20px;
          text-align:center;
          color:#91a6bf;
          font-size:13px;
        ">
          🔎 جارٍ البحث...
        </div>
      `;

      try {

        const url =
          "https://nominatim.openstreetmap.org/search" +
          "?format=jsonv2" +
          "&addressdetails=1" +
          "&limit=8" +
          "&accept-language=ar" +
          "&q=" +
          encodeURIComponent(query);

        const response =
          await fetch(url, {
            headers: {
              "Accept": "application/json"
            }
          });

        if (!response.ok) {
          throw new Error("Search error");
        }

        const data =
          await response.json();

        renderSearchResults(data);

      } catch (error) {

        searchResults.innerHTML = `
          <div style="
            padding:20px;
            text-align:center;
            color:#ff9b9b;
            font-size:13px;
          ">
            تعذر إجراء البحث حالياً.
          </div>
        `;

      }

    }


    /*
      عرض نتائج البحث
    */
    function renderSearchResults(results) {

      if (!results.length) {

        searchResults.innerHTML = `
          <div style="
            padding:20px;
            text-align:center;
            color:#91a6bf;
            font-size:13px;
          ">
            لم يتم العثور على نتائج.
          </div>
        `;

        return;
      }


      searchResults.innerHTML = "";


      results.forEach((place) => {

        const item =
          document.createElement("div");

        item.className =
          "result-item";


        const icon =
          document.createElement("div");

        icon.className =
          "result-icon";

        icon.textContent =
          getPlaceIcon(place);


        const text =
          document.createElement("div");

        text.className =
          "result-text";


        const name =
          document.createElement("div");

        name.className =
          "result-name";

        name.textContent =
          getPlaceName(place);


        const address =
          document.createElement("div");

        address.className =
          "result-address";

        address.textContent =
          place.display_name || "";


        text.appendChild(name);
        text.appendChild(address);

        item.appendChild(icon);
        item.appendChild(text);


        item.addEventListener("click", () => {

          selectSearchResult(place);

        });


        searchResults.appendChild(item);

      });

    }


    /*
      أيقونة المكان
    */
    function getPlaceIcon(place) {

      const type =
        place.type || "";

      if (
        type.includes("restaurant") ||
        type.includes("cafe") ||
        type.includes("fast_food")
      ) {
        return "🍽️";
      }

           if (
        type.includes("hospital") ||
        type.includes("clinic") ||
        type.includes("doctors")
      ) {
        return "🏥";
      }

      if (
        type.includes("school") ||
        type.includes("university") ||
        type.includes("college")
      ) {
        return "🎓";
      }

      if (
        type.includes("hotel") ||
        type.includes("guest_house")
      ) {
        return "🏨";
      }

      if (
        type.includes("shop") ||
        type.includes("supermarket") ||
        type.includes("mall")
      ) {
        return "🛍️";
      }

      if (
        type.includes("bank") ||
        type.includes("atm")
      ) {
        return "🏦";
      }

      if (
        type.includes("pharmacy")
      ) {
        return "💊";
      }

      if (
        type.includes("mosque")
      ) {
        return "🕌";
      }

      if (
        type.includes("place") ||
        type.includes("city") ||
        type.includes("town") ||
        type.includes("village")
      ) {
        return "📍";
      }

      if (
        type.includes("road") ||
        type.includes("street")
      ) {
        return "🛣️";
      }

      return "📌";
    }


    /*
      اسم المكان
    */
    function getPlaceName(place) {

      if (
        place.namedetails &&
        place.namedetails.name
      ) {
        return place.namedetails.name;
      }

      if (place.name) {
        return place.name;
      }

      return (
        place.display_name ||
        "موقع"
      ).split(",")[0];

    }


    /*
      اختيار نتيجة البحث
    */
    function selectSearchResult(place) {

      const lat =
        parseFloat(place.lat);

      const lon =
        parseFloat(place.lon);


      map.setView(
        [lat, lon],
        Math.max(map.getZoom(), 16),
        {
          animate: false
        }
      );


      if (searchMarker) {
        map.removeLayer(searchMarker);
      }


      searchMarker =
        L.marker([lat, lon])
          .addTo(map)
          .bindPopup(`
            <div dir="rtl" style="
              min-width:190px;
              font-family:Arial;
            ">

              <strong style="
                display:block;
                font-size:15px;
                color:#111;
                margin-bottom:6px;
              ">
                ${escapeHTML(getPlaceName(place))}
              </strong>

              <div style="
                color:#555;
                font-size:12px;
                line-height:1.6;
              ">
                ${escapeHTML(place.display_name || "")}
              </div>

              <button
                onclick="openDirectionsFromSearch(${lat}, ${lon})"
                style="
                  margin-top:10px;
                  width:100%;
                  border:0;
                  padding:10px;
                  border-radius:9px;
                  background:#1264c4;
                  color:white;
                  font-weight:bold;
                  cursor:pointer;
                "
              >
                🧭 الحصول على الاتجاهات
              </button>

            </div>
          `)
          .openPopup();


      searchResults.style.display = "none";


      destinationInput.value =
        getPlaceName(place);

      destinationInput.dataset.lat =
        lat;

      destinationInput.dataset.lon =
        lon;

    }


    /*
      حماية النصوص
    */
    function escapeHTML(value) {

      return String(value)
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;");

    }


    /*
      فتح الاتجاهات من نتيجة البحث
    */
    window.openDirectionsFromSearch =
      function(lat, lon) {

        directionsPanel.style.display =
          "block";


        destinationInput.dataset.lat =
          lat;

        destinationInput.dataset.lon =
          lon;


        if (currentUserLocation) {

          startInput.value =
            "موقعي الحالي";

          startInput.dataset.lat =
            currentUserLocation[0];

          startInput.dataset.lon =
            currentUserLocation[1];

        }

      };


    /*
      زر الاتجاهات
    */
    document.getElementById("routeButton")
      .addEventListener("click", () => {

        if (
          directionsPanel.style.display ===
          "block"
        ) {

          directionsPanel.style.display =
            "none";

        } else {

          directionsPanel.style.display =
            "block";

        }

      });


    /*
      إغلاق الاتجاهات
    */
    document.getElementById("closeDirection")
      .addEventListener("click", () => {

        directionsPanel.style.display =
          "none";

      });


    /*
      زر موقعي
    */
    document.getElementById("myLocation")
      .addEventListener("click", () => {

        locateUser(true);

      });


    /*
      تحديد موقع المستخدم
    */
    function locateUser(showMessage = false) {

      if (!navigator.geolocation) {

        showToast(
          "هذا الجهاز لا يدعم تحديد الموقع."
        );

        return;

      }


      if (showMessage) {

        showToast(
          "جارٍ تحديد موقعك..."
        );

      }


      navigator.geolocation.getCurrentPosition(

        function(position) {

          const lat =
            position.coords.latitude;

          const lon =
            position.coords.longitude;

          const accuracy =
            position.coords.accuracy;


          currentUserLocation =
            [lat, lon];


          /*
            أيقونة موقع المستخدم
          */
          const userIcon =
            L.divIcon({
              className: "",
              html:
                '<div class="user-marker"></div>',
              iconSize: [20, 20],
              iconAnchor: [10, 10]
            });


          if (userMarker) {

            userMarker.setLatLng(
              [lat, lon]
            );

          } else {

            userMarker =
              L.marker(
                [lat, lon],
                {
                  icon: userIcon,
                  zIndexOffset: 1000
                }
              ).addTo(map);

          }


          /*
            دائرة دقة الموقع
          */
          if (userCircle) {

            userCircle.setLatLng(
              [lat, lon]
            );

            userCircle.setRadius(
              accuracy
            );

          } else {

            userCircle =
              L.circle(
                [lat, lon],
                {
                  radius: accuracy,
                  color: "#198cff",
                  fillColor: "#198cff",
                  fillOpacity: 0.08,
                  weight: 1
                }
              ).addTo(map);

          }


          /*
            الانتقال إلى موقع المستخدم
          */
          map.setView(
            [lat, lon],
            17,
            {
              animate: false
            }
          );


          /*
            معلومات الموقع
          */
          document.getElementById(
            "locationCoordinates"
          ).textContent =
            `${lat.toFixed(6)}, ${lon.toFixed(6)} • دقة تقريبية ${Math.round(accuracy)} م`;


          document.getElementById(
            "locationCard"
          ).style.display =
            "block";


          /*
            تجهيز نقطة البداية
          */
          startInput.value =
            "موقعي الحالي";

          startInput.dataset.lat =
            lat;

          startInput.dataset.lon =
            lon;


          if (showMessage) {

            showToast(
              "تم تحديد موقعك بنجاح."
            );

          }

        },

        function(error) {

          let message =
            "تعذر تحديد موقعك.";

          if (error.code === 1) {

            message =
              "يرجى السماح بالوصول إلى موقعك.";

          } else if (error.code === 2) {

            message =
              "تعذر الحصول على موقعك.";

          } else if (error.code === 3) {

            message =
              "انتهت مهلة تحديد الموقع.";

          }


          showToast(message);

        },

        {
          enableHighAccuracy: true,
          timeout: 15000,
          maximumAge: 0
        }

      );

    }


    /*
      زر استخدام موقعي
    */
    document.getElementById("useMyLocation")
      .addEventListener("click", () => {

        if (currentUserLocation) {

          startInput.value =
            "موقعي الحالي";

          startInput.dataset.lat =
            currentUserLocation[0];

          startInput.dataset.lon =
            currentUserLocation[1];

          showToast(
            "تم استخدام موقعك كنقطة انطلاق."
          );

        } else {

          locateUser(true);

        }

      });


    /*
      حساب المسار
    */
    document.getElementById("calculateRoute")
      .addEventListener("click", async () => {

        let startLat =
          parseFloat(
            startInput.dataset.lat
          );

        let startLon =
          parseFloat(
            startInput.dataset.lon
          );


        let endLat =
          parseFloat(
            destinationInput.dataset.lat
          );

        let endLon =
          parseFloat(
            destinationInput.dataset.lon
          );


        /*
          إذا لم توجد إحداثيات للبداية
        */
        if (
          !Number.isFinite(startLat) ||
          !Number.isFinite(startLon)
        ) {

          if (
            startInput.value.trim() ===
            "موقعي الحالي"
          ) {

            if (!currentUserLocation) {

              locateUser(true);

              showToast(
                "اسمح بالوصول إلى موقعك ثم اضغط عرض المسار."
              );

              return;

            }


            startLat =
              currentUserLocation[0];

            startLon =
              currentUserLocation[1];

          } else {

            const start =
              await geocodeAddress(
                startInput.value.trim()
              );


            if (!start) {

              showToast(
                "لم يتم العثور على نقطة الانطلاق."
              );

              return;

            }


            startLat =
              start.lat;

            startLon =
              start.lon;

          }

        }


        /*
          إذا لم توجد إحداثيات للوجهة
        */
        if (
          !Number.isFinite(endLat) ||
          !Number.isFinite(endLon)
        ) {

          const destination =
            await geocodeAddress(
              destinationInput.value.trim()
            );


          if (!destination) {

            showToast(
              "لم يتم العثور على الوجهة."
            );

            return;

          }


          endLat =
            destination.lat;

          endLon =
            destination.lon;

        }


        startInput.dataset.lat =
          startLat;

        startInput.dataset.lon =
          startLon;

        destinationInput.dataset.lat =
          endLat;

        destinationInput.dataset.lon =
          endLon;


        calculateRoute(
          startLat,
          startLon,
          endLat,
          endLon
        );

      });


    /*
      تحويل العنوان إلى إحداثيات
    */
    async function geocodeAddress(query) {

      if (!query) {
        return null;
      }


      try {

        const url =
          "https://nominatim.openstreetmap.org/search" +
          "?format=jsonv2" +
          "&limit=1" +
          "&accept-language=ar" +
          "&q=" +
          encodeURIComponent(query);


        const response =
          await fetch(url);


        if (!response.ok) {
          return null;
        }


        const data =
          await response.json();


        if (!data.length) {
          return null;
        }


        return {
          lat:
            parseFloat(data[0].lat),

          lon:
            parseFloat(data[0].lon)
        };


      } catch (error) {

        return null;

      }

    }


    /*
      حساب الطريق باستخدام OSRM
    */
    async function calculateRoute(
      startLat,
      startLon,
      endLat,
      endLon
    ) {

      showToast(
        "جارٍ حساب الطريق..."
      );


      try {

        const url =
          `https://router.project-osrm.org/route/v1/driving/` +
          `${startLon},${startLat};` +
          `${endLon},${endLat}` +
          `?overview=full&geometries=geojson&steps=true`;


        const response =
          await fetch(url);


        if (!response.ok) {
          throw new Error(
            "Routing error"
          );
        }


        const data =
          await response.json();


        if (
          data.code !== "Ok" ||
          !data.routes ||
          !data.routes.length
        ) {

          throw new Error(
            "No route"
          );

        }


        const route =
          data.routes[0];


        /*
          حذف المسار السابق
        */
        if (routeLayer) {

          map.removeLayer(
            routeLayer
          );

        }


        /*
          رسم المسار
        */
        routeLayer =
          L.geoJSON(
            route.geometry,
            {
              style: {
                color: "#177cff",
                weight: 6,
                opacity: 0.95
              }
            }
          ).addTo(map);


        /*
          عرض المسار بالكامل
        */
        map.fitBounds(
          routeLayer.getBounds(),
          {
            paddingTopLeft:
              [25, 120],

            paddingBottomRight:
              [25, 170],

            animate: false
          }
        );


        /*
          المسافة
        */
        const distanceKm =
          route.distance / 1000;


        /*
          الوقت
        */
        const durationMin =
          Math.round(
            route.duration / 60
          );


        document.getElementById(
          "routeDistance"
        ).textContent =
          distanceKm < 1
            ? Math.round(
                route.distance
              ) + " م"
            : distanceKm.toFixed(1) +
              " كم";


        document.getElementById(
          "routeDuration"
        ).textContent =
          formatDuration(
            durationMin
          );


        document.getElementById(
          "routeInfo"
        ).style.display =
          "block";


        showToast(
          "تم حساب الطريق."
        );


      } catch (error) {

        showToast(
          "تعذر حساب الطريق حالياً."
        );

      }

    }


    /*
      تنسيق مدة الطريق
    */
    function formatDuration(minutes) {

      if (minutes < 60) {

        return (
          minutes +
          " دقيقة"
        );

      }


      const hours =
        Math.floor(
          minutes / 60
        );


      const mins =
        minutes % 60;


      if (!mins) {

        return (
          hours +
          " ساعة"
        );

      }


      return (
        hours +
        " س " +
        mins +
        " د"
      );

    }


    /*
      إخفاء نتائج البحث عند الضغط
      على الخريطة
    */
    map.on("click", () => {

      searchResults.style.display =
        "none";

    });

  </script>

</body>
</html>