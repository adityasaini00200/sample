<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>New Year 2026 Countdown</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
    <style>
        body { 
            font-family: 'Arial', sans-serif; 
            background: linear-gradient(45deg, #000428, #004e92, #ff6b6b); 
            display: flex; flex-direction: column; 
            justify-content: center; align-items: center; 
            height: 100vh; margin: 0; color: white; 
            animation: bgShift 10s infinite; 
            text-align: center;
        }
        @keyframes bgShift { 0%,100%{background-position:0% 50%;} 50%{background-position:100% 50%;} }
        .countdown { font-size: 3em; margin: 20px; }
        .time { display: inline-block; margin: 0 15px; background: rgba(255,255,255,0.1); padding: 20px 15px; border-radius: 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.3); min-width: 80px; }
        .label { font-size: 0.4em; margin-top: 5px; opacity: 0.8; }
        #qr { margin-top: 30px; padding: 20px; background: rgba(255,255,255,0.1); border-radius: 20px; }
        h1 { font-size: 2.5em; text-shadow: 0 0 20px #ff6b6b; animation: glow 2s infinite alternate; }
        @keyframes glow { from { filter: drop-shadow(0 0 10px #fff); } to { filter: drop-shadow(0 0 30px #ff6b6b); } }
        @media (max-width: 600px) { .countdown { font-size: 2em; } .time { margin: 0 10px; padding: 15px 10px; } }
    </style>
</head>
<body>
    <h1>🎉 Happy New Year 2026! 🎉</h1>
    <div class="countdown" id="timer"></div>
    <div id="qr"><canvas id="qrcode"></canvas><p>Scan to share countdown!</p></div>
    <script>
        function updateCountdown() {
            const now = new Date().getTime();
            const target = new Date('January 1, 2026 00:00:00 GMT+00:00').getTime(); // UTC for global
            const diff = target - now;
            if (diff < 0) {
                document.getElementById('timer').innerHTML = '<h2>Happy New Year! 🎊✨</h2>';
                return;
            }
            const days = Math.floor(diff / (1000 * 60 * 60 * 24));
            const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
            const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
            const seconds = Math.floor((diff % (1000 * 60)) / 1000);
            document.getElementById('timer').innerHTML = 
                `<div class="time"><div>${days}</div><div class="label">Days</div></div>
                 <div class="time"><div>${hours}</div><div class="label">Hours</div></div>
                 <div class="time"><div>${minutes}</div><div class="label">Mins</div></div>
                 <div class="time"><div>${seconds}</div><div class="label">Secs</div></div>`;
        }
        // Generate QR for current page
        QRCode.toCanvas(document.getElementById('qrcode'), window.location.href, { width: 200, colorDark: '#ffffff', colorLight: 'rgba(255,255,255,0.1)' });
        setInterval(updateCountdown, 1000);
        updateCountdown();
    </script>
</body>
</html>
