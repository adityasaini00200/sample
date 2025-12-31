<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <meta name="theme-color" content="#002244" />
  <title>New Year 2026 Countdown</title>

  <!-- QRCode library -->
  <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js" defer></script>

  <style>
    :root{
      --bg1: #000428;
      --bg2: #004e92;
      --accent: #ff6b6b;
      --panel-bg: rgba(255,255,255,0.08);
      --glass: rgba(255,255,255,0.06);
      --text: #ffffff;
      --shadow: rgba(0,0,0,0.35);
      --radius: 14px;
    }

    /* Respect user preference for reduced motion */
    @media (prefers-reduced-motion: no-preference) {
      body {
        animation: bgShift 12s infinite linear;
      }
      h1 { animation: glow 2s infinite alternate; }
    }

    @media (prefers-reduced-motion: reduce) {
      body, h1 { animation: none; }
    }

    @keyframes bgShift {
      0%{ background-position:0% 50% }
      50%{ background-position:100% 50% }
      100%{ background-position:0% 50% }
    }

    @keyframes glow {
      from { filter: drop-shadow(0 0 10px #fff) }
      to   { filter: drop-shadow(0 0 30px var(--accent)) }
    }

    html,body{
      height:100%;
      margin:0;
      color:var(--text);
      font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background: linear-gradient(45deg, var(--bg1), var(--bg2), var(--accent));
      background-size: 400% 400%;
      display:flex;
      align-items:center;
      justify-content:center;
      text-align:center;
      padding:24px;
      box-sizing:border-box;
    }

    main {
      width:100%;
      max-width:900px;
      padding:24px;
      border-radius:20px;
      background: linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
      box-shadow: 0 20px 60px var(--shadow);
      backdrop-filter: blur(6px);
    }

    h1{
      font-size:2.2rem;
      margin:0 0 12px 0;
      line-height:1.05;
      text-shadow: 0 0 14px rgba(255,255,255,0.05);
    }

    .countdown-wrapper{
      display:flex;
      flex-direction:column;
      align-items:center;
      gap:18px;
    }

    /* Grid for time units */
    .countdown {
      display:grid;
      grid-template-columns: repeat(4, minmax(80px, 1fr));
      gap:12px;
      align-items:stretch;
      width:100%;
      margin:8px 0 0 0;
    }

    .time {
      background: var(--panel-bg);
      border-radius: var(--radius);
      padding:16px 10px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.25);
      display:flex;
      flex-direction:column;
      align-items:center;
      justify-content:center;
      min-width:72px;
    }

    .time .value{
      font-size:1.6rem;
      font-weight:700;
      letter-spacing:0.6px;
    }

    .label {
      margin-top:6px;
      font-size:0.78rem;
      opacity:0.9;
    }

    #qr {
      margin-top:18px;
      padding:14px;
      display:inline-flex;
      gap:12px;
      align-items:center;
      background:var(--glass);
      border-radius:18px;
    }

    #qrcode{
      width:100%;
      max-width:200px;
      height:auto;
      display:block;
    }

    .actions{
      margin-top:12px;
      display:flex;
      gap:10px;
      justify-content:center;
      flex-wrap:wrap;
    }

    button{
      background:transparent;
      color:var(--text);
      border:1px solid rgba(255,255,255,0.14);
      padding:8px 12px;
      border-radius:10px;
      cursor:pointer;
      font-size:0.95rem;
    }
    button:active{ transform: translateY(1px); }
    button[disabled]{ opacity:0.5; cursor:not-allowed; }

    p.small{ font-size:0.85rem; opacity:0.9; margin:6px 0 0 0; }

    @media (max-width:640px){
      .countdown{ grid-template-columns: repeat(2, 1fr); gap:10px; }
      .time .value{ font-size:1.2rem; }
      h1{ font-size:1.6rem; }
    }

    /* Fallback (no JS) */
    noscript .no-js-note { display:block; color: #fff; background: rgba(0,0,0,0.35); padding:12px; border-radius:8px; margin-top:12px; }
  </style>
</head>
<body>
  <main>
    <h1 aria-hidden="true">🎉 Happy New Year 2026! 🎉</h1>

    <div class="countdown-wrapper">
      <div id="timer" class="countdown" role="timer" aria-live="polite" aria-atomic="true">
        <!-- JS will populate -->
      </div>

      <div id="qr" aria-label="QR code and share controls">
        <canvas id="qrcode" aria-hidden="true"></canvas>
        <div>
          <p class="small" id="qr-hint">Scan to share countdown</p>
          <div class="actions">
            <button id="shareBtn" type="button">Share</button>
            <button id="copyBtn" type="button">Copy Link</button>
          </div>
        </div>
      </div>

      <noscript>
        <div class="no-js-note">JavaScript is required for the live countdown and QR/share functionality. Please enable JavaScript.</div>
      </noscript>
    </div>
  </main>

  <script>
    (function () {
      'use strict';

      // Target: midnight UTC of Jan 1st, 2026
      const targetTime = Date.UTC(2026, 0, 1, 0, 0, 0); // months are 0-indexed

      const timerEl = document.getElementById('timer');
      const qrcanvas = document.getElementById('qrcode');
      const shareBtn = document.getElementById('shareBtn');
      const copyBtn = document.getElementById('copyBtn');

      // Format number with zero padding (hours/mins/secs)
      function pad(n, digits = 2) {
        return String(n).padStart(digits, '0');
      }

      function updateCountdown() {
        const now = Date.now();
        const diff = targetTime - now;

        if (diff <= 0) {
          timerEl.innerHTML = '<div style="grid-column:1/-1"><h2 style="margin:0">Happy New Year! 🎊✨</h2></div>';
          // stop updates by clearing interval (we'll rely on flag below)
          if (intervalId) {
            clearInterval(intervalId);
            intervalId = null;
          }
          return;
        }

        const days = Math.floor(diff / (1000 * 60 * 60 * 24));
        const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
        const seconds = Math.floor((diff % (1000 * 60)) / 1000);

        timerEl.innerHTML = `
          <div class="time" aria-label="${days} days">
            <div class="value">${days}</div>
            <div class="label">Days</div>
          </div>
          <div class="time" aria-label="${hours} hours">
            <div class="value">${pad(hours)}</div>
            <div class="label">Hours</div>
          </div>
          <div class="time" aria-label="${minutes} minutes">
            <div class="value">${pad(minutes)}</div>
            <div class="label">Mins</div>
          </div>
          <div class="time" aria-label="${seconds} seconds">
            <div class="value">${pad(seconds)}</div>
            <div class="label">Secs</div>
          </div>`;
      }

      // initial render
      let intervalId = null;
      updateCountdown();
      intervalId = setInterval(updateCountdown, 1000);

      // Generate QR code for the current page (if available)
      function makeQRCode(text) {
        // Ensure library is available
        if (typeof QRCode !== 'undefined' && QRCode.toCanvas) {
          // the library supports a callback form; use it for better compatibility
          try {
            QRCode.toCanvas(qrcanvas, text, {
              width: 200,
              margin: 1,
              color: {
                dark: '#ffffff',
                light: 'rgba(255,255,255,0.06)'
              }
            }, function (error) {
              if (error) {
                console.error('QR generation error:', error);
                // fallback: draw a simple alternative message onto canvas
                try {
                  const ctx = qrcanvas.getContext && qrcanvas.getContext('2d');
                  if (ctx) {
                    qrcanvas.width = 200;
                    qrcanvas.height = 200;
                    ctx.fillStyle = 'rgba(255,255,255,0.04)';
                    ctx.fillRect(0,0,qrcanvas.width,qrcanvas.height);
                    ctx.fillStyle = '#fff';
                    ctx.font = '12px sans-serif';
                    ctx.textAlign = 'center';
                    ctx.fillText('QR unavailable', qrcanvas.width/2, qrcanvas.height/2);
                  }
                } catch (e) { /* noop */ }
              }
            });
          } catch (err) {
            console.warn('QRCode call failed:', err);
          }
        } else {
          console.warn('QRCode library not loaded.');
        }
      }

      // Use a safe URL to share: prefer canonical if set, otherwise location.href
      const shareUrl = (function () {
        try {
          // if hosted on http(s), use that; if file:, still use location.href
          return window.location.href;
        } catch (e) {
          return 'https://example.com'; // fallback
        }
      })();

      // Create QR
      makeQRCode(shareUrl);

      // Share button: use Web Share API when available
      shareBtn.addEventListener('click', async function () {
        if (navigator.share) {
          try {
            await navigator.share({
              title: 'New Year 2026 Countdown',
              text: 'Countdown to 2026 — join the celebration!',
              url: shareUrl
            });
          } catch (err) {
            // user cancelled or error
            console.info('Share failed or cancelled', err);
          }
        } else {
          // disable share and inform user
          shareBtn.setAttribute('disabled', 'true');
          shareBtn.textContent = 'Share not supported';
          setTimeout(() => { shareBtn.removeAttribute('disabled'); shareBtn.textContent = 'Share'; }, 3000);
        }
      });

      // Copy link fallback
      copyBtn.addEventListener('click', async function () {
        try {
          if (navigator.clipboard && navigator.clipboard.writeText) {
            await navigator.clipboard.writeText(shareUrl);
            copyBtn.textContent = 'Copied!';
            setTimeout(() => copyBtn.textContent = 'Copy Link', 2000);
          } else {
            // fallback: create input and select
            const tmp = document.createElement('input');
            document.body.appendChild(tmp);
            tmp.value = shareUrl;
            tmp.select();
            document.execCommand('copy');
            document.body.removeChild(tmp);
            copyBtn.textContent = 'Copied!';
            setTimeout(() => copyBtn.textContent = 'Copy Link', 2000);
          }
        } catch (err) {
          console.error('Copy failed', err);
          copyBtn.textContent = 'Failed';
          setTimeout(() => copyBtn.textContent = 'Copy Link', 2000);
        }
      });

    })();
  </script>
</body>
</html>
