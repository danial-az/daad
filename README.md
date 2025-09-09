<div dir="rtl" align="right">

## 🧠 نقش p5.js در شبیه‌سازی مفاهیم علمی
این کتابخانه، برای شبیه سازی مفاهیم فیزیکی و ریاضی استفاده میشود و بسیار قدرتمند است و ما شبیه ساز هایی اماده کردیم برای که با استفاده از این کتابخانه کد های انرا اماده کردیم، ما پنج تا شبیه ساز اماده کردیم، اولی شبیه ساز جاذبه و پرتاب است که میتوانیم تاثیر سرعت اولیه و جاذبه را روی توپ ببینیم. در شبیه سازی بعدی ما میخواهیم که تبدیل انرژی جنبشی و پتانسیل اونگ را با ضریب میرایی ببینیم و در شبیه سازی سوم میخواهیم تاثیر دوره امواج را ببینیم در زمان، در شبیه سازی بعدی انتشار ذرات را با طول عمر و حرکت در هوا با مقاومت هوا شبیه سازی میکند. و در شبیه سازی اخر هم درخت فرکتال را رسم کردیم.
## 🎯 ۱. مدل‌سازی فیزیک واقع‌گرایانه
- امکان پیاده‌سازی دقیق معادلات حرکت، نیروها و برهمکنش‌های فیزیکی را فراهم می‌آورد
- حلقه draw() با نرخ فریم ثابت، شبیه‌سازی پویا و روانی ایجاد می‌کند

## 📊 ۲. تجسم مفاهیم انتزاعی ریاضی
- ترسیم نمودارها و اشکال پیچیده ریاضی با توابع ساده
- نمایش بصری توابع سینوسی، معادلات دیفرانسیل و ساختارهای فراکتالی
- تبدیل مفاهیم انتزاعی به تصاویر قابل درک و تعاملی

## 🔬 ۳. یادگیری تعاملی و آزمایشگاه مجازی
- کاربران می‌توانند پارامترها را تغییر داده و نتایج را بلافاصله مشاهده کنند
- ایجاد محیطی امن برای آزمایش بدون نیاز به تجهیزات فیزیکی
- امکان بررسی سناریوهای مختلف با تغییر شرایط اولیه

## 📈 ４. جمع‌آوری و نمایش داده‌ها
- نمایش همزمان پدیده فیزیکی و داده‌های عددی مربوطه
- نمودارهای بلادرنگ برای نمایش روابط بین متغیرها
- امکان بررسی کمی و کیفی پدیده‌ها به صورت همزمان

</div>
## 🎨 راه‌اندازی و ساخت بوم (Canvas)
- در ابتدای پروژه کتابخانه **p5.js** به صورت CDN در فایل HTML بارگذاری می‌شود:  

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.7.0/p5.min.js"></script>
```

- سپس در تابع `setup()` بوم اصلی ساخته می‌شود:

```javascript
function setup() {
  canvas = createCanvas(800, 500);
  canvas.parent('canvas-container'); // اتصال به بخش مشخصی از HTML
}
```

- این بوم پایه‌ی اصلی برای رسم اشکال، انیمیشن و محاسبات فیزیکی است.

---

## 🌀 چرخه‌ی ترسیم (Loop)

- در p5.js تابع `draw()` مثل یک حلقه بی‌نهایت در هر فریم اجرا می‌شود.  
- در این پروژه از `switch` استفاده شده تا بسته به دکمه‌ی انتخابی، شبیه‌سازی مربوطه نمایش داده شود:  

```javascript
function draw() {
  background(20, 30, 50); // پس‌زمینه
  switch(currentDemo) {
    case 'gravity': drawGravityDemo(); break;
    case 'pendulum': drawPendulumDemo(); break;
    case 'waves': drawWavesDemo(); break;
    case 'particles': drawParticlesDemo(); break;
    case 'fractal': drawFractalDemo(); break;
  }
}
```

---

## 🌍 شبیه‌سازی جاذبه و پرتابه

- برای حرکت پرتابه‌ها از **بردارها (p5.Vector)** استفاده شده است:  

```javascript
function drawGravityDemo() {
            // رسم زمین
            stroke(100, 255, 100);
            strokeWeight(3);
            line(0, height - 30, width, height - 30);
            
            // رسم خط هدایت
            if(isDragging) {
                stroke(255, 100, 100, 150);
                strokeWeight(2);
                line(mouseStartX, mouseStartY, mouseX, mouseY);
                
                // نمایش سرعت اولیه
                let vel = createVector(mouseX - mouseStartX, mouseY - mouseStartY);
                vel.mult(0.3);
                stats.initialVelocity = vel.mag().toFixed(1);
                stats.launchAngle = (-atan2(vel.y, vel.x) * 180 / PI).toFixed(0);
                
                // نمایش پیش‌بینی مسیر
                stroke(255, 255, 0, 100);
                strokeWeight(1);
                noFill();
                beginShape();
                for(let t = 0; t < 100; t += 2) {
                    let x = mouseStartX + vel.x * t;
                    let y = mouseStartY + vel.y * t + 0.5 * gravity.y * t * t;
                    if(y > height - 30 || x > width) break;
                    vertex(x, y);
                }
                endShape();
            }
            
            // به‌روزرسانی پرتابه‌ها
            for(let i = projectiles.length - 1; i >= 0; i--) {
                let p = projectiles[i];
                
                p.velocity.add(gravity);
                p.position.add(p.velocity);
                
                // اضافه کردن به مسیر
                p.trail.push(p.position.copy());
                if(p.trail.length > 30) {
                    p.trail.splice(0, 1);
                }
                
                // رسم مسیر
                stroke(255, 150 + sin(frameCount * 0.1) * 50, 100, 200);
                strokeWeight(2);
                for(let j = 1; j < p.trail.length; j++) {
                    let alpha = map(j, 0, p.trail.length, 0, 255);
                    stroke(255, 100, 50, alpha);
                    line(p.trail[j-1].x, p.trail[j-1].y, p.trail[j].x, p.trail[j].y);
                }
                
                // رسم پرتابه
                fill(255, 200 + sin(frameCount * 0.2 + i) * 55, 100);
                noStroke();
                ellipse(p.position.x, p.position.y, p.size, p.size);
                
                // حذف پرتابه
                if(p.position.y > height - 30 || p.position.x > width) {
                    stats.range = p.position.x;
                    projectiles.splice(i, 1);
                }
            }
        }
