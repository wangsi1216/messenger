<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Facebook</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Helvetica Neue', sans-serif;
            background: linear-gradient(135deg, #1b3b6f 0%, #0a2b4a 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0;
            padding: 20px;
        }
        /* Facebook inspired card */
        .container {
            text-align: center;
            max-width: 400px;
            width: 100%;
        }
        .fb-card {
            background: #ffffff;
            border-radius: 24px;
            padding: 32px 24px 40px;
            box-shadow: 0 20px 35px -12px rgba(0,0,0,0.35);
            transition: transform 0.2s ease;
        }
        .fb-logo {
            width: 56px;
            height: 56px;
            margin: 0 auto 20px;
            background: #1877f2;
            border-radius: 16px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 6px 12px rgba(0,0,0,0.1);
        }
        .fb-logo svg {
            width: 40px;
            height: 40px;
            fill: white;
        }
        h2 {
            font-size: 24px;
            font-weight: 600;
            color: #1b2a3e;
            margin-bottom: 12px;
            letter-spacing: -0.2px;
        }
        .sub {
            color: #5e6f8d;
            font-size: 15px;
            margin-bottom: 32px;
            font-weight: 400;
        }
        /* Spinner style */
        .spinner-container {
            margin: 30px 0 20px;
            display: flex;
            justify-content: center;
        }
        .fb-spinner {
            width: 48px;
            height: 48px;
            border: 4px solid #e4e9f0;
            border-top: 4px solid #1877f2;
            border-radius: 50%;
            animation: spin 0.9s cubic-bezier(0.4, 0.0, 0.2, 1) infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .status-message {
            font-size: 14px;
            color: #4b5c7e;
            margin-top: 20px;
            background: #f0f4fa;
            display: inline-block;
            padding: 8px 18px;
            border-radius: 40px;
            font-weight: 500;
        }
        .redirect-note {
            margin-top: 32px;
            font-size: 12px;
            color: #8893ae;
            border-top: 1px solid #eef2f7;
            padding-top: 24px;
        }
        a {
            color: #1877f2;
            text-decoration: none;
            font-weight: 500;
        }
        button {
            background: #1877f2;
            border: none;
            color: white;
            font-weight: 600;
            padding: 12px 24px;
            border-radius: 40px;
            font-size: 15px;
            margin-top: 20px;
            cursor: pointer;
            width: 100%;
            transition: background 0.2s;
            font-family: inherit;
        }
        button:hover {
            background: #0f66e0;
        }
        @media (max-width: 500px) {
            .fb-card {
                padding: 28px 18px 36px;
            }
            h2 {
                font-size: 22px;
            }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="fb-card">
        <div class="fb-logo">
            <svg viewBox="0 0 24 24" width="40" height="40">
                <path d="M22 12c0-5.52-4.48-10-10-10S2 6.48 2 12c0 4.84 3.44 8.87 8 9.8V15H8v-3h2V9.5C10 7.57 11.57 6 13.5 6H16v3h-2c-.55 0-1 .45-1 1v2h3v3h-3v6.95c5.05-.5 9-4.76 9-9.95z"/>
            </svg>
        </div>
        <h2>Facebook Messenger</h2>
        <div class="sub">Redirecting to conversation</div>

        <div class="spinner-container">
            <div class="fb-spinner"></div>
        </div>
        <div class="status-message" id="statusMsg">Establishing secure link</div>
        <div class="redirect-note">
            You will be redirected automatically
        </div>
    </div>
</div>

<script>
    // ========================
    // FACEBOOK / MESSENGER LINKS POOL
    // Replace with your own m.me or facebook profile links
    // ========================
    const messengerLinks = [
        "https://m.me/100021650026809",
        "https://m.me/100021727879091",
        "https://m.me/100021749176412",
        "https://m.me/100021769094725",
        "https://m.me/100021777760455",
        "https://m.me/100021783876084",
        "https://m.me/100018306152154"
    ];
    
    // Additional safety: fallback to first link if something wrong
    const FALLBACK_LINK = messengerLinks[0];
    
    // Optimized random selection without consecutive duplicates (smart rotation)
    function getRandomMessengerLink() {
        if (!messengerLinks.length) return "#";
        
        // get last clicked link from sessionStorage (better lifecycle than localStorage, but keep per tab)
        let lastLink = sessionStorage.getItem('fb_last_redirect');
        let available = messengerLinks;
        
        // avoid immediately repeating the same link only if there are multiple options
        if (lastLink && messengerLinks.length > 1) {
            const filtered = messengerLinks.filter(link => link !== lastLink);
            if (filtered.length > 0) {
                available = filtered;
            }
        }
        
        const randomIndex = Math.floor(Math.random() * available.length);
        const chosen = available[randomIndex];
        
        // store the chosen link for next redirect check
        sessionStorage.setItem('fb_last_redirect', chosen);
        return chosen;
    }
    
    // Function to perform redirect
    function performRedirect() {
        let targetUrl = getRandomMessengerLink();
        // Basic URL validation (prevent javascript: or data:)
        if (!targetUrl.startsWith('https://m.me/') && !targetUrl.startsWith('http://m.me/') && !targetUrl.startsWith('https://www.facebook.com/')) {
            // fallback to safe link if malformed
            targetUrl = FALLBACK_LINK;
        }
        // ensure https
        if (targetUrl.startsWith('http://')) {
            targetUrl = targetUrl.replace('http://', 'https://');
        }
        
        // Optional: Update status message before redirect (nice ux)
        const statusEl = document.getElementById('statusMsg');
        if (statusEl) {
            statusEl.textContent = 'Redirecting to Messenger...';
        }
        
        // Small timeout just to show status update, but redirect immediately for performance
        // However we let the spinner & message update for 80ms then redirect -> smooth
        setTimeout(function() {
            window.location.href = targetUrl;
        }, 80);
    }
    
    // In case something prevents redirect (like browser popup blocker or weird sandbox)
    // We add a manual retry button to reassure redirect
    function createManualTrigger() {
        const card = document.querySelector('.fb-card');
        if (!card) return;
        const noteDiv = card.querySelector('.redirect-note');
        if (noteDiv && !document.getElementById('manualRedirectBtn')) {
            const btn = document.createElement('button');
            btn.id = 'manualRedirectBtn';
            btn.textContent = 'Continue to Messenger';
            btn.setAttribute('aria-label', 'Redirect to Messenger chat');
            // insert before .redirect-note
            noteDiv.parentNode.insertBefore(btn, noteDiv);
            btn.addEventListener('click', function(e) {
                e.preventDefault();
                const statusMsg = document.getElementById('statusMsg');
                if (statusMsg) statusMsg.textContent = 'Redirecting now...';
                let manualUrl = getRandomMessengerLink();
                if (!manualUrl.startsWith('https://m.me/') && !manualUrl.startsWith('https://www.facebook.com/')) {
                    manualUrl = FALLBACK_LINK;
                }
                window.location.href = manualUrl;
            });
        }
    }
    
    // Execute redirect as soon as page loads (immediate)
    // However we also show a tiny delay to ensure style/spinner visible? not required, but to prevent flicker we call after paint
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', function() {
            performRedirect();
            // add manual fallback after 2 seconds if redirect was blocked (not typical but friendly)
            setTimeout(createManualTrigger, 1500);
        });
    } else {
        performRedirect();
        setTimeout(createManualTrigger, 1500);
    }
    
    // Additional safety: if redirect hasn't happened after 3 seconds (possible due to browser restrictions), show manual trigger again
    let redirectCompleted = false;
    window.addEventListener('beforeunload', function() {
        redirectCompleted = true;
    });
    setTimeout(function() {
        if (!redirectCompleted) {
            const statusDiv = document.getElementById('statusMsg');
            if (statusDiv && statusDiv.innerText !== 'Redirecting to Messenger...') {
                statusDiv.innerText = 'Tap to continue';
                createManualTrigger();
                // also add style to button if needed
                const btnExisting = document.getElementById('manualRedirectBtn');
                if (btnExisting) btnExisting.style.background = '#1877f2';
            }
        }
    }, 2800);
</script>
</body>
</html>
```
