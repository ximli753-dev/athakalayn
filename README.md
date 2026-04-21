<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>تطبيق الثقلين - الإصدار المطور</title>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:wght@400;700&family=Tajawal:wght@300;500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root { --gold: #d4af37; --bg: #000804; --glass: rgba(255, 255, 255, 0.05); --active-bg: rgba(212, 175, 55, 0.15); }
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: var(--bg); background-image: radial-gradient(circle at center, #051a0f 0%, #000 100%); color: #e0e0e0; font-family: 'Tajawal', sans-serif; min-height: 100vh; overflow-x: hidden; }
        
        .overlay { position: fixed; inset: 0; z-index: 9999; background: #000; display: flex; flex-direction: column; justify-content: center; align-items: center; padding: 20px; backdrop-filter: blur(15px); }
        .choice-card { width: 90%; max-width: 400px; padding: 25px; border-radius: 20px; margin: 10px; border: 1px solid var(--gold); background: var(--glass); cursor: pointer; text-align: center; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .choice-card:active { transform: scale(0.95); background: var(--active-bg); }
        
        /* تعديل صندوق التحذير ليكون كبيراً وبألوان هادئة */
        .warn-box { border: 2px solid var(--gold); border-radius: 30px; padding: 40px 30px; text-align: center; background: rgba(10, 20, 15, 0.95); max-width: 550px; width: 95%; box-shadow: 0 0 30px rgba(212, 175, 55, 0.2); }
        .warn-contact { margin: 20px 0; padding: 20px; background: rgba(212, 175, 55, 0.07); border-radius: 20px; font-size: 1.1rem; border: 1px dashed var(--gold); line-height: 2; }
        .warn-contact b { color: var(--gold); font-size: 1.3rem; margin: 0 5px; }

        header { padding: 15px; text-align: center; background: rgba(0,0,0,0.8); border-bottom: 1px solid var(--gold); position: sticky; top: 0; z-index: 100; backdrop-filter: blur(10px); }
        .logo { font-family: 'Amiri', serif; font-size: 1.8rem; color: var(--gold); text-shadow: 0 0 10px rgba(212, 175, 55, 0.3); }

        .page { display: none; padding: 20px; max-width: 700px; margin: auto; padding-bottom: 140px; }
        .page.active { display: block; animation: fadeIn 0.6s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }

        .card { background: var(--glass); border-radius: 20px; padding: 20px; margin-bottom: 15px; border-right: 4px solid var(--gold); transition: 0.3s; }
        .card h3 { color: var(--gold); margin-bottom: 12px; font-family: 'Amiri'; font-size: 1.4rem; border-bottom: 1px solid rgba(212,175,55,0.1); padding-bottom: 5px; }
        .card p { font-family: 'Amiri', serif; font-size: 1.3rem; line-height: 1.8; text-align: justify; color: #d0d0d0; }

        .prayer-list { background: var(--glass); border-radius: 20px; overflow: hidden; }
        .prayer-time-box { display: flex; justify-content: space-between; padding: 15px 20px; border-bottom: 1px solid rgba(212,175,55,0.1); transition: 0.3s; }
        .prayer-time-box:last-child { border: none; }
        .prayer-time-box span:first-child { color: var(--gold); font-weight: bold; }

        .subha-container { text-align: center; margin-top: 20px; }
        .press-zone { width: 220px; height: 220px; border-radius: 50%; border: 4px solid var(--gold); margin: 25px auto; display: flex; flex-direction: column; align-items: center; justify-content: center; background: radial-gradient(circle, rgba(212,175,55,0.1) 0%, transparent 70%); cursor: pointer; transition: 0.2s; box-shadow: 0 0 30px rgba(212,175,55,0.1); position: relative; overflow: hidden; }
        .press-zone:active { transform: scale(0.92); background: var(--active-bg); }
        
        .reset-btn { width: 100%; max-width: 300px; padding: 18px; border-radius: 15px; border: 1px solid #ff4444; background: rgba(255,68,68,0.05); color: #ff4444; font-size: 1.2rem; font-weight: bold; cursor: pointer; margin-top: 15px; transition: 0.3s; }
        .reset-btn:active { background: #ff4444; color: white; }

        nav { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); width: 92%; max-width: 500px; background: rgba(0, 12, 6, 0.95); border: 1px solid var(--gold); border-radius: 35px; display: flex; padding: 12px; z-index: 1000; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .nav-link { flex: 1; text-align: center; color: #666; cursor: pointer; transition: 0.3s; font-size: 0.85rem; }
        .nav-link.active { color: var(--gold); transform: translateY(-3px); }
        .nav-link i { font-size: 1.5rem; display: block; margin-bottom: 5px; }

        select { width: 100%; padding: 15px; background: #0a0a0a; color: var(--gold); border: 1px solid var(--gold); border-radius: 15px; font-family: 'Tajawal'; font-size: 1rem; outline: none; margin-bottom: 20px; }
    </style>
</head>
<body>

<div id="gate" class="overlay">
    <div class="logo" style="font-size: 3rem; margin-bottom: 40px;">الثقلين</div>
    <div class="choice-card" onclick="chooseMethod('shia')">
        <h2 style="color: var(--gold);">المذهب الشيعي</h2>
    </div>
    <div class="choice-card" onclick="chooseMethod('sunni')">
        <h2 style="color: var(--gold);">المذهب السني</h2>
    </div>
</div>

<div id="warn" class="overlay" style="display:none;">
    <div class="warn-box">
        <h2 style="color:var(--gold); margin-bottom:20px; font-family:'Amiri'; font-size:2.5rem;">تنبيه هادئ</h2>
        <p style="font-size: 1.2rem; line-height: 1.6;">يرجى تفعيل الموقع (GPS) لضبط أوقات الصلاة بدقة.</p>
        
        <div class="warn-contact">
            <p style="color: #fff; margin-bottom