```

- مسیر حرکت پرتابه با استفاده از فرمول‌های فیزیک محاسبه و نمایش داده می‌شود:  
  - سرعت: `v = v₀ + gt`  
  - مکان: `s = v₀t + ½gt²`  

- قابلیت پیش‌بینی مسیر قبل از پرتاب با رسم خطوط نقطه‌ای زرد وجود دارد.  
- تعداد پرتابه‌ها، زاویه و برد نیز به‌صورت زنده محاسبه می‌شوند.  

---

## ⚖️ شبیه‌سازی آونگ

- معادلات دینامیکی آونگ ساده به‌صورت زیر پیاده‌سازی شده است:  

```javascript
let angularAcc = (-gravity / p.length) * sin(p.angle); // شتاب زاویه‌ای
p.angularVel += angularAcc; // سرعت زاویه‌ای
p.angle += p.angularVel;    // زاویه آونگ
```

- انرژی‌ها نیز محاسبه و نمایش داده می‌شوند:  
  - انرژی جنبشی (KE): `0.5 * m * (L * ω)²`  
  - انرژی پتانسیل (PE): `m * g * h`  
  - مجموع انرژی (قانون بقای انرژی): ثابت  

- در صفحه نوارهای رنگی قرمز و آبی نشان‌دهنده نسبت انرژی جنبشی و پتانسیل هستند.  

---

## 🌊 شبیه‌سازی امواج

- امواج با استفاده از توابع سینوسی رسم شده‌اند:  

```javascript
let y = height/2 + wave.amplitude * sin(
           x * wave.frequency + wave.phase + frameCount * wave.speed
         );
```

- برای طبیعی‌تر شدن، چند لایه موج با دامنه و سرعت‌های متفاوت روی هم ترسیم شده‌اند.  
- در بخش بالای صفحه فرمول کلی موج نیز نمایش داده می‌شود:  

```
y = A × sin(fx + φ + t)
```

---

## ✨ شبیه‌سازی سیستم ذرات

- ذرات به صورت تصادفی تولید می‌شوند:  

```javascript
particles.push({
  x: width/2 + random(-20, 20),
  y: height - 50,
  vx: random(-3, 3),
  vy: random(-8, -3),
  life: 255,
  size: random(3, 8)
});
```

- سپس در هر فریم تحت اثر جاذبه و مقاومت هوا حرکت می‌کنند:  

```javascript
p.vy += 0.1;  // جاذبه
p.vx *= 0.99; // مقاومت هوا
p.x += p.vx;
p.y += p.vy;
p.life -= 3;  // کاهش عمر ذره
```

- پس از پایان عمر (`life <= 0`) ذره حذف می‌شود.  

---

## 🌳 شبیه‌سازی فراکتال بازگشتی

- الگوریتم بازگشتی برای ترسیم درخت فراکتال به‌کار رفته است:  

```javascript
function branch(len) {
  line(0, 0, 0, -len);
  translate(0, -len);
  if(len > 4) {
    push();
    rotate(angle);
    branch(len * 0.67); // شاخه سمت راست
    pop();

    push();
    rotate(-angle);
    branch(len * 0.67); // شاخه سمت چپ
    pop();
  }
}
```

- زاویه `angle` با حرکت ماوس تغییر می‌کند و باعث تغییر شکل درخت می‌شود.  

---

## 🖱️ تعامل با کاربر

- **mousePressed()** → شروع پرتاب توپ یا ایجاد اختلال در آونگ‌ها  
- **mouseReleased()** → ثبت پرتابه جدید و آغاز حرکت آن  
- **حرکت ماوس** → تغییر زاویه شاخه‌ها در فراکتال  

مثال:  

```javascript
function mouseReleased() {
  if(currentDemo === 'gravity' && isDragging) {
    let velocity = createVector(mouseX - mouseStartX, mouseY - mouseStartY);
    velocity.mult(0.3);
    projectiles.push({
      position: createVector(mouseStartX, mouseStartY),
      velocity: velocity.copy(),
      size: 12
    });
    stats.projectileCount++;
    isDragging = false;
  }
}
```

---

<div dir="rtl">

## 📊 نمایش داده‌ها و آمار

بخش آماری پروژه شامل:

- ▪️ تعداد پرتابه‌ها  
- ▪️ سرعت اولیه  
- ▪️ زاویه پرتاب  
- ▪️ برد پرتابه  

این مقادیر در هر فریم به‌روز شده و به‌صورت زنده نمایش داده می‌شوند.  

</div>

---


</div>
