<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <title>ساعة الأرض</title>
    <style>
        body { margin:0; background:black; color:white; font-family:Arial; }
        #clock {
            position:absolute; top:20px; left:20px;
            font-size:3rem; background:rgba(0,0,0,0.5); padding:10px 25px; border-radius:50px;
        }
        #earth {
            width:100vw; height:100vh; object-fit:cover;
        }
    </style>
</head>
<body>
    <div id="clock"></div>
    <img id="earth" src="https://eoimages.gsfc.nasa.gov/images/imagerecords/57000/57723/globe_west_2048.jpg" alt="Earth">
    <script>
        function updateClock() {
            const now = new Date();
            document.getElementById('clock').textContent = 
                now.getHours().toString().padStart(2,'0') + ':' +
                now.getMinutes().toString().padStart(2,'0') + ':' +
                now.getSeconds().toString().padStart(2,'0');
        }
        setInterval(updateClock, 1000);
        updateClock();
    </script>
</body>
</html>
