<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alislamiah Maps - واجهة ثابتة</title>
    
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body, html { width: 100%; height: 100%; overflow: hidden; background: #e5e3df; }

        /* حاوية ثابتة تماماً تشبه تصميم واجهة الخرائط الكبرى */
        .static-map-container {
            width: 100%;
            height: 100%;
            position: relative;
            background-image: url('https://images.unsplash.com/photo-1524661135-423995f22d0b?q=80&w=1920&auto=format&fit=crop'); /* خلفية خريطة واقعية وثابتة عالية الدقة */
            background-size: cover;
            background-position: center;
        }

        /* طبقة تعتيم خفيفة لجعل الواجهة احترافية */
        .overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0, 0, 0, 0.1);
        }

        /* شريط البحث العلوي الثابت */
        .search-container {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 10;
            display: flex;
            align-items: center;
            background: #fff;
            width: 90%;
            max-width: 450px;
            height: 50px;
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            padding: 0 15px;
        }
        .search-container input {
            flex: 1; border: none; outline: none; font-size: 16px; color: #202124; background: transparent; text-align: right;
        }

        /* شعار التطبيق الثابت */
        .brand-logo {
            position: absolute;
            top: 20px;
            right: 20px;
            z-index: 10;
            background: #fff;
            padding: 10px 18px;
            border-radius: 8px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            font-weight: bold;
            color: #1b4d3e;
            font-size: 16px;
        }

        /* علامة تثبيت ثابتة في منتصف الشاشة */
        .center-pin {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -100%);
            z-index: 5;
            font-size: 35px;
            color: #d93025;
            text-shadow: 0 2px 5px rgba(0,0,0,0.3);
        }
    </style>
</head>
<body>

    <div class="static-map-container">
        <div class="overlay"></div>
        
        <!-- شعار Alislamiah Maps الثابت -->
        <div class="brand-logo">Alislamiah Maps</div>

        <!-- شريط البحث العلوي الثابت -->
        <div class="search-container">
            <input type="text" placeholder="البحث في Alislamiah Maps...">
        </div>

        <!-- دبوس تحديد الموقع الثابت في المنتصف -->
        <div class="center-pin">📍</div>
    </div>

</body>
</html>
