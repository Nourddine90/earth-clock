<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>الأرض الحية مع الساعة</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: black;
            font-family: 'Arial', sans-serif;
            overflow: hidden;
            direction: ltr; /* محاذاة الساعة لليسار لأن الأرقام إنجليزية */
        }
        #clock {
            position: absolute;
            top: 20px;
            left: 20px;
            color: white;
            font-size: 3rem;
            font-weight: bold;
            text-shadow: 2px 2px 8px rgba(0,0,0,0.8);
            background-color: rgba(0,0,0,0.3);
            padding: 10px 25px;
            border-radius: 50px;
            backdrop-filter: blur(5px);
            z-index: 10;
            font-family: 'Courier New', monospace;
            border: 1px solid rgba(255,255,255,0.3);
        }
        #earth-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        #earth-image {
            width: 100%;
            height: 100%;
            object-fit: cover; /* يغطي الشاشة بالكامل */
            transition: opacity 1s ease-in-out;
        }
        /* تنسيق بسيط للهاتف */
        @media (max-width: 600px) {
            #clock { font-size: 2rem; top: 10px; left: 10px; }
        }
        .credit {
            position: absolute;
            bottom: 10px;
            right: 20px;
            color: rgba(255,255,255,0.5);
            font-size: 0.8rem;
            z-index: 10;
            background-color: rgba(0,0,0,0.3);
            padding: 4px 10px;
            border-radius: 20px;
        }
        .credit a {
            color: rgba(255,255,255,0.8);
            text-decoration: none;
        }
    </style>
</head>
<body>
    <div id="clock"></div>
    <div id="earth-container">
        <img id="earth-image" src="" alt="صورة الأرض من الفضاء">
    </div>
    <div class="credit">
        <span>الصورة من: <a href="https://epic.gsfc.nasa.gov/" target="_blank">ناسا EPIC</a> (تحديث يومي)</span>
    </div>

    <script>
        // دالة لتحديث الساعة كل ثانية
        function updateClock() {
            const now = new Date();
            const hours = now.getHours().toString().padStart(2, '0');
            const minutes = now.getMinutes().toString().padStart(2, '0');
            const seconds = now.getSeconds().toString().padStart(2, '0');
            const timeString = `${hours}:${minutes}:${seconds}`;
            document.getElementById('clock').textContent = timeString;
        }

        // تحديث الساعة فوراً ثم كل ثانية
        updateClock();
        setInterval(updateClock, 1000);

        // الحصول على صورة حقيقية للأرض من ناسا (EPIC API)
        // هذا الـ API يعيد أحدث صورة ملتقطة للأرض من القمر الصناعي DSCOVR
        async function fetchEarthImage() {
            try {
                // نستخدم وكيلاً لتجاوز مشاكل CORS (لأن API ناسا لا يدعم الطلبات المباشرة من المتصفح بسهولة)
                // لكن يمكننا استخدام صورة ثابتة كبديل، أو محاولة جلب الصورة بطريقة أخرى
                // الأسهل: استخدام صورة ثابتة عالية الدقة من ناسا (صورة "Blue Marble") كخلفية أساسية
                // ولكن لنجرب الحصول على صورة محدثة عبر خدمة proxy مجانية (قد تعمل وقد لا تعتمد على المتصفح)
                
                // محاولة أولى: استخدام صورة ثابتة احتياطية (عالية الجودة)
                // هذه الصورة هي "Blue Marble" الشهيرة من ناسا
                document.getElementById('earth-image').src = "https://eoimages.gsfc.nasa.gov/images/imagerecords/57000/57723/globe_west_2048.jpg";
                
                // محاولة ثانية: جلب أحدث صورة من EPIC إذا أمكن (مع تجنب CORS)
                // لاحظ: هذا قد لا يعمل في جميع المتصفحات بسبب سياسة CORS.
                // لكننا سنحاول باستخدام proxy مجاني.
                const proxyUrl = 'https://cors-anywhere.herokuapp.com/';
                const epicUrl = 'https://epic.gsfc.nasa.gov/api/natural';
                
                // سنحاول جلب البيانات عبر proxy (قد يحتاج لتفعيل مؤقت في موقع cors-anywhere)
                // إذا لم يعمل، ستظهر الصورة الاحتياطية.
                const response = await fetch(proxyUrl + epicUrl);
                if (response.ok) {
                    const data = await response.json();
                    if (data && data.length > 0) {
                        // أحدث صورة متاحة
                        const latest = data[0];
                        const date = latest.date.split(' ')[0].replace(/-/g, '/');
                        const imageName = latest.image;
                        // رابط الصورة عالية الدقة
                        const imageUrl = `https://epic.gsfc.nasa.gov/archive/natural/${date}/png/${imageName}.png`;
                        // تعيين الصورة الجديدة
                        document.getElementById('earth-image').src = imageUrl;
                    }
                } else {
                    console.log('لم نتمكن من جلب الصورة المحدثة، نعرض الصورة الاحتياطية.');
                }
            } catch (error) {
                console.log('حدث خطأ في جلب الصورة، نعرض الصورة الاحتياطية.', error);
                // الصورة الاحتياطية موجودة بالفعل
            }
        }

        // استدعاء دالة جلب الصورة
        fetchEarthImage();

        // تحديث الصورة كل ساعة (اختياري)
        setInterval(fetchEarthImage, 60 * 60 * 1000); // كل ساعة
    </script>
</body>
</html>
