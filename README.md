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
function drawPendulumDemo() {
            for(let p of pendulums) {
                // محاسبه نیروها
                let gravity = 0.4;
                let angularAcc = (-gravity / p.length) * sin(p.angle);
                
                p.angularVel += angularAcc;
                p.angularVel *= 0.999; // میرایی بسیار کم برای حفظ انرژی
                p.angle += p.angularVel;
                
                // موقعیت وزنه
                let bobX = p.x + p.length * sin(p.angle);
                let bobY = p.y + p.length * cos(p.angle);
                
                // رسم ریسمان
                stroke(200, 200, 200);
                strokeWeight(2);
                line(p.x, p.y, bobX, bobY);
                
                // رسم مسیر (قوس دایره)
                stroke(p.color[0], p.color[1], p.color[2], 50);
                strokeWeight(1);
                noFill();
                let startAngle = -PI/3;
                let endAngle = PI/3;
                arc(p.x, p.y, p.length * 2, p.length * 2, startAngle, endAngle);
                
                // نقطه تکیه
                fill(100);
                noStroke();
                ellipse(p.x, p.y, 8, 8);
                
                // وزنه
                fill(p.color[0], p.color[1], p.color[2]);
                ellipse(bobX, bobY, 20, 20);
                
                // محاسبه انرژی‌ها (فیزیک صحیح)
                let mass = 1; // جرم واحد
                let g = 0.4;  // شتاب جاذبه
                
                // انرژی جنبشی: KE = 0.5 * m * (L * ω)²
                let linearVelocity = abs(p.angularVel * p.length);
                let ke = 0.5 * mass * linearVelocity * linearVelocity;
                
                // انرژی پتانسیل: PE = mgh = mg * L * (1 - cos(θ))
                let height = p.length * (1 - cos(p.angle));
                let pe = mass * g * height;
                
                // انرژی کل
                let totalEnergy = ke + pe;
                
                // نمایش انرژی‌ها
                fill(255);
                textAlign(CENTER);
                textSize(11);
                text(`KE: ${ke.toFixed(2)}`, p.x, p.y + p.length + 30);
                text(`PE: ${pe.toFixed(2)}`, p.x, p.y + p.length + 45);
                text(`Total: ${totalEnergy.toFixed(2)}`, p.x, p.y + p.length + 60);
                
                // نمایش نوار انرژی
                let barWidth = 60;
                let barHeight = 8;
                let barY = p.y + p.length + 75;
                
                // نوار کل انرژی (زمینه)
                fill(50);
                rect(p.x - barWidth/2, barY, barWidth, barHeight);
                
                if(totalEnergy > 0) {
                    // نوار انرژی جنبشی (قرمز)
                    fill(255, 100, 100);
                    let keWidth = (ke / totalEnergy) * barWidth;
                    rect(p.x - barWidth/2, barY, keWidth, barHeight);
                    
                    // نوار انرژی پتانسیل (آبی)
                    fill(100, 100, 255);
                    let peWidth = (pe / totalEnergy) * barWidth;
                    rect(p.x - barWidth/2 + keWidth, barY, peWidth, barHeight);
                }
                
                // قاب نوار
                noFill();
                stroke(255);
                rect(p.x - barWidth/2, barY, barWidth, barHeight);
            }
            
            // اطلاعات بقای انرژی
            fill(255);
            textAlign(LEFT);
            textSize(14);
            text('⚖️ قانون بقای انرژی: KE + PE = ثابت', 20, 30);
            text('🔴 قرمز: انرژی جنبشی (KE)', 20, 50);
            text('🔵 آبی: انرژی پتانسیل (PE)', 20, 70);
            text('کلیک نزدیک آونگ برای اختلال', 20, 90);
        }
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
function drawWavesDemo() {
            // گرادیانت پس‌زمینه
            for(let i = 0; i <= height; i++) {
                let alpha = map(i, 0, height, 0, 1);
                let c = lerpColor(color(50, 100, 200), color(20, 50, 100), alpha);
                stroke(c);
                line(0, i, width, i);
            }
            
            // رسم امواج
            for(let w = 0; w < waves.length; w++) {
                let wave = waves[w];
                
                fill(255, 255, 255, 100 - w * 20);
                noStroke();
                
                beginShape();
                vertex(0, height);
                
                for(let x = 0; x <= width; x += 3) {
                    let y = height/2 + wave.amplitude * sin(x * wave.frequency + wave.phase + frameCount * wave.speed);
                    vertex(x, y);
                }
                
                vertex(width, height);
                endShape(CLOSE);
                
                wave.phase += wave.speed;
            }
            
            // نمایش معادله موج
            fill(255);
            textAlign(LEFT);
            textSize(14);
            text('معادله موج: y = A × sin(fx + φ + t)', 20, 30);
            text(`A: دامنه، f: فرکانس، φ: فاز، t: زمان`, 20, 50);
        }
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
function drawParticlesDemo() {
            // تولید ذرات جدید
            if(frameCount % 3 == 0) {
                particles.push({
                    x: width/2 + random(-20, 20),
                    y: height - 50,
                    vx: random(-3, 3),
                    vy: random(-8, -3),
                    life: 255,
                    size: random(3, 8),
                    color: [random(200, 255), random(100, 200), random(50, 150)]
                });
            }
            
            // به‌روزرسانی ذرات
            for(let i = particles.length - 1; i >= 0; i--) {
                let p = particles[i];
                
                // فیزیک
                p.vy += 0.1; // جاذبه
                p.vx *= 0.99; // مقاومت هوا
                p.x += p.vx;
                p.y += p.vy;
                p.life -= 3;
                
                // رسم
                let alpha = map(p.life, 0, 255, 0, 255);
                fill(p.color[0], p.color[1], p.color[2], alpha);
                noStroke();
                ellipse(p.x, p.y, p.size, p.size);
                
                // حذف ذرات مرده
                if(p.life <= 0 || p.y > height) {
                    particles.splice(i, 1);
                }
            }
            
            // نمایش منبع
            fill(255, 200, 0);
            ellipse(width/2, height - 50, 15, 15);
        }
```



- پس از پایان عمر (`life <= 0`) ذره حذف می‌شود.  

---

## 🌳 شبیه‌سازی فراکتال بازگشتی

- الگوریتم بازگشتی برای ترسیم درخت فراکتال به‌کار رفته است:  

```javascript
function drawFractalDemo() {
            translate(width/2, height - 50);
            stroke(100, 255, 100);
            strokeWeight(2);
            
            angle = map(mouseX, 0, width, 0, PI/2);
            
            branch(80);
            
            // اطلاعات
            fill(255);
            textAlign(LEFT);
            text(`زاویه: ${(angle * 180 / PI).toFixed(1)}°`, -width/2 + 20, -height + 80);
            text('حرکت ماوس برای تغییر زاویه', -width/2 + 20, -height + 100);
        }

        function branch(len) {
            line(0, 0, 0, -len);
            translate(0, -len);
            
            if(len > 4) {
                push();
                rotate(angle);
                branch(len * 0.67);
                pop();
                
                push();
                rotate(-angle);
                branch(len * 0.67);
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
