---
title: 🚀 Повна інструкція створення блогу Ai24q на Hugo + Cloudflare + Sveltia CMS
description: |-
  📋 Зміст проекту
  Ви створюєте сучасний блог із:
  Головна сторінка : Ваш існуючий дизайн з неоморфізмом та яскравими кольорами
  Блог : Список статей з превью (картинка, заголовок, опис, теги)
  Сторінка статті : Читабельний текст як в Medium
  Sveltia CMS : Зручний редактор контенту
  Без локального Hugo : Все збирається на Cloudflare Pages
date: 2025-11-28
image: /images/uploads/Screenshot 2025-11-28 00.47.53.png
tags: []
categories: []
draft: true
---
# 🎯 ЕТАП 1: Підготовка репозиторію та головної сторінки

###### [https://claude.ai/chat/feec4270-ca80-4dba-813d-80115c758053](https://claude.ai/chat/feec4270-ca80-4dba-813d-80115c758053)

### Крок 1.1: Очищення репозиторію

1. Перейдіть на [https://github.com/Ai24q/site](https://github.com/Ai24q/site)
2. Віддати всі файли, окрім захованих ( `.git`, `.github`)
3. Ми створимо нову структуру з нуля

### Крок 1.2: Створення структури Гюго

Створюйте файли один для одного через GitHub:

#### 📄`config.yml`

  **Де:** В корені репозиторію
**Як створити:** Додати файл → Створити новий файл → Назва:`config.yml`

ЯМЛ

```plain
baseURL: 'https://ai24q.pages.dev'
languageCode: 'uk-ua'
title: 'Ai24q'
theme: []

params:
  description: "Розумні рішення на основі ШІ"
  author: "Ai24q"
  favicon: "/favicon/"

# Налаштування для блогу
permalinks:
  blog: /blog/:slug/

# Таксономії (теги та категорії)
taxonomies:
  tag: tags
  category: categories
```

#### 📄`content/_index.md`

  \*\*Де: \*\*`content/_index.md`
**Зміст:**

знижка

```plain
---
title: "Головна"
---
```

#### 📄`layouts/_default/baseof.html`

  \*\*Де: \*\*`layouts/_default/baseof.html`
**Зміст:**

HTML

```plain
<!DOCTYPE html>
<html lang="uk">
<head>
  {{- partial "head.html" . -}}
</head>
<body class="font-body">
  <div class="grain"></div>
  <div id="cursor"></div>
  <div id="loader">
    <div class="flex gap-2 h-32 items-end">
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
    </div>
    <h1 class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 text-neo-bg font-display font-bold text-6xl mix-blend-exclusion">ЗАВАНТАЖЕННЯ</h1>
  </div>
  {{- partial "nav.html" . -}}
  <main id="smooth-wrapper">
    <div id="smooth-content">
      {{- block "main" . }}{{- end }}
    </div>
  </main>
  {{- partial "footer.html" . -}}
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js"></script>
  <script src="https://unpkg.com/lenis@1.0.29/dist/lenis.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  <script>
    lucide.createIcons();
    const menuBtn = document.getElementById('menu-btn');
    const mobileMenu = document.getElementById('mobile-menu');
    const closeMenu = document.getElementById('close-menu');
    if(menuBtn) menuBtn.addEventListener('click', () => mobileMenu.classList.add('open'));
    if(closeMenu) closeMenu.addEventListener('click', () => mobileMenu.classList.remove('open'));
    if(mobileMenu) mobileMenu.addEventListener('click', (e) => { if (e.target.tagName === 'A') mobileMenu.classList.remove('open'); });
    const tlLoader = gsap.timeline();
    tlLoader.to(".loader-bar", { height: "100%", duration: 0.8, stagger: 0.1, ease: "power4.inOut" })
      .to("#loader h1", { opacity: 0, duration: 0.2 })
      .to("#loader", { y: "-100%", duration: 0.8, ease: "power4.inOut" }, "-=0.2")
      .from(".hero-text", { y: 200, opacity: 0, rotate: 5, stagger: 0.1, duration: 1, ease: "power3.out" }, "-=0.5");
    const lenis = new Lenis({ duration: 1.2, easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)), smooth: true, touchMultiplier: 2 });
    function raf(time) { lenis.raf(time); requestAnimationFrame(raf); } requestAnimationFrame(raf);
    gsap.registerPlugin(ScrollTrigger);
    gsap.to(".float-anim", { y: -100, scrollTrigger: { trigger: "body", start: "top top", end: "bottom top", scrub: 1 } });
    gsap.to(".float-anim-reverse", { y: 150, rotate: 45, scrollTrigger: { trigger: "body", start: "top top", end: "bottom top", scrub: 1.5 } });
    document.querySelectorAll(".manifesto-text").forEach(text => { gsap.to(text, { opacity: 1, scrollTrigger: { trigger: text, start: "top 80%", end: "top 40%", scrub: true } }); });
    document.querySelectorAll('.stats-card').forEach(card => {
      card.addEventListener('mousemove', (e) => {
        const rect = card.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;
        gsap.to(card, { x: (x - rect.width / 2) / 5, y: (y - rect.height / 2) / 5, duration: 0.3, ease: "power2.out" });
      });
      card.addEventListener('mouseleave', () => gsap.to(card, { x: 0, y: 0, duration: 0.5, ease: "elastic.out(1, 0.5)" }));
    });
    gsap.from(".service-card", { y: 100, opacity: 0, rotate: 5, stagger: 0.1, duration: 0.8, ease: "back.out(1.7)", scrollTrigger: { trigger: ".service-card", start: "top 85%" } });
    gsap.from("#process > div > div", { y: 100, opacity: 0, stagger: 0.1, duration: 0.8, ease: "back.out(1.7)", scrollTrigger: { trigger: "#process", start: "top 85%" } });
    gsap.from(".faq-item", { y: 50, opacity: 0, stagger: 0.15, duration: 0.6, ease: "power2.out", scrollTrigger: { trigger: "#faq", start: "top 80%" } });
    if (window.innerWidth > 768 && window.matchMedia('(pointer: fine)').matches) {
      const cursor = document.getElementById('cursor');
      const links = document.querySelectorAll('a, button, .service-card, summary');
      document.addEventListener('mousemove', (e) => { cursor.style.left = e.clientX + 'px'; cursor.style.top = e.clientY + 'px'; });
      links.forEach(link => {
        link.addEventListener('mouseenter', () => { cursor.style.width = '60px'; cursor.style.height = '60px'; cursor.style.backgroundColor = '#A3FF00'; cursor.style.mixBlendMode = 'exclusion'; });
        link.addEventListener('mouseleave', () => { cursor.style.width = '24px'; cursor.style.height = '24px'; cursor.style.backgroundColor = '#0f0f0f'; cursor.style.mixBlendMode = 'difference'; });
      });
    }
    const dangerBtn = document.getElementById('danger-btn');
    if(dangerBtn) {
      dangerBtn.addEventListener('click', () => {
        gsap.to("body", { x: -10, duration: 0.1, yoyo: true, repeat: 5 });
        const duration = 2000, end = Date.now() + duration;
        (function frame() {
          confetti({ particleCount: 5, angle: 60, spread: 55, origin: { x: 0 }, colors: ['#FF4D00', '#A3FF00', '#9D00FF'] });
          confetti({ particleCount: 5, angle: 120, spread: 55, origin: { x: 1 }, colors: ['#FF4D00', '#A3FF00', '#9D00FF'] });
          if (Date.now() < end) requestAnimationFrame(frame);
        }());
        const original = dangerBtn.innerHTML;
        dangerBtn.innerHTML = '<div class="relative border-4 border-black bg-neo-black px-8 py-6 md:px-12 md:py-8 font-heavy text-3xl md:text-5xl lg:text-6xl text-white flex items-center gap-3 md:gap-4">О НІ!</div>';
        setTimeout(() => { dangerBtn.innerHTML = original; lucide.createIcons(); }, 3000);
      });
    }
  </script>
</body>
</html>
```

#### 📄`layouts/partials/head.html`

  **Де:** `layouts/partials/head.html`

HTML

```plain
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{ if .IsHome }}{{ site.Title }} | {{ site.Params.description }}{{ else }}{{ .Title }} | {{ site.Title }}{{ end }}</title>
<link rel="apple-touch-icon" sizes="180x180" href="{{ site.Params.favicon }}apple-touch-icon.png">
<link rel="icon" type="image/png" sizes="32x32" href="{{ site.Params.favicon }}favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="{{ site.Params.favicon }}favicon-16x16.png">
<link rel="manifest" href="{{ site.Params.favicon }}site.webmanifest">
<meta name="google-site-verification" content="gR7ds0ZEIQybLoFDWoTmr1NM7AvbDKhE43C9WVo0OvY" />
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Archivo+Black&family=Space+Grotesk:wght@300;500;700&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">
<script src="https://unpkg.com/lucide@latest"></script>
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: { 'neo-bg': '#FFFAE5', 'neo-main': '#FF4D00', 'neo-accent': '#A3FF00', 'neo-purple': '#9D00FF', 'neo-black': '#0f0f0f', 'neo-white': '#ffffff', 'neo-blue': '#0047FF' },
        fontFamily: { 'display': ['"Syne"', 'sans-serif'], 'body': ['"Space Grotesk"', 'sans-serif'], 'heavy': ['"Archivo Black"', 'sans-serif'], },
        boxShadow: { 'neo': '8px 8px 0px 0px #0f0f0f', 'neo-sm': '4px 4px 0px 0px #0f0f0f', 'neo-lg': '12px 12px 0px 0px #0f0f0f', 'neo-hover': '12px 12px 0px 0px #0f0f0f', 'neo-active': '2px 2px 0px 0px #0f0f0f', },
        cursor: { 'none': 'none' }
      }
    }
  }
</script>
<style>
  body { background-color: #FFFAE5; color: #0f0f0f; overflow-x: hidden; cursor: none; }
  ::selection { background-color: #0f0f0f; color: #A3FF00; }
  ::-webkit-scrollbar { width: 16px; } ::-webkit-scrollbar-track { background: #FFFAE5; border-left: 3px solid #0f0f0f; } ::-webkit-scrollbar-thumb { background: #0f0f0f; border: 3px solid #FFFAE5; } ::-webkit-scrollbar-thumb:hover { background: #FF4D00; }
  .text-outline { -webkit-text-stroke: 2px #0f0f0f; color: transparent; }
  .text-outline-white { -webkit-text-stroke: 2px #ffffff; color: transparent; }
  .grain { position: fixed; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='0.05'/%3E%3C/svg%3E"); z-index: 9998; }
  .marquee-container { overflow: hidden; white-space: nowrap; } .marquee-content { display: inline-block; animation: marquee 20s linear infinite; } @keyframes marquee { 0% { transform: translateX(0); } 100% { transform: translateX(-50%); } }
  #cursor { position: fixed; top: 0; left: 0; width: 24px; height: 24px; background-color: #0f0f0f; border-radius: 0%; pointer-events: none; z-index: 9999; transform: translate(-50%, -50%); transition: width 0.2s, height 0.2s, background-color 0.2s; mix-blend-mode: difference; }
  #loader { position: fixed; top: 0; left: 0; width: 100%; height: 100vh; background: #0f0f0f; z-index: 10000; display: flex; justify-content: center; align-items: center; } .loader-bar { width: 20px; height: 0%; background: #A3FF00; margin: 0 5px; }
  #mobile-menu { position: fixed; top: 0; left: 0; width: 100%; height: 100vh; background: #0f0f0f; color: #FFFAE5; z-index: 9997; display: flex; flex-direction: column; justify-content: center; align-items: center; gap: 2rem; font-size: 2.5rem; font-family: '"Archivo Black"', sans-serif; text-transform: uppercase; transform: translateY(-100%); transition: transform 0.4s ease; } #mobile-menu.open { transform: translateY(0); } #mobile-menu a { color: #A3FF00; text-decoration: none; } #mobile-menu a:hover { text-decoration: line-through; }
  .faq-item { border: 4px solid black; box-shadow: 8px 8px 0px 0px #0f0f0f; transition: all 0.2s ease; } .faq-item:hover { transform: translate(-2px, -2px); box-shadow: 10px 10px 0px 0px #0f0f0f; } .faq-item[open] { background-color: #A3FF00; } .faq-item summary { padding: 1.5rem; font-weight: bold; font-size: 1.25rem; cursor: pointer; display: flex; justify-content: space-between; align-items: center; } .faq-item summary::-webkit-details-marker { display: none; } .faq-item summary::after { content: '+'; font-size: 2rem; font-family: 'Archivo Black', sans-serif; } .faq-item[open] summary::after { content: '−'; } .faq-item p { padding: 0 1.5rem 1.5rem; font-size: 1.1rem; }
  @media (pointer: coarse), (max-width: 768px) { body { cursor: auto !important; } #cursor { display: none !important; } }
</style>
```

#### 📄`layouts/partials/nav.html`

  **Де:** `layouts/partials/nav.html`

HTML

```plain
<div id="mobile-menu">
  <a href="/#about">МАНІФЕСТ</a>
  <a href="/#work">ХАОС</a>
  <a href="/blog">В БЛОГ</a>
  <a href="https://t.me/ai24q" target="_blank" rel="noopener noreferrer">ПІДПИСАТИСЬ</a>
  <button id="close-menu" class="absolute top-8 right-8 text-neo-accent">
    <i data-lucide="x" class="w-12 h-12"></i>
  </button>
</div>
<nav class="fixed top-0 left-0 w-full flex justify-between items-center p-4 md:p-8 z-50 pointer-events-none mix-blend-difference text-[#FFFAE5]">
  <div class="pointer-events-auto group">
    <a href="/" class="font-heavy text-3xl md:text-4xl tracking-tighter hover:text-neo-accent transition-colors">Ai24q.</a>
  </div>
  <div class="pointer-events-auto hidden md:flex gap-6 lg:gap-8 font-bold text-lg">
    <a href="/#about" class="hover:line-through decoration-4 decoration-neo-accent">МАНІФЕСТ</a>
    <a href="/#work" class="hover:line-through decoration-4 decoration-neo-accent">ХАОС</a>
    <a href="/blog" class="hover:line-through decoration-4 decoration-neo-accent">В БЛОГ</a>
    <a href="https://t.me/ai24q" target="_blank" rel="noopener noreferrer" class="border-2 border-[#FFFAE5] px-6 py-2 hover:bg-[#FFFAE5] hover:text-black transition-all duration-300 shadow-none hover:shadow-[4px_4px_0px_0px_#A3FF00]">ПІДПИСАТИСЬ</a>
  </div>
  <div class="pointer-events-auto md:hidden border-2 border-[#FFFAE5] p-2" id="menu-btn">
    <i data-lucide="menu" class="w-6 h-6"></i>
  </div>
</nav>
```

#### 📄`layouts/partials/footer.html`

  **Де:** `layouts/partials/footer.html`

HTML

```plain
<footer class="bg-black text-neo-bg pt-16 pb-10 px-4 border-t-8 border-neo-accent">
  <div class="container mx-auto">
    <div class="grid grid-cols-1 md:grid-cols-2 gap-8 md:gap-12 text-center md:text-left">
      <div class="flex justify-center md:justify-start">
        <h1 class="font-heavy text-[14vw] md:text-[10vw] leading-none text-neo-bg select-none">Ai24q.</h1>
      </div>
      <div class="flex flex-col items-center md:items-end space-y-4 text-2xl md:text-3xl lg:text-4xl">
        <a href="https://wa.me/message/7FYFUNZWUZGCF1" target="_blank" rel="noopener noreferrer" class="font-display hover:text-neo-accent hover:ml-4 transition-all">WHATSAPP -></a>_
_        <a href="https://t.me/Ai24qbot" target="_blank" rel="noopener noreferrer" class="font-display hover:text-neo-accent hover:ml-4 transition-all">TELEGRAM -></a>
        <a href="mailto:ai24ua@gmail.com" class="relative group mt-4 md:mt-6">
          <div class="absolute inset-0 bg-neo-accent translate-x-2 translate-y-2 md:translate-x-3 md:translate-y-3 transition-transform group-hover:translate-x-3 group-hover:translate-y-3 md:group-hover:translate-x-4 md:group-hover:translate-y-4"></div>
          <div class="relative border-4 border-black bg-neo-bg px-6 py-3 md:px-8 md:py-4 font-heavy text-xl md:text-2xl text-black hover:-translate-y-1 hover:-translate-x-1 active:translate-x-1 active:translate-y-1 transition-transform duration-100">
            НАПИСАТИ НА EMAIL
          </div>
        </a>
      </div>
    </div>
    <div class="mt-16 pt-8 border-t-2 border-white/20 flex flex-col md:flex-row justify-between items-center text-xs md:text-sm font-mono gap-4">
      <p>© {{ now.Year }} {{ site.Title }}. ВСІ ПРАВА ЗАХИЩЕНІ.</p>
      <p>СТВОРЕНО KOTOVICH ТА НЕЙРОМЕРЕЖАМИ.</p>
      <a href="https://t.me/ai24q" target="_blank" rel="noopener noreferrer" class="hover:text-neo-accent transition-colors">БІЛЬШЕ ШІ — НАШОМУ КАНАЛУ</a>
    </div>
  </div>
</footer>
```

#### 📄 `layouts/index.html`(Головна сторінка)

  **Де:** `layouts/index.html`

HTML

```plain
{{ define "main" }}
<section class="relative min-h-screen flex flex-col justify-center items-center pt-20 px-4 overflow-hidden border-b-4 border-black bg-neo-bg">
  <div class="absolute top-1/4 left-4 w-20 h-20 md:w-48 md:h-48 border-4 border-black bg-neo-main rounded-full shadow-neo float-anim opacity-80"></div>
  <div class="absolute bottom-1/4 right-4 w-24 h-24 md:w-64 md:h-64 border-4 border-black bg-neo-purple rotate-12 shadow-neo float-anim-reverse z-10"></div>
  <div class="absolute top-1/2 right-1/4 w-12 h-12 md:w-16 md:h-16 border-4 border-black bg-neo-accent shadow-neo-sm"></div>
  <div class="relative z-20 text-center max-w-4xl">
    <div class="overflow-hidden"><h1 class="hero-text font-display font-bold text-[10vw] md:text-[12vw] leading-[0.85] tracking-tighter text-black">МИ СТВОРЮЄМО</h1></div>
    <div class="overflow-hidden"><h1 class="hero-text font-display font-bold text-[10vw] md:text-[12vw] leading-[0.85] tracking-tighter text-neo-blue text-outline">ШТУЧНИЙ</h1></div>
    <div class="overflow-hidden"><h1 class="hero-text font-display font-bold text-[10vw] md:text-[12vw] leading-[0.85] tracking-tighter text-black">ІНТЕЛЕКТ.</h1></div>
    <p class="hero-sub mt-6 md:mt-8 font-body text-lg md:text-2xl font-bold max-w-md mx-auto bg-white border-4 border-black p-4 shadow-neo rotate-1 md:rotate-2 text-sm md:text-base">
      Нейромережі, що руйнують правила. {{ site.Params.description }}.
    </p>
  </div>
  <div class="absolute bottom-10 left-1/2 -translate-x-1/2 animate-bounce">
    <i data-lucide="arrow-down" class="w-10 h-10 md:w-12 md:h-12 text-black stroke-[3]"></i>
  </div>
</section>

<div class="w-full bg-neo-accent border-b-4 border-black py-4 md:py-6 overflow-hidden flex items-center transform -skew-y-2 origin-left mt-[-4px] z-30 relative">
  <div class="marquee-container font-heavy text-3xl md:text-5xl lg:text-6xl text-black uppercase tracking-widest">
    <div class="marquee-content">
      /// ДАНИХ БІЛЬШЕ НЕМАЄ /// НЕЙРОМЕРЕЖІ ПРАВЛЯТЬ /// {{ site.Params.description | upper }} /// 24/7 ОПТИМІЗАЦІЯ ШІ /// КІБЕРПАНК — ЦЕ ЗАРАЗ ///
      /// ДАНИХ БІЛЬШЕ НЕМАЄ /// НЕЙРОМЕРЕЖІ ПРАВЛЯТЬ /// {{ site.Params.description | upper }} /// 24/7 ОПТИМІЗАЦІЯ ШІ /// КІБЕРПАНК — ЦЕ ЗАРАЗ ///
    </div>
  </div>
</div>

<section id="about" class="relative py-20 md:py-32 px-4 md:px-12 bg-neo-black text-neo-bg overflow-hidden">
  <div class="container mx-auto grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-12">
    <div class="md:col-span-5">
      <div class="sticky top-20 md:top-32">
        <h2 class="font-heavy text-5xl md:text-7xl lg:text-8xl mb-4 md:mb-6 text-neo-accent">МАНІФЕСТ</h2>
        <div class="w-full h-3 md:h-4 bg-neo-main border-2 border-white shadow-[4px_4px_0px_0px_#ffffff]"></div>
      </div>
    </div>
    <div class="md:col-span-7 space-y-8 md:space-y-12 text-lg md:text-2xl lg:text-3xl font-bold leading-snug">
      <p class="manifesto-text opacity-20">Ми віримо, що інтернет став стерильним лікарняним коридором. Все кругле, м'яке і безпечне. <span class="text-neo-main bg-white px-2">Ми — кувалда.</span></p>
      <p class="manifesto-text opacity-20">Ai24q — це не про логіку. Це про почуття. Ми використовуємо сиру естетику, щоб активувати дофамінові рецептори, про які ви не знали.</p>
      <p class="manifesto-text opacity-20">Потворне — це нове прекрасне. Помилка — це новий правильний шлях. Якщо ваш сайт не виглядає так, ніби може зламати браузер — ви не старалися.</p>
      <div class="grid grid-cols-2 gap-4 mt-8">
        <div class="bg-neo-bg text-black border-4 border-white p-4 md:p-6 shadow-[8px_8px_0px_0px_#A3FF00] hover:translate-x-1 hover:translate-y-1 hover:shadow-none transition-all stats-card text-center">
          <h3 class="font-heavy text-4xl md:text-5xl">99.9%</h3>
          <p class="text-xs md:text-sm font-mono uppercase mt-1">Uptime ШІ</p>
        </div>
        <div class="bg-neo-main text-white border-4 border-white p-4 md:p-6 shadow-[8px_8px_0px_0px_#9D00FF] hover:translate-x-1 hover:translate-y-1 hover:shadow-none transition-all stats-card text-center">
          <h3 class="font-heavy text-4xl md:text-5xl">0.01с</h3>
          <p class="text-xs md:text-sm font-mono uppercase mt-1">Час реакції</p>
        </div>
      </div>
    </div>
  </div>
</section>

<section id="process" class="py-16 md:py-24 bg-neo-bg border-t-4 border-black">
  <div class="container mx-auto px-4">
    <h2 class="font-heavy text-4xl md:text-6xl text-center mb-12 md:mb-16">ЯК МИ РОБОТИМО</h2>
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-8 text-center">
      <div class="bg-neo-black text-neo-bg border-4 border-black shadow-neo p-6 md:p-8 flex flex-col items-center justify-center hover:shadow-none hover:translate-x-2 hover:translate-y-2 transition-all">
        <i data-lucide="search" class="w-12 h-12 md:
```

Пн.

ПРОДОВЖУЙ ДАЛЬШЕ,,,,📄 `layouts/index.html`(Головна сторінка)

w-16 md:h-16text-neo-accent mb-4 stroke-[3]"></i><h3 class="font-heavy text-2xl md:text-3xl mb-2">1. АНАЛІЗ</h3><p class="font-bold text-sm md:text-base">Занурюємось у ваш хаос, щоб знайти прихованізакономірності.​​md:h-16 text-neo-bg mb-4 stroke-[3]"></i><h3 class="font-heavy text-2xl md:text-3xl mb-2">2. ХАОС</h3><p class="font-bold text-sm md:text-base">Створюємо ШІ-рішення, яке руйнує старі правила.</p></div><div class="bg-neo-main text-white border-4 border-black shadow-neo p-6 md:p-8 flex flex-col items-center justify-center hover:shadow-none hover:translate-x-2 hover:translate-y-2 transition-all"><i data-lucide="target" class="w-12 h-12 md:w-16 md:h-16 text-neo-bg mb-4 stroke-[3]"></i><h3 class="font-heavy text-2xl md:text-3xl mb-2">3. РЕЗУЛЬТАТ</h3><p class="font-bold text-sm md:text-base">Отримаємо зростання, економію та ліди на автопілоті.</p></div></div>

</div> </section><section id="work" class="py-16 md:py-20 bg-neo-blue border-t-4 border-black"> <div class="container mx-auto px-4"> <div class="flex flex-col md:flex-row justify-between items-start md:items-end mb-12 border-b-4 border-black pb-4 gap-4"> <h2 class="font-heavy text-4xl md:text-6xl lg:text-7xl text-white">НАШ ІНСТРУМЕНТАРІЙ</h2> <i data-lucide="wrench" class="w-10 h-10 md:w-12 md:h-12 text-neo-accent stroke-[3] ​​animate-spin-slow"></i> </div> <div class="grid grid-cols-1" md:grid-cols-3 gap-4 md:gap-8 auto-rows-[280px] md:auto-rows-[300px]"> <div class="service-card bg-neo-bg border-4 border-black shadow-neo p-6 md:p-8 flex flex-col justify-between md:col-span-2 hover:bg-neo-accent transition-colours group text-sm md:text-base"> <div class="flex justify-between items-start"> <h3 class="font-heavy text-3xl md:text-4xl uppercase">Машинне навчання</h3> <i data-lucide="layout-grid" class="w-8 h-8 md:w-10 md:h-10 stroke-[3] ​​group-hover:rotate-90 transition-transform"></i> </div> <p class="font-bold border-t-2 border-black pt-3 md:pt-4">Створюємо нейромережі, що руйнують шаблони й очікування.</p> </div> <div class="service-card bg-neo-main border-4 border-black shadow-neo p-6 md:p-8 flex flex-col justify-center items-center text-center rotate-1 hover:rotate-0 conversion-transform"> <i data-lucide="zap" class="w-16 h-16 md:w-20 md:h-20 text-black fill-white mb-3 md:mb-4 stroke-[3]"></i> <h3 class="font-display font-bold text-xl md:text-2xl text-white">24/7<br>ОПТИМІЗАЦІЯ</h3> </div> <div class="service-card bg-neo-black border-4 border-black shadow-neo p-6 md:p-8 flex flex-col justify-between text-white hover:shadow-none hover:translate-x-2 hover:translate-y-2 md:hover:translate-x-[8px] md:hover:translate-y-[8px] transition-all"> <h3 class="font-heavy text-2xl md:text-3xl text-neo-purple">НАУКА ПРО ДАНІ</h3> <p class="text-gray-400 text-sm">Дані рухаються, коли не повинні.</p> </div> <div class="service-card bg-white border-4 border-black shadow-neo p-0 overflow-hidden md:col-span-2 relative group"> <div class="absolute inset-0" bg-[url('https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?q=80&w=2564&auto=format&fit=crop')] bg-cover bg-center фільтр сірий контраст-125 груповий-наведення:grayscale-0 transition-all duration-500"></div> <div class="absolute inset-0 bg-black/50 flex items-center justify-center opacity-0 group-hover:opacity-100 transition-opacity"> <h3 class="font-heavy text-4xl md:text-5xl text-white -rotate-6">ШІ ХАОС</h3> </div> </div> </div> </div> </div> </section><section id="faq" class="py-16 md:py-24 bg-neo-bg border-t-4 border-black"> <div class="container mx-auto px-4"> <h2 class="font-heavy text-4xl md:text-6xl text-center mb-12 md:mb-16">ЧАСТИ ПИТАННЯ</h2> <div class="max-w-3xl mx-auto space-y-4"> <details class="faq-item bg-white"> <summary>Чому мені потрібен ШІ для мого бізнесу?</summary> <p>ШІ може автоматизувати до 80% рутинних завдань, зекономивши вам десятки годин на тиждень. Він також залучає нових клієнтів 24/7 та покращує якість обслуговування, що впливає на ваш прибуток.</p> </details> <details class="faq-item bg-neo-accent"> <summary>Це дуже складно і дорого?</summary> <p>Ні! Ми пропонуємо пакети для будь-якого бюджету. Почати можна за $297, а ми беремо на себе всю технічну роботу. Ви отримуєте результат без необхідності вивчати програмування чи складні інструменти.</p> </details> <details class="faq-item bg-neo-purple text-white"> <summary>Як швидко я побачив результат?</summary> <p>Багато наші клієнти відчувають економію часу вже протягом першого тижня. Перших лідів від автоматизованої системи можна отримати протягом 30 днів. Ми гарантуємо результат або працюємо безкоштовно до його досягнення.</p> </details> <details class="faq-item bg-neo-main text-white"> <summary>А що, якщо ШІ зробить помилку і налякає клієнтів?</summary> <p>Ми використовуємо "систему безпечного старту". Спочатку ШІ тестується лише нами, потім запускається на обмеженій кількості запитів, і тільки після вашого підтвердження працює в повному режимі. Ви завжди можете зупинити його один клік.</p> </details> </div> </div> </section><section class="py-20 md:py-32 bg-neo-bg flex flex-col items-center justify-center text-center overflow-hidden relative px-4"> <h2 class="font-display font-bold text-3xl md:text-5xl lg:text-6xl mb-10 md:mb-12 z-10">ГОТОВІ ДО ШІ?</h2> <button id="danger-btn" class="relative group"> <div class="absolute inset-0 bg-black translate-x-3 translate-y-3 md:translate-x-4 md:translate-y-4 conversion-transform group-hover:translate-x-5 group-hover:translate-y-5 md:group-hover:translate-x-6 md:group-hover:translate-y-6"></div> <div class="relative border-4 border-black bg-neo-main px-8 py-6 md:px-12 md:py-8 font-heavy text-3xl md:text-5xl lg:text-6xl text-white hover:-translate-y-1 hover:-translate-x-1 active:translate-x-1 active:translate-y-1 transition-transform duration-100 flex items-center gap-3 md:gap-4"> <i data-lucide="skull" class="w-10 h-10 md:w-12 md:h-12 stroke-[3]"></i> НЕ КЛІКАЙ </div> </button> <div id="confetti-container" class="absolute inset-0 pointer-events-none overflow-hidden"></div> </section> {{ end }} \`\`\`\`

### Крок 1.3: Додати фавікон

Завантажте це зображення на свій Chromebook і додайте до репозиторію:

1. Правою кнопкою → Зберегти як:

        - [android-chrome-192x192.png](https://raw.githubusercontent.com/Ai24q/site/main/static/favicon/android-chrome-192x192.png)
        - [android-chrome-512x512.png](https://raw.githubusercontent.com/Ai24q/site/main/static/favicon/android-chrome-512x512.png)
        - [значок-дотику-apple.png](https://raw.githubusercontent.com/Ai24q/site/main/static/favicon/apple-touch-icon.png)
        - [favicon-16x16.png](https://raw.githubusercontent.com/Ai24q/site/main/static/favicon/favicon-16x16.png)
        - [favicon-32x32.png](https://raw.githubusercontent.com/Ai24q/site/main/static/favicon/favicon-32x32.png)

2. На GitHub: Додати файл → Завантажити файли
3. Перетягнути всі 5 файлів
4. У полі "Name your files" додайте `static/favicon/`перед кожним іменем
5. Здійснити

#### 📄`static/favicon/site.webmanifest`

json

```plain
{
  "name": "Ai24q",
  "short_name": "Ai24q",
  "icons": [
    { "src": "/favicon/android-chrome-192x192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "/favicon/android-chrome-512x512.png", "sizes": "512x512", "type": "image/png" }
  ],
  "theme_color": "#FFFAE5",_
_  "background_color": "#0f0f0f",
  "display": "standalone"
}
```

### Крок 1.4: Налаштування Cloudflare Pages

1. Перейдіть на [dash.cloudflare.com](https://dash.cloudflare.com/7480731932ec4b15bbcfd594a31052cb/pages/view/ai24q/ai24q)
2. Налаштування → Збірки та розгортання
3. **Пресет фреймворку:** Hugo\*\*
**4. \*\*Команда збірки:** `hugo`\*\*\`
`**5. **Зібрати вихідний каталог:** `public\`
4. Зберегти

## ✅ Перевірка Етапу 1

Після створення всіх файлів Cloudflare автоматично запустить збірку. Перейдіть у розділ **Розгортання** та дочекайтеся зеленого статусу ✅.
  **Якщо збірка успішна:** ваша головна сторінка з'явиться за адресою [https://ai24q.pages.dev](https://ai24q.pages.dev)
  **Напишіть мені "Етап 1 готовий!"** і ми перейдемо до **Етапу 2: Створення блогу** .

# 🎯 ЕТАП 2: Створення блогу

_(Надам код для Етапу 2 після того, як ви підтвердите успішне виконання Етапу 1)_

{{ define "main" }} <!-- HERO --> <section class="relative min-h-screen flex flex-col justify-center items-center pt-20 px-4 overflow-hidden border-b-4 border-black bg-neo-bg"> <div class="absolute top-1/4 left-4 w-20 h-20 md:w-48 md:h-48 border-4 border-black bg-neo-main rounded-full shadow-neo">

вклеєний

Пн.

ЦЕЙ КОД ПРАВИЛЬНИЙ??? ШОСЬ СТОРІНКА НЕ ДОБРЕ ВІДОБРАЖАЄТЬСЯ layouts/index.html ??? ПРОДОВЖІТЬ 2 ЕТАП

# 🔍 АНАЛІЗ ПРОБЛЕМИ + ВИПРАВЛЕННЯ

Так, я бачу проблему! Код правильний, але **йому не вистачає анімації та стилів** , які мають бути в `layouts/partials/head.html`. Також можлива проблема зі структурою файлів.

## ✅ ШВИДКЕ ВИПРАВЛЕННЯ

### Крок 1: Перевірте структуру файлів

Ваша структура має виглядати **точно так** :

```plain
Ai24q/site/
├── config.yml
├── content/
│   └── _index.md
├── layouts/
│   ├── _default/
│   │   └── baseof.html
│   ├── partials/
│   │   ├── head.html
│   │   ├── nav.html
│   │   └── footer.html
│   └── index.html
└── static/
    └── favicon/
```

### Крок 2: Додайте анімації в`head.html`

Відкрийте `layouts/partials/head.html`і **ДОДАЙТЕ** цей код **В КІНЕЦЬ** розділу `<style>`:

css

```plain
/* Анімації для float */
@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(5deg); }
}

.float-anim {
  animation: float 6s ease-in-out infinite;
}

.float-anim-reverse {
  animation: float 8s ease-in-out infinite reverse;
}

/* Анімація для service cards */
@keyframes slide-up {
  from { opacity: 0; transform: translateY(50px); }
  to { opacity: 1; transform: translateY(0); }
}
```

## 🎯 ЕТАП 2: СТВОРЕННЯ БЛОГУ

Тепер створюємо повноцінний блог!

### Крок 2.1: Створюємо файл списку блогу

#### 📄`content/blog/_index.md`

  **Де:** `content/blog/_index.md`

знижка

```plain
---
title: "Блог"
description: "Останні новини та статті про штучний інтелект"
---
```

#### 📄 `layouts/blog/list.html`(Сторінка зі списком статей)

  **Де:** `layouts/blog/list.html`

HTML

```plain
{{ define "main" }}
<!-- BLOG HERO -->
<section class="relative min-h-[60vh] flex flex-col justify-center items-center pt-32 pb-20 px-4 overflow-hidden border-b-4 border-black bg-neo-black">
  <div class="absolute top-20 left-10 w-32 h-32 md:w-48 md:h-48 border-4 border-white bg-neo-accent rounded-full shadow-neo opacity-30 float-anim"></div>
  <div class="absolute bottom-20 right-10 w-24 h-24 md:w-40 md:h-40 border-4 border-white bg-neo-main rotate-45 shadow-neo opacity-30 float-anim-reverse"></div>

  <div class="relative z-20 text-center max-w-4xl">
    <h1 class="font-heavy text-[15vw] md:text-[10vw] lg:text-[8vw] text-neo-accent mb-6 tracking-tighter">БЛОГ</h1>
    <p class="font-body text-xl md:text-2xl text-neo-bg font-bold bg-neo-purple border-4 border-white px-8 py-4 shadow-neo inline-block -rotate-1">
      {{ .Description }}
    </p>
  </div>
</section>

<!-- BLOG POSTS GRID -->
<section class="py-16 md:py-24 bg-neo-bg">
  <div class="container mx-auto px-4">

    <!-- Фільтр по тегах (опціонально) -->
    <div class="mb-12 flex flex-wrap gap-3 justify-center">
      <a href="/blog" class="px-6 py-2 border-4 border-black bg-neo-accent font-heavy text-sm uppercase shadow-neo hover:shadow-none hover:translate-x-1 hover:translate-y-1 transition-all">
        ВСІ СТАТТІ
      </a>
      {{ range .Site.Taxonomies.tags }}
      <a href="{{ .Page.Permalink }}" class="px-6 py-2 border-4 border-black bg-white font-bold text-sm uppercase shadow-neo-sm hover:bg-neo-accent hover:shadow-none hover:translate-x-1 hover:translate-y-1 transition-all">
        {{ .Page.Title }}
      </a>
      {{ end }}
    </div>

    <!-- Сітка статей -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 max-w-7xl mx-auto">
      {{ range .Pages }}
      <article class="group">
        <a href="{{ .Permalink }}" class="block">
          <!-- Картинка -->
          <div class="relative overflow-hidden border-4 border-black shadow-neo group-hover:shadow-neo-lg group-hover:-translate-y-2 transition-all duration-300 bg-white">
            {{ if .Params.image }}
            <img src="{{ .Params.image }}" alt="{{ .Title }}" class="w-full h-64 object-cover filter grayscale group-hover:grayscale-0 transition-all duration-500">
            {{ else }}
            <div class="w-full h-64 bg-gradient-to-br from-neo-purple to-neo-main flex items-center justify-center">
              <i data-lucide="image" class="w-20 h-20 text-white opacity-50"></i>
            </div>
            {{ end }}

            <!-- Теги на картинці -->
            <div class="absolute top-4 left-4 flex flex-wrap gap-2">
              {{ range .Params.tags }}
              <span class="px-3 py-1 bg-neo-accent border-2 border-black text-xs font-heavy uppercase shadow-neo-sm">
                {{ . }}
              </span>
              {{ end }}
            </div>
          </div>

          <!-- Контент -->
          <div class="mt-4">
            <h2 class="font-heavy text-2xl md:text-3xl mb-2 group-hover:text-neo-main transition-colors">
              {{ .Title }}
            </h2>
            <p class="font-body text-base md:text-lg font-bold text-gray-700 mb-3 line-clamp-3">
              {{ .Params.description | default .Summary }}
            </p>
            <div class="flex items-center justify-between text-sm font-mono">
              <time class="text-gray-500">{{ .Date.Format "02.01.2006" }}</time>
              <span class="text-neo-main font-heavy uppercase group-hover:underline">Читати →</span>
            </div>
          </div>
        </a>
      </article>
      {{ end }}
    </div>

    <!-- Якщо статей немає -->
    {{ if eq (len .Pages) 0 }}
    <div class="text-center py-20">
      <div class="inline-block border-4 border-black bg-neo-purple text-white px-12 py-8 shadow-neo rotate-2">
        <i data-lucide="file-x" class="w-16 h-16 mx-auto mb-4"></i>
        <p class="font-heavy text-2xl">СТАТЕЙ ПОКИ НЕМАЄ</p>
        <p class="font-body mt-2">Але вони скоро з'являться!</p>
      </div>
    </div>
    {{ end }}
  </div>
</section>

<!-- CTA Section -->
<section class="py-16 md:py-20 bg-neo-black border-t-4 border-black">
  <div class="container mx-auto px-4 text-center">
    <h2 class="font-heavy text-4xl md:text-6xl text-neo-accent mb-6">ХОЧЕТЕ БІЛЬШЕ?</h2>
    <p class="font-body text-xl text-neo-bg mb-8 max-w-2xl mx-auto">
      Підписуйтесь на наш Telegram-канал, щоб не пропустити нові статті про ШІ!
    </p>
    <a href="https://t.me/ai24q" target="_blank" rel="noopener noreferrer" class="inline-block relative group">
      <div class="absolute inset-0 bg-neo-accent translate-x-3 translate-y-3 transition-transform group-hover:translate-x-4 group-hover:translate-y-4"></div>
      <div class="relative border-4 border-black bg-neo-bg px-8 py-4 font-heavy text-xl text-black hover:-translate-y-1 hover:-translate-x-1 transition-transform">
        ПІДПИСАТИСЬ
      </div>
    </a>
  </div>
</section>
{{ end }}
```

### Крок 2.2: Створюємо шаблон окремої статті

#### 📄 `layouts/blog/single.html`(Сторінка окремої статті)

  **Де:** `layouts/blog/single.html`

HTML

```plain
{{ define "main" }}
<!-- Article Header -->
<article class="bg-neo-bg">
  <!-- Hero -->
  <header class="relative py-20 md:py-32 px-4 border-b-4 border-black bg-neo-black text-neo-bg overflow-hidden">
    <div class="absolute inset-0 opacity-10">
      {{ if .Params.image }}
      <img src="{{ .Params.image }}" alt="" class="w-full h-full object-cover">
      {{ end }}
    </div>

    <div class="container mx-auto max-w-4xl relative z-10">
      <!-- Теги -->
      <div class="flex flex-wrap gap-2 mb-6">
        {{ range .Params.tags }}
        <a href="/tags/{{ . | urlize }}" class="px-4 py-2 bg-neo-accent border-2 border-white text-sm font-heavy uppercase shadow-neo-sm hover:shadow-none hover:translate-x-1 hover:translate-y-1 transition-all">
          {{ . }}
        </a>
        {{ end }}
      </div>

      <!-- Заголовок -->
      <h1 class="font-heavy text-4xl md:text-6xl lg:text-7xl mb-6 leading-tight text-neo-accent">
        {{ .Title }}
      </h1>

      <!-- Опис -->
      {{ if .Params.description }}
      <p class="font-body text-xl md:text-2xl font-bold mb-8 leading-relaxed">
        {{ .Params.description }}
      </p>
      {{ end }}

      <!-- Мета-інформація -->
      <div class="flex flex-wrap items-center gap-6 font-mono text-sm text-gray-400">
        <time class="flex items-center gap-2">
          <i data-lucide="calendar" class="w-4 h-4"></i>
          {{ .Date.Format "02 January 2006" }}
        </time>
        <span class="flex items-center gap-2">
          <i data-lucide="clock" class="w-4 h-4"></i>
          {{ .ReadingTime }} хв читання
        </span>
      </div>
    </div>
  </header>

  <!-- Featured Image -->
  {{ if .Params.image }}
  <div class="container mx-auto max-w-5xl px-4 -mt-16 relative z-20">
    <div class="border-4 border-black shadow-neo-lg overflow-hidden">
      <img src="{{ .Params.image }}" alt="{{ .Title }}" class="w-full h-auto">
    </div>
  </div>
  {{ end }}

  <!-- Article Content -->
  <div class="container mx-auto max-w-3xl px-4 py-16 md:py-24">
    <div class="prose prose-lg md:prose-xl max-w-none article-content">
      {{ .Content }}
    </div>
  </div>

  <!-- Share & Back -->
  <div class="container mx-auto max-w-3xl px-4 pb-16 border-t-4 border-black pt-8">
    <div class="flex flex-col md:flex-row justify-between items-center gap-6">
      <a href="/blog" class="group flex items-center gap-2 font-heavy text-lg hover:text-neo-main transition-colors">
        <i data-lucide="arrow-left" class="w-5 h-5 group-hover:-translate-x-1 transition-transform"></i>
        НАЗАД ДО БЛОГУ
      </a>

      <div class="flex gap-4">
        <a href="https://t.me/share/url?url={{ .Permalink }}&text={{ .Title }}" target="_blank" class="p-3 border-4 border-black bg-neo-accent shadow-neo hover:shadow-none hover:translate-x-1 hover:translate-y-1 transition-all">
          <i data-lucide="send" class="w-5 h-5"></i>
        </a>
        <button onclick="navigator.share({title: '{{ .Title }}', url: '{{ .Permalink }}'})" class="p-3 border-4 border-black bg-neo-purple text-white shadow-neo hover:shadow-none hover:translate-x-1 hover:translate-y-1 transition-all">
          <i data-lucide="share-2" class="w-5 h-5"></i>
        </button>
      </div>
    </div>
  </div>
</article>

<!-- Related Posts -->
{{ $related := .Site.RegularPages.Related . | first 3 }}
{{ if $related }}
<section class="py-16 md:py-24 bg-neo-blue border-t-4 border-black">
  <div class="container mx-auto px-4">
    <h2 class="font-heavy text-4xl md:text-6xl text-center mb-12 text-white">СХОЖІ СТАТТІ</h2>
    <div class="grid grid-cols-1 md:grid-cols-3 gap-8 max-w-7xl mx-auto">
      {{ range $related }}
      <article class="group">
        <a href="{{ .Permalink }}" class="block">
          <div class="border-4 border-black shadow-neo bg-white overflow-hidden group-hover:shadow-neo-lg group-hover:-translate-y-2 transition-all">
            {{ if .Params.image }}
            <img src="{{ .Params.image }}" alt="{{ .Title }}" class="w-full h-48 object-cover filter grayscale group-hover:grayscale-0 transition-all">
            {{ else }}
            <div class="w-full h-48 bg-neo-accent"></div>
            {{ end }}
            <div class="p-6">
              <h3 class="font-heavy text-xl mb-2 group-hover:text-neo-main transition-colors">{{ .Title }}</h3>
              <p class="text-sm text-gray-600 line-clamp-2">{{ .Summary }}</p>
            </div>
          </div>
        </a>
      </article>
      {{ end }}
    </div>
  </div>
</section>
{{ end }}

<style>
/* Стилі для контенту статті (Medium-style) */
.article-content {
  font-family: 'Space Grotesk', sans-serif;
  font-size: 1.125rem;
  line-height: 1.8;
  color: #1a1a1a;
}

.article-content h2 {
  font-family: 'Syne', sans-serif;
  font-weight: 800;
  font-size: 2rem;
  margin-top: 3rem;
  margin-bottom: 1.5rem;
  color: #0f0f0f;
}

.article-content h3 {
  font-family: 'Syne', sans-serif;
  font-weight: 700;
  font-size: 1.5rem;
  margin-top: 2.5rem;
  margin-bottom: 1rem;
}

.article-content p {
  margin-bottom: 1.75rem;
}

.article-content strong {
  font-weight: 700;
  color: #FF4D00;
}

.article-content a {
  color: #0047FF;
  text-decoration: underline;
  text-decoration-thickness: 2px;
  text-underline-offset: 3px;
  transition: all 0.2s;
}

.article-content a:hover {
  color: #FF4D00;
  text-decoration-thickness: 3px;
}

.article-content ul, .article-content ol {
  margin-bottom: 1.75rem;
  padding-left: 2rem;
}

.article-content li {
  margin-bottom: 0.75rem;
}

.article-content blockquote {
  border-left: 6px solid #A3FF00;
  padding-left: 1.5rem;
  margin: 2rem 0;
  font-style: italic;
  font-size: 1.25rem;
  color: #333;
  background: #FFFAE5;
  padding: 1.5rem;
  border: 4px solid #0f0f0f;
  box-shadow: 4px 4px 0px 0px #0f0f0f;
}

.article-content code {
  background: #0f0f0f;
  color: #A3FF00;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-family: 'Courier New', monospace;
  font-size: 0.9em;
}

.article-content pre {
  background: #0f0f0f;
  border: 4px solid #A3FF00;
  padding: 1.5rem;
  overflow-x: auto;
  margin: 2rem 0;
  box-shadow: 8px 8px 0px 0px #A3FF00;
}

.article-content pre code {
  background: none;
  padding: 0;
}

.article-content img {
  width: 100%;
  height: auto;
  margin: 2rem 0;
  border: 4px solid #0f0f0f;
  box-shadow: 8px 8px 0px 0px #0f0f0f;
}
</style>
{{ end }}
```

### Крок 2.3: Створюємо тестову статистику

#### 📄`content/blog/test-post.md`

  **Де:** `content/blog/test-post.md`

знижка

```plain
---
title: "Як штучний інтелект змінює світ"
description: "Розповідаємо про найцікавіші тренди в галузі ШІ, які змінюють наше життя прямо зараз"
date: 2025-01-15
tags: ["AI", "Технології", "Майбутнє"]
image: "https://images.unsplash.com/photo-1677442136019-21780ecad995?w=1200"
---

Штучний інтелект — це вже не фантастика. Це реальність, яка змінює все навколо нас.

## Революція вже тут

ШІ проникає в усі сфери нашого життя: від медицини до мистецтва. **Нейромережі навчилися розуміти людську мову**, створювати зображення та навіть писати код.

### Що зміниться найближчим часом?
  1. **Автоматизація рутини** — ШІ візьме на себе нудні завдання**
**2. **Персоналізація** — кожен продукт буде адаптований під вас**
**3. **Нові професії** — з'являться спеціальності, про які ми ще не знаємо

> Майбутнє належить тим, хто вміє працювати разом зі штучним інтелектом.

## Як підготуватися?

Почніть експериментувати вже зараз. Використовуйте ШІ-інструменти у своїй роботі, вивчайте нові технології та **не бійтеся змін**.

Світ змінюється швидко, і ми допоможемо вам не відстати!
```

        **🎉 Після створення всіх файлів напишіть мені "Етап 2 готовий!" і я перейду до ЕТАПУ 3: ІНТЕГРАЦІЯ SVELTIA CMS**

<!DOCTYPE html> <html lang="uk"> <head> <meta name="generator" content="Hugo 0.147.7"><meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Ai24q | Розумні рішення на основі ШІ</title> <link rel="apple-touch-icon" sizes="180x180" href="/favicon/apple-t

вклеєний

Пн.

ПЕРЕВІРТЕ ГОЛОВНУ СТОРІНКУ!!! ТУТ ДВА ПІДВАЛА І НЕ ТО!!!

# 🔴 ВИЯВЛЕНО КРИТИЧНУ ПОМІЛКУ!

Я бачу проблему! У вашому коді **навігація та футер дублюються** — вони виводяться і в `<head>`, і в `<body>`. Це сталося через неправильну структуру файлу `baseof.html`.

## ✅ ТЕРМІНОВЕ ВИПРАВЛЕННЯ

### Крок 1: Виправте`layouts/_default/baseof.html`

Відкрийте файл `layouts/_default/baseof.html`і **ПОВНІСТЮ ЗАМІНІТЬ** його вміст на цьому:

HTML

```plain
<!DOCTYPE html>
<html lang="uk">
<head>
  {{- partial "head.html" . -}}
</head>
<body class="font-body">
  <!-- Grain -->
  <div class="grain"></div>

  <!-- Cursor (desktop only) -->
  <div id="cursor"></div>

  <!-- Loader -->
  <div id="loader">
    <div class="flex gap-2 h-32 items-end">
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
      <div class="loader-bar"></div>
    </div>
    <h1 class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 text-neo-bg font-display font-bold text-6xl mix-blend-exclusion">ЗАВАНТАЖЕННЯ</h1>
  </div>

  <!-- Navigation -->
  {{- partial "nav.html" . -}}

  <!-- Main Content -->
  <main id="smooth-wrapper">
    <div id="smooth-content">
      {{- block "main" . }}{{- end }}
    </div>
  </main>

  <!-- Footer -->
  {{- partial "footer.html" . -}}

  <!-- Scripts -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js"></script>
  <script src="https://unpkg.com/lenis@1.0.29/dist/lenis.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

  <script>
    lucide.createIcons();

    // Mobile Menu
    const menuBtn = document.getElementById('menu-btn');
    const mobileMenu = document.getElementById('mobile-menu');
    const closeMenu = document.getElementById('close-menu');

    if(menuBtn) menuBtn.addEventListener('click', () => mobileMenu.classList.add('open'));
    if(closeMenu) closeMenu.addEventListener('click', () => mobileMenu.classList.remove('open'));
    if(mobileMenu) mobileMenu.addEventListener('click', (e) => {
      if (e.target.tagName === 'A') mobileMenu.classList.remove('open');
    });

    // Preloader
    const tlLoader = gsap.timeline();
    tlLoader.to(".loader-bar", { height: "100%", duration: 0.8, stagger: 0.1, ease: "power4.inOut" })
      .to("#loader h1", { opacity: 0, duration: 0.2 })
      .to("#loader", { y: "-100%", duration: 0.8, ease: "power4.inOut" }, "-=0.2")
      .from(".hero-text", { y: 200, opacity: 0, rotate: 5, stagger: 0.1, duration: 1, ease: "power3.out" }, "-=0.5");

    // Lenis Smooth Scroll
    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      smooth: true,
      touchMultiplier: 2
    });

    function raf(time) {
      lenis.raf(time);
      requestAnimationFrame(raf);
    }
    requestAnimationFrame(raf);

    // GSAP Animations
    gsap.registerPlugin(ScrollTrigger);

    gsap.to(".float-anim", {
      y: -100,
      scrollTrigger: {
        trigger: "body",
        start: "top top",
        end: "bottom top",
        scrub: 1
      }
    });

    gsap.to(".float-anim-reverse", {
      y: 150,
      rotate: 45,
      scrollTrigger: {
        trigger: "body",
        start: "top top",
        end: "bottom top",
        scrub: 1.5
      }
    });

    document.querySelectorAll(".manifesto-text").forEach(text => {
      gsap.to(text, {
        opacity: 1,
        scrollTrigger: {
          trigger: text,
          start: "top 80%",
          end: "top 40%",
          scrub: true
        }
      });
    });

    document.querySelectorAll('.stats-card').forEach(card => {
      card.addEventListener('mousemove', (e) => {
        const rect = card.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;
        gsap.to(card, {
          x: (x - rect.width / 2) / 5,
          y: (y - rect.height / 2) / 5,
          duration: 0.3,
          ease: "power2.out"
        });
      });
      card.addEventListener('mouseleave', () => {
        gsap.to(card, {
          x: 0,
          y: 0,
          duration: 0.5,
          ease: "elastic.out(1, 0.5)"
        });
      });
    });

    gsap.from(".service-card", {
      y: 100,
      opacity: 0,
      rotate: 5,
      stagger: 0.1,
      duration: 0.8,
      ease: "back.out(1.7)",
      scrollTrigger: {
        trigger: ".service-card",
        start: "top 85%"
      }
    });

    gsap.from("#process > div > div", {
      y: 100,
      opacity: 0,
      stagger: 0.1,
      duration: 0.8,
      ease: "back.out(1.7)",
      scrollTrigger: {
        trigger: "#process",
        start: "top 85%"
      }
    });

    gsap.from(".faq-item", {
      y: 50,
      opacity: 0,
      stagger: 0.15,
      duration: 0.6,
      ease: "power2.out",
      scrollTrigger: {
        trigger: "#faq",
        start: "top 80%"
      }
    });

    // Custom Cursor (desktop only)
    if (window.innerWidth > 768 && window.matchMedia('(pointer: fine)').matches) {
      const cursor = document.getElementById('cursor');
      const links = document.querySelectorAll('a, button, .service-card, summary');

      document.addEventListener('mousemove', (e) => {
        cursor.style.left = e.clientX + 'px';
        cursor.style.top = e.clientY + 'px';
      });

      links.forEach(link => {
        link.addEventListener('mouseenter', () => {
          cursor.style.width = '60px';
          cursor.style.height = '60px';
          cursor.style.backgroundColor = '#A3FF00';
          cursor.style.mixBlendMode = 'exclusion';
        });
        link.addEventListener('mouseleave', () => {
          cursor.style.width = '24px';
          cursor.style.height = '24px';
          cursor.style.backgroundColor = '#0f0f0f';
          cursor.style.mixBlendMode = 'difference';
        });
      });
    }

    // Danger Button
    const dangerBtn = document.getElementById('danger-btn');
    if(dangerBtn) {
      dangerBtn.addEventListener('click', () => {
        gsap.to("body", { x: -10, duration: 0.1, yoyo: true, repeat: 5 });

        const duration = 2000, end = Date.now() + duration;
        (function frame() {
          confetti({
            particleCount: 5,
            angle: 60,
            spread: 55,
            origin: { x: 0 },
            colors: ['#FF4D00', '#A3FF00', '#9D00FF']
          });
          confetti({
            particleCount: 5,
            angle: 120,
            spread: 55,
            origin: { x: 1 },
            colors: ['#FF4D00', '#A3FF00', '#9D00FF']
          });
          if (Date.now() < end) requestAnimationFrame(frame);
        }());

        const original = dangerBtn.innerHTML;
        dangerBtn.innerHTML = '<div class="relative border-4 border-black bg-neo-black px-8 py-6 md:px-12 md:py-8 font-heavy text-3xl md:text-5xl lg:text-6xl text-white flex items-center gap-3 md:gap-4">О НІ!</div>';
        setTimeout(() => {
          dangerBtn.innerHTML = original;
          lucide.createIcons();
        }, 3000);
      });
    }
  </script>
</body>
</html>
```

### Крок 2: Додайте анімації в`layouts/partials/head.html`

Відкрийте `layouts/partials/head.html`і **ДОДАЙТЕ** в кінець розділу `<style>`(перед закриваючим `</style>`):

css

```plain
/* Анімації */
@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(5deg); }
}

.float-anim {
  animation: float 6s ease-in-out infinite;
}

.float-anim-reverse {
  animation: float 8s ease-in-out infinite reverse;
}
```

## ✅ Після виправлення

1. Зміни до GitHub за **допомогою Commit**
2. Cloudflare автоматично перезбирає сайт
3. Перевірте [https://ai24q.pages.dev](https://ai24q.pages.dev)
  **Тепер має бути:**

- ✅ Один заголовок вгорі
- ✅ Один колонтитул внизу
- ✅ Плавні анімації
- ✅ Працюючі ефекти
        **Напишіть "Виправлено!" коли все працюєме, і ми перейдемо до Етапу 3: Sveltia CMS** 🚀

Пн.

Перейдемо до Етапу 3: Sveltia CMS ДЯКУЮ!!!

2 / 2

# 🎯 ЕТАП 3: ІНТЕГРАЦІЯ SVELTIA CMS

Sveltia CMS — це нова альтернатива Netlify CMS, яка працює прямо в браузері через GitHub API. Ви можете додати та редагувати статті блогу **без коду** , прямо з зручного інтерфейсу!

## 📋 Що буде працювати:

- ✅ Редагування статей через веб-інтерфейс
- ✅ Завантажте зображення
- ✅ Попередній перегляд статей
- ✅ Автоматичне збереження в GitHub
- ✅ Підтримка тегів та категорій

## Крок 3.1: Створення конфігураційного файлу CMS

### 📄`static/admin/config.yml`

  **Де:** `static/admin/config.yml`

ЯМЛ

```plain
backend:
  name: github
  repo: Ai24q/site
  branch: main
  base_url: https://sveltia-cms-auth.netlify.app

# Локальний режим для тестування (опціонально)
# local_backend: true

# Медіа-файли
media_folder: "static/images/uploads"_
_public_folder: "/images/uploads"

# Налаштування сайту
site_url: https://ai24q.pages.dev_
_display_url: https://ai24q.pages.dev
logo_url: https://ai24q.pages.dev/favicon/android-chrome-192x192.png

# Локалізація
locale: uk

# Колекції контенту
collections:
  # БЛОГ
    - name: "blog"
    label: "📝 Статті блогу"
    label_singular: "Стаття"
    folder: "content/blog"
    create: true
    slug: "{{slug}}"
    preview_path: "blog/{{slug}}"
    editor:
      preview: true
    fields:
            - label: "Заголовок"
        name: "title"
        widget: "string"
        hint: "Назва статті (наприклад: Як ШІ змінює світ)"
                          - label: "Опис"
        name: "description"
        widget: "text"
        hint: "Короткий опис статті для превью (1-2 речення)"
        required: false
                          - label: "Дата публікації"
        name: "date"
        widget: "datetime"
        format: "YYYY-MM-DD"
        date_format: "DD.MM.YYYY"_
_        time_format: false
        default: ""
                          - label: "Головне зображення"
        name: "image"
        widget: "image"
        required: false
        hint: "Рекомендований розмір: 1200x630px"
        media_library:
          config:
            multiple: false
                          - label: "Теги"
        name: "tags"
        widget: "list"
        required: false
        hint: "Додайте теги через Enter (наприклад: AI, Технології, Майбутнє)"
        field:
          label: "Тег"
          name: "tag"
          widget: "string"
                          - label: "Категорії"
        name: "categories"
        widget: "list"
        required: false
        hint: "Категорії статті"
        field:
          label: "Категорія"
          name: "category"
          widget: "string"
                          - label: "Чернетка"
        name: "draft"
        widget: "boolean"
        default: false
        required: false
        hint: "Якщо увімкнено, стаття не буде опублікована"
                          - label: "Зміст статті"
        name: "body"
        widget: "markdown"
        hint: "Основний текст статті в форматі Markdown"
        buttons:
                    - bold
                    - italic
                    - code
                    - link
                    - heading-two
                    - heading-three
                    - quote
                    - bulleted-list
                    - numbered-list
        editor_components:
                    - image
                    - code-block

  # НАЛАШТУВАННЯ САЙТУ (опціонально)
    - name: "settings"
    label: "⚙️ Налаштування"
    files:
            - label: "Загальні налаштування"
        name: "general"
        file: "config.yml"
        fields:
                    - label: "Назва сайту"
            name: "title"
            widget: "string"
                                          - label: "URL сайту"
            name: "baseURL"
            widget: "string"
                                          - label: "Мова"
            name: "languageCode"
            widget: "string"
                                          - label: "Параметри"
            name: "params"
            widget: "object"
            fields:
                            - label: "Опис сайту"
                name: "description"
                widget: "string"
                                                          - label: "Автор"
                name: "author"
                widget: "string"
```

## Крок 3.2: Створення HTML-сторінок адміністратора

### 📄`static/admin/index.html`

  **Де:** `static/admin/index.html`

HTML

```plain
<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sveltia CMS - Ai24q Admin</title>

  <!-- Sveltia CMS -->
  <script type="module" src="https://unpkg.com/@sveltia/cms/dist/sveltia-cms.js"></script>

  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    }

    /* Кастомізація під ваш бренд */
    :root {
            --primary-color: #A3FF00;
            --secondary-color: #FF4D00;
            --accent-color: #9D00FF;
            --background: #FFFAE5;
            --text: #0f0f0f;
    }
  </style>
</head>
<body>
  <!-- Sveltia CMS завантажиться автоматично -->
</body>
</html>
```

## Крок 3.3: Налаштування GitHub OAuth

Щоб увійти в CMS, потрібна авторизація через GitHub. Використовуємо готовий сервіс Sveltia.

### 🔐 Налаштування програми OAuth в GitHub:

1. Перейдіть на [https://github.com/settings/developers](https://github.com/settings/developers)
2. Натисніть **«Новий додаток OAuth»**
3. Заповніть форму:

        - **Назва програми:** `Ai24q CMS`\*\*\`
`**    - **URL-адреса головної сторінки:** `https://ai24q.pages.dev`**`
`**    - **URL-адреса зворотного виклику авторизації:** `https://sveltia-cms-auth.netlify.app/callback\`\*\*
**4. Натисніть \*\*«Зареєструвати заявку»**

5. **Збережіть ID клієнта** (він нам не знадобитися для базового використання)

## Крок 3.4: Додавання посилання на CMS в навігацію (опціонально)

Якщо хочете швидкого доступу до адмінки, додайте кнопку у футер.

### Оновіть`layouts/partials/footer.html`

Знайдіть рядок із посиланням на каналі Telegram і **ДОДАЙТЕ** після нього:

HTML

```plain
<a href="/admin" target="_blank" class="hover:text-neo-accent transition-colors">АДМІН-ПАНЕЛЬ</a>
```

Повний блок буде виглядати так:

HTML

```plain
<div class="mt-16 pt-8 border-t-2 border-white/20 flex flex-col md:flex-row justify-between items-center text-xs md:text-sm font-mono gap-4">
  <p>© 2025 Ai24q. ВСІ ПРАВА ЗАХИЩЕНІ.</p>
  <p>СТВОРЕНО KOTOVICH ТА НЕЙРОМЕРЕЖАМИ.</p>
  <a href="https://t.me/ai24q" target="_blank" rel="noopener noreferrer" class="hover:text-neo-accent transition-colors">БІЛЬШЕ ШІ — НАШОМУ КАНАЛУ</a>_
_  <a href="/admin" target="_blank" class="hover:text-neo-purple transition-colors">⚙️ АДМІН</a>
</div>
```

## Крок 3.5: Створення першої статті через CMS

### Як увійти в CMS:

1. Перейдіть на [**https://ai24q.pages.dev/admin**](https://ai24q.pages.dev/admin)\*\*
**2. Натисніть \*\*«Увійти через GitHub»**
2. Підтвердити доступ до репозиторію
3. Ви в адмін-панелі! 🎉

### Як додати нову статтю:

1. У лівій панелі виберіть **"📝 Статті блогу"**
2. Натисніть **«Нова стаття»**
3. Заповніть форму:

        - **Заголовок:** "Тестова стаття з CMS"\*\*
**    - \*\*Опис:** "Це перше положення, створене через Sveltia CMS"\*\*
**    - \*\*Дата:** Виберіть сьогоднішню дату\*\*
**    - \*\*Теги:** Додайте "Тест", "CMS"\*\*
**    - \*\*Зміст:** Напишіть щось на кшталт:

знижка

```plain
     ## Це працює!

     Я тільки що створив статтю через **Sveltia CMS** без єдиного рядка коду.

     ### Що далі?

     Тепер я можу:
          - Додавати нові статті
          - Завантажувати зображення
          - Редагувати контент
          - Публікувати одним кліком
```

4. Натисніть **«Опублікувати»** → **«Опублікувати зараз»**

## Крок 3.6: Перевірка результату

1. Cloudflare автоматично збирає сайт (займає 1-2 хвилини)
2. Перейдіть на [**https://ai24q.pages.dev/blog**](https://ai24q.pages.dev/blog)
3. Ви побачите свою нову статию! 🎉

## 📚 ДОВІДКА: Markdown-синтаксис для статей

Якщо ви пишете статті в CMS, використовуйте Markdown:

знижка

```plain
# Великий заголовок (H1)
## Середній заголовок (H2)
### Малий заголовок (H3)
  **Жирний текст**
*Курсив*

[Посилання на сайт](https://ai24q.pages.dev)

![Зображення](https://example.com/image.jpg)

> Цитата або важлива інформація
  - Список
- Пункт 2
- Пункт 3
  1. Нумерований список
2. Пункт 2
3. Пункт 3

`Код в рядку`
```

Блок коду

```plain
```

## 🎨 БОНУС: Завантажте зображення

### Через CMS:

1. Клікніть на поле "Головне зображення"
2. Перетягнути зображення або вибрати з комп'ютера
3. Воно автоматично завантажуватися в`/static/images/uploads/`

### Рекомендації щодо зображення:

- **Формат:** JPG, PNG, WebP\*\*
**- \*\*Розмір:** 1200x630px (для попереднього перегляду в соцмережах)\*\*
**- \*\*Вага:** До 500 Кб (стисніть на [https://tinypng.com](https://tinypng.com) )

## ✅ ПІДСУМОК ЕТАПУ 3

Після виконання всіх кроків у вас буде:

✅ Повноцінна CMS для керування блогом
✅ Вхід через GitHub
✅ Зручний редактор з попереднім переглядом
✅ Автоматичне збереження в репозиторії
✅ Завантаження зображень
✅ Підтримка тегів та категорій

## 🎉 ФІНАЛЬНА ПЕРЕВІРКА

1. Створіть 2-3 тестові статті через CMS
2. Додайте зображення до статей
3. Перевірте, як вони виглядають на [https://ai24q.pages.dev/blog](https://ai24q.pages.dev/blog)
4. Перевірте крему статтю

## 📖 ЩО ДАЛІ?

Тепер ваш блог **повністю працює** ! Ви можете:

1. **Писати статті** через`/admin`\*\*
**2. \*\*Ділитися ними** в соцмережах\*\*
**3. \*\*Оптимізувати для SEO** (додам інструкції, якщо потрібно)\*\*
**4. \*\*Додати коментарі** (можу показати як)\*\*
**5. \*\*Налаштувати аналітику** (Google Analytics тощо)
        **Напишіть "CMS працює!" коли все буде готово, і я дам вам фінальний чек-лист + поради щодо SEO та просування!** 🚀

<!DOCTYPE html> <html lang="uk"> <head> <meta name="generator" content="Hugo 0.147.7"><meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Ai24q | Розумні рішення на основі ШІ</title> <link rel="apple-touch-icon" sizes="180x180" href="/favicon/apple-

вклеєний

Пн.

Привіт! Помилка на головній сторінці - два підвала лишній у верхній частині сайту [https://ai24q.pages.dev/](https://ai24q.pages.dev/)

# 🔴 ВИЯВЛЕНО ПРОБЛЕМУ!

Я бачу! У вас **навігація та футер виводяться двічі** — один раз в `<head>`(що неправильно), і другий раз в `<body>`. Це сталося через помилку в файлі `layouts/partials/head.html`.

## ✅ ТЕРМІНОВЕ ВИПРАВЛЕННЯ

### Проблема:

У файлі `layouts/partials/head.html` **після розділів`</style>`** є HTML-код навігації та футера, який там **не повинен бути** .

### Рішення:

Відкрийте файл `layouts/partials/head.html`і **ВІДАЛІТЬ** весь HTML-код після закриваючого тегу `</style>`.

## 📄 ПРАВИЛЬНИЙ`layouts/partials/head.html`

  **Повністю замініть** вміст файлу на цей:

HTML

```plain
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{ if .IsHome }}{{ site.Title }} | {{ site.Params.description }}{{ else }}{{ .Title }} | {{ site.Title }}{{ end }}</title>

<!-- Favicon -->
<link rel="apple-touch-icon" sizes="180x180" href="{{ site.Params.favicon }}apple-touch-icon.png">
<link rel="icon" type="image/png" sizes="32x32" href="{{ site.Params.favicon }}favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="{{ site.Params.favicon }}favicon-16x16.png">
<link rel="manifest" href="{{ site.Params.favicon }}site.webmanifest">

<!-- Google Site Verification -->
<meta name="google-site-verification" content="gR7ds0ZEIQybLoFDWoTmr1NM7AvbDKhE43C9WVo0OvY" />

<!-- Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Archivo+Black&family=Space+Grotesk:wght@300;500;700&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">

<!-- Icons -->
<script src="https://unpkg.com/lucide@latest"></script>

<!-- Tailwind -->
<script src="https://cdn.tailwindcss.com"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: {
          'neo-bg': '#FFFAE5',
          'neo-main': '#FF4D00',
          'neo-accent': '#A3FF00',
          'neo-purple': '#9D00FF',
          'neo-black': '#0f0f0f',
          'neo-white': '#ffffff',
          'neo-blue': '#0047FF'
        },
        fontFamily: {
          'display': ['"Syne"', 'sans-serif'],
          'body': ['"Space Grotesk"', 'sans-serif'],
          'heavy': ['"Archivo Black"', 'sans-serif'],
        },
        boxShadow: {
          'neo': '8px 8px 0px 0px #0f0f0f',
          'neo-sm': '4px 4px 0px 0px #0f0f0f',
          'neo-lg': '12px 12px 0px 0px #0f0f0f',
          'neo-hover': '12px 12px 0px 0px #0f0f0f',
          'neo-active': '2px 2px 0px 0px #0f0f0f',
        },
        cursor: { 'none': 'none' }
      }
    }
  }
</script>

<style>
  body {
    background-color: #FFFAE5;
    color: #0f0f0f;
    overflow-x: hidden;
    cursor: none;
  }

  ::selection {
    background-color: #0f0f0f;
    color: #A3FF00;
  }

  ::-webkit-scrollbar { width: 16px; }
  ::-webkit-scrollbar-track { background: #FFFAE5; border-left: 3px solid #0f0f0f; }
  ::-webkit-scrollbar-thumb { background: #0f0f0f; border: 3px solid #FFFAE5; }
  ::-webkit-scrollbar-thumb:hover { background: #FF4D00; }

  .text-outline {
        -webkit-text-stroke: 2px #0f0f0f;
    color: transparent;
  }

  .text-outline-white {
        -webkit-text-stroke: 2px #ffffff;
    color: transparent;
  }

  .grain {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='0.05'/%3E%3C/svg%3E");
    z-index: 9998;
  }

  .marquee-container {
    overflow: hidden;
    white-space: nowrap;
  }

  .marquee-content {
    display: inline-block;
    animation: marquee 20s linear infinite;
  }

  @keyframes marquee {
    0% { transform: translateX(0); }
    100% { transform: translateX(-50%); }
  }

  #cursor {
    position: fixed;
    top: 0;
    left: 0;
    width: 24px;
    height: 24px;
    background-color: #0f0f0f;
    border-radius: 0%;
    pointer-events: none;
    z-index: 9999;
    transform: translate(-50%, -50%);
    transition: width 0.2s, height 0.2s, background-color 0.2s;
    mix-blend-mode: difference;
  }

  #loader {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: #0f0f0f;
    z-index: 10000;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .loader-bar {
    width: 20px;
    height: 0%;
    background: #A3FF00;
    margin: 0 5px;
  }

  #mobile-menu {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: #0f0f0f;
    color: #FFFAE5;
    z-index: 9997;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 2rem;
    font-size: 2.5rem;
    font-family: '"Archivo Black"', sans-serif;
    text-transform: uppercase;
    transform: translateY(-100%);
    transition: transform 0.4s ease;
  }

  #mobile-menu.open {
    transform: translateY(0);
  }

  #mobile-menu a {
    color: #A3FF00;
    text-decoration: none;
  }

  #mobile-menu a:hover {
    text-decoration: line-through;
  }

  .faq-item {
    border: 4px solid black;
    box-shadow: 8px 8px 0px 0px #0f0f0f;
    transition: all 0.2s ease;
  }

  .faq-item:hover {
    transform: translate(-2px, -2px);
    box-shadow: 10px 10px 0px 0px #0f0f0f;
  }

  .faq-item[open] {
    background-color: #A3FF00;
  }

  .faq-item summary {
    padding: 1.5rem;
    font-weight: bold;
    font-size: 1.25rem;
    cursor: pointer;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .faq-item summary::-webkit-details-marker {
    display: none;
  }

  .faq-item summary::after {
    content: '+';
    font-size: 2rem;
    font-family: 'Archivo Black', sans-serif;
  }

  .faq-item[open] summary::after {
    content: '−';
  }

  .faq-item p {
    padding: 0 1.5rem 1.5rem;
    font-size: 1.1rem;
  }

  /* Анімації */
  @keyframes float {
    0%, 100% { transform: translateY(0px) rotate(0deg); }
    50% { transform: translateY(-20px) rotate(5deg); }
  }

  .float-anim {
    animation: float 6s ease-in-out infinite;
  }

  .float-anim-reverse {
    animation: float 8s ease-in-out infinite reverse;
  }

  /* Hide cursor on mobile */
  @media (pointer: coarse), (max-width: 768px) {
    body { cursor: auto !important; }
    #cursor { display: none !important; }
  }
</style>
```

## ⚠️ ВАЖЛИВО!

ПІСЛЯ `</style>` **НЕ ПОВИННО** бути **ЖОДНОГО HTML-коду** . Ніякої навігації, ніякого футера. Файл `head.html`має! **ТІЛЬКИ** те, що знаходиться всередині тегу `<head>`.

## ✅ Після виправлення:

1. **Зміни Commit** на GitHub
2. Cloudflare автоматично перезбирає сайт (1-2 хвилини)
3. Перевірте [https://ai24q.pages.dev](https://ai24q.pages.dev)
  **Тепер має бути:**

- ✅ Одна навігація вгорі
- ✅ Один футер внизу
- ✅ Все правильно
        **Напишіть "Виправлено!" коли все працюємо, і ми продовжуємо з настройками CMS!** 🚀

Роблю на хром бук, без Linux, без встановлення Hugo локально! Інтегрувати Sveltia CMS в мій проєкт Hugo !!! Усі коди скидайте до чату, тільки розділіть на два - три етапи (тобто поговорити) + Інструкцію Хочу шоб все було максимально просто та зручно Обов'язково Сайт/блог повинен мати адаптивний дизайн

вклеєний

🚀 Повна інструкція створення блогу Ai24q на Hugo + Cloudflare + Sveltia CMS 📋 Зміст проекту Ви створюєте сучасний блог із: Головна сторінка: Ваш існуючий дизайн з неоморфізмом та яскравими кольорами Блог: Список статей із попереднім переглядом (картинка, заголовок, опис, теги) Сторінка статті: Читабельний текст

вклеєний

Пн.

Привіт! Знову Помилка на головній сторінці - два підвала лішній у верхній частині сайту. Будь-ласка проведи мене знову, бо шось сторінка не правильно відображається... Десь є помилка.... Надайте мені, продублюйте всі коди і підпишіть шо і як...Також на сайті [https://dash.cloudflare.com/7480731932ec4b15bbcfd594a31052cb/pages/view/ai24q](https://dash.cloudflare.com/7480731932ec4b15bbcfd594a31052cb/pages/view/ai24q) Пише Виникла внутрішня проблема з вашою інсталяцією Cloudflare Workers & Pages Git. Якщо ця проблема не виникає після перевстановлення, **зверніться до служби підтримки** . Дякую!!! __ [https://ai24q.pages.dev/](https://ai24q.pages.dev/) __

# 🆘 КРИТИЧНА ПОМИЛКА - ПОВНЕ ВИПРАВЛЕННЯ

Розумію вашу проблему! У вас є **ДВІ критичні помилки** :

1. \*\*Д\*\*

![Знімок екрана 20251127 22.29.23.png](https://ai24q.pages.dev/api/d3122760-b659-42ee-b097-ae6beeac73a1/files/8c484ee2-4b3f-40b1-8e0f-6d99afa94ca3/preview)

![Знімок екрана 20251127 22.23.11.png](https://ai24q.pages.dev/api/d3122760-b659-42ee-b097-ae6beeac73a1/files/5ba087df-7782-46f3-af74-59efa7a0fe0c/preview)

![Знімок екрана 20251127 22.21.08.png](https://ai24q.pages.dev/api/d3122760-b659-42ee-b097-ae6beeac73a1/files/e9ebe7d0-ee63-4ac3-aa64-1368b24f3d97/preview)

Пн.

ДЯКУЮ!!! ДУЖЕ СТИЛЬНО!!! ПЕРЕВІР ЧИ ПРАВИЛЬНО!!!! Крок 3.4: Додавання посилання на CMS у навігації (опціонально) Якщо хочете отримати швидкий доступ до адмінки, додайте кнопку у футер. Оновіть `layouts/partials/footer.html` Знайдіть рядок із посиланнями на каналі Telegram і ДОДАЙТЕ після нього: <footer class="bg-black text-neo-bg pt-16 pb-10 px-4 border-t-8 border-neo-accent"> <div class="container mx-auto"> <div class="grid grid-cols-1 md:grid-cols-2 gap-8 md:gap-12 text-center md:text-left"> <div class="flex justify-center md:justify-start"> <h1 class="font-heavy text-[14vw] md:text-[10vw] leading-none text-neo-bg select-none">Ai24q.</h1> </div> <div class="flex flex-col items-center md:items-end space-y-4 text-2xl md:text-3xl lg:text-4xl"><a href=" https://wa.me/message/7FYFUNZWUZGCF1 " target="_blank" rel="noopener noreferrer" class="font-display hover:text-neo-accent hover:ml-4 transition-all">[WHATSAPP -></a>](https://wa.me/message/7FYFUNZWUZGCF1)[<a href=" https://t.me/Ai24qbot " target="_blank" rel="noopener noreferrer" class="font-display hover:text-neo-accent hover:ml-4 transition-all">](https://t.me/Ai24qbot) TELEGRAM -></a> <a href="/admin" target="_blank" class="hover:text-neo-accent transition-colors">АДМІНІСТРАЦІЙНА ПАНЕЛЬ</a> <a href="mailto:ai24ua@gmail.com" class="relative group mt-4 md:mt-6"> <div class="absolute inset-0" bg-neo-accent translate-x-2 translate-y-2 md:translate-x-3 md:translate-y-3 transition-transform group-hover:translate-x-3 group-hover:translate-y-3 md:group-hover:translate-x-4 md:group-hover:translate-y-4"></div> <div class="relative border-4 border-black bg-neo-bg px-6 py-3 md:px-8 md:py-4 font-heavy text-xl md:text-2xl text-black hover:-translate-y-1 hover:-translate-x-1 active:translate-x-1 active:translate-y-1 transition-transform duration-100"> НАПИСАТИ НА ЕЛЕКТРОННУ ПОШТУ </div> </a> </div> </div> <div class="mt-16 pt-8 border-t-2 border-white/20 flex flex-col md:flex-row justify-between items-center text-xs md:text-sm font-mono gap-4"> <p>© {{ now.Year }} {{ site.Title }}. ВСІ ПРАВА ЗАХИЩЕНІ.</p> <p>СТВОРЕНО КОТОВИЧ ТА НЕЙРОМЕРЕЖАМИ.</p><a href=" https://t.me/ai24q " target="_blank" rel="noopener noreferrer" class="hover:text-neo-accent conversion-colors">БІЛЬШЕ ШІ — НАШОМУ КАНАЛУ</a> </div> </div> НЕ МОЖУ ПІДКЛЮЧИТИ ... [https://ai24q.pages.dev/admin/](https://ai24q.pages.dev/admin/) CMS Ai24q \* Цей додаток належить Ai24q .\* Ви можете розповісти свою програму на **GitHub Marketplace**, щоб інші користувачі могли її знайти. \* 0 користувачів \* Ідентифікатор клієнта \*Секрети клієнта Секрет клієнтаДодано9 хвилин тому від Ai24qНіколи не використовувався Секрет клієнтаДодано20 хвилин тому від Ai24qНіколи не використовувався Логотип програми Перетягування Ви також можете перетягнути зображення з комп'ютера.[](https://t.me/ai24q)[](https://ai24q.pages.dev/admin/)`Ov23liBOtjiNIOs9Ejdo*****1a71dd37*****22c98a69`

Назва програми Щось, що користувачі розпізнають і чому довіряють. URL-адреса головної сторінки Повна URL-адреса головної сторінки вашої програми. Опис програми Це відображається всім користувачам вашої програми. URL-адреса зворотного виклику авторизації 1 URL-адреса зворотного виклику вашої програми. Ознайомтеся з нашою документацією OAuth для отримання додаткової інформації. Увімкнути потік пристроїв. Дозволити цей додаток OAuth авторизувати користувачів через Device Flow. Для отримання додаткової інформації ознайомтеся **з документацією Device Flow .** \* Нижній колонтитул \* © 2025 GitHub, Inc. \* Навігація внизу сторінки \* Умови \* Конфіденційність \* Безпека \* Статус \* Громада \* Документи \* Контакти \* Керування файлами cookie \* !!!! ДЯКУЮ КРУТО!!!! ДОПОМОЖИ ЩЕ ПІДКЛЮЧИТИ CMS Ai24q, ПРОВЕДИ МЕНЕ !!!! ТРЕБА БУДЕ ЩЕ НАЛАШТУВАТИ ПІД СЕО ПОСИЛАННЯ І ОПИС !!! СКИНЬ МЕНІ В ЯКИХ ПАПКАХ ЩО НАЛАШТОВУЄТЬСЯ І ЩО ДЕ ЛЕЖИТЬ

Показати більше

# 🎉 ВІТАЮ! САЙТ ПРАЦЮЄ! Тепер підключимо CMS!

# 🔐 КРОК-ЗА-КРОКОМ: Підключення Sveltia CMS до GitHub

## ✅ Крок 1: Налаштування OAuth App в GitHub (ВИ ВЖЕ ЗРОБИЛИ!)

У вас вже є додаток OAuth! Тепер потрібно **ПРАВИЛЬНО** налаштувати **URL-адресу зворотного виклику** .

### 📝 Що робити:

1. Перейдіть на: [https://github.com/settings/developers](https://github.com/settings/developers)
2. Знайдіть свою програму **"Ai24q"**
3. Натисніть на неї
4. Знайдіть поле **"URL зворотного виклику авторизації"**
5. **ВИДАЛИТЬ** стару URL-адресу (якщо є)\*\*
**6. \*\*ВСТАВТЕ** цю:

```plain
https://sveltia-cms-auth.netlify.app/callback
```

7. Натисніть **«Оновити програму»**

## ✅ Крок 2: Оновіть файл`static/admin/config.yml`

Цей файл вже є у вас. Відкрийте його і **ПЕРЕВІРТЕ** , що там написано **ТОЧНО** це:

### 📄`static/admin/config.yml`

ЯМЛ

```plain
backend:
  name: github
  repo: Ai24q/site
  branch: main
  base_url: https://sveltia-cms-auth.netlify.app

# Медіа-файли
media_folder: "static/images/uploads"_
_public_folder: "/images/uploads"

# Налаштування сайту
site_url: https://ai24q.pages.dev_
_display_url: https://ai24q.pages.dev
logo_url: https://ai24q.pages.dev/favicon/android-chrome-192x192.png

# Локалізація
locale: uk

# Колекції контенту
collections:
  # БЛОГ
    - name: "blog"
    label: "📝 Статті блогу"
    label_singular: "Стаття"
    folder: "content/blog"
    create: true
    slug: "{{slug}}"
    preview_path: "blog/{{slug}}"
    editor:
      preview: true
    fields:
            - label: "Заголовок"
        name: "title"
        widget: "string"
        hint: "Назва статті (наприклад: Як ШІ змінює світ)"
                          - label: "Опис"
        name: "description"
        widget: "text"
        hint: "Короткий опис статті для превью (1-2 речення)"
        required: false
                          - label: "Дата публікації"
        name: "date"
        widget: "datetime"
        format: "YYYY-MM-DD"
        date_format: "DD.MM.YYYY"_
_        time_format: false
        default: ""
                          - label: "Головне зображення"
        name: "image"
        widget: "image"
        required: false
        hint: "Рекомендований розмір: 1200x630px"
        media_library:
          config:
            multiple: false
                          - label: "Теги"
        name: "tags"
        widget: "list"
        required: false
        hint: "Додайте теги через Enter (наприклад: AI, Технології, Майбутнє)"
        field:
          label: "Тег"
          name: "tag"
          widget: "string"
                          - label: "Категорії"
        name: "categories"
        widget: "list"
        required: false
        hint: "Категорії статті"
        field:
          label: "Категорія"
          name: "category"
          widget: "string"
                          - label: "Чернетка"
        name: "draft"
        widget: "boolean"
        default: false
        required: false
        hint: "Якщо увімкнено, стаття не буде опублікована"
                          - label: "Зміст статті"
        name: "body"
        widget: "markdown"
        hint: "Основний текст статті в форматі Markdown"
```

## ✅ Крок 3: Перевірте файл`static/admin/index.html`

### 📄`static/admin/index.html`

HTML

```plain
<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sveltia CMS - Ai24q Admin</title>

  <!-- Sveltia CMS -->
  <script type="module" src="https://unpkg.com/@sveltia/cms/dist/sveltia-cms.js"></script>

  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    }

    /* Кастомізація під ваш бренд */
    :root {
            --primary-color: #A3FF00;
            --secondary-color: #FF4D00;
            --accent-color: #9D00FF;
            --background: #FFFAE5;
            --text: #0f0f0f;
    }
  </style>
</head>
<body>
  <!-- Sveltia CMS завантажиться автоматично -->
</body>
</html>
```

  ---

## ✅ Крок 4: Вхід в CMS

Тепер **ВСЕ ГОТОВО**! 🎉

### Як увійти:

1. Перейдіть на: **https://ai24q.pages.dev/admin**
2. Натисніть **"Увійти за допомогою GitHub"**
3. GitHub попросить дозвіл — **натисніть "Authorize"**
4. Ви в адмін-панелі! 🚀
  ---

# 📚 SEO: Де що налаштовується

## 🗂️ Структура проекту:

```plain
Ai24q/site/
├── config.yml                    ← Загальні SEO (title, description)
├── content/
│   ├── _index.md                 ← Головна сторінка (мета-дані)
│   └── blog/
│       ├── _index.md             ← Сторінка блогу (мета-дані)
│       └── post-name.md          ← Окрема стаття (title, description, image)
├── layouts/
│   ├── _default/
│   │   └── baseof.html           ← Основний HTML (тут <head>)
│   ├── partials/
│   │   └── head.html             ← SEO мета-теги, Open Graph, Twitter Cards
│   ├── index.html                ← Головна сторінка
│   └── blog/
│       ├── list.html             ← Список статей
│       └── single.html           ← Окрема стаття
└── static/
    ├── admin/                    ← CMS
    ├── favicon/                  ← Іконки
    └── images/uploads/           ← Зображення для статей
```

## 🎯 SEO налаштування

### 1️⃣ **Загальні налаштування сайту**

📄**`config.yml`**

ЯМЛ

```plain
title: 'Ai24q | ШІ рішення для бізнесу'
languageCode: 'uk-ua'
baseURL: 'https://ai24q.pages.dev'

params:
  description: "Створюємо ШІ-рішення для автоматизації бізнесу. Нейромережі, машинне навчання та аналітика даних."
  author: "Ai24q"
  keywords: "штучний інтелект, ШІ, AI, машинне навчання, автоматизація, бізнес"

  # Open Graph / Facebook
  og_image: "https://ai24q.pages.dev/images/og-image.jpg"

  # Twitter Card
  twitter_card: "summary_large_image"_
_  twitter_site: "@ai24q"
```

### 2️⃣ **SEO для окремих сторінок**

#### 📄 **Головна сторінка:`content/_index.md`**

знижка

```plain
---
title: "Ai24q | ШІ рішення для бізнесу"
description: "Створюємо ШІ-рішення для автоматизації бізнесу. Машинне навчання, аналітика даних, чат-боти."
keywords: ["ШІ", "AI", "машинне навчання", "автоматизація"]
---
```

#### 📄 **Блог:`content/blog/_index.md`**

знижка

```plain
---
title: "Блог про штучний інтелект | Ai24q"
description: "Останні новини та статті про ШІ, машинне навчання та автоматизацію бізнесу"
---
```

### 3️⃣ **SEO в статтях блогу**

Коли ви створюєте статус через CMS, заповніть **ці поля** :

знижка

```plain
---
title: "Як ШІ змінює бізнес у 2025 році"
description: "Розповідаємо, як штучний інтелект допомагає автоматизувати процеси та збільшувати прибуток"
date: 2025-01-15
image: "/images/uploads/ai-business-2025.jpg"
tags: ["AI", "Бізнес", "Автоматизація"]
categories: ["Технології"]
---

Тут ваша стаття...
```

  **Ці дані автоматично підхоплюються для:**

- Google (назва, опис)
- Facebook Open Graph (зображення, назва, опис)
- Twitter Cards (зображення, назва, опис)

### 4️⃣ **Додайте мета-теги SEO в`layouts/partials/head.html`**

Відкрийте `layouts/partials/head.html`і **ДОДАЙТЕ** після `<title>`:

HTML

```plain
<!-- SEO -->
<meta name="description" content="{{ if .Description }}{{ .Description }}{{ else }}{{ site.Params.description }}{{ end }}">
<meta name="keywords" content="{{ if .Params.tags }}{{ delimit .Params.tags ", " }}{{ else }}{{ site.Params.keywords }}{{ end }}">
<meta name="author" content="{{ site.Params.author }}">

<!-- Open Graph / Facebook -->
<meta property="og:type" content="{{ if .IsHome }}website{{ else }}article{{ end }}">
<meta property="og:url" content="{{ .Permalink }}">
<meta property="og:title" content="{{ .Title }}">
<meta property="og:description" content="{{ if .Description }}{{ .Description }}{{ else }}{{ site.Params.description }}{{ end }}">
<meta property="og:image" content="{{ if .Params.image }}{{ .Params.image | absURL }}{{ else }}{{ site.Params.og_image }}{{ end }}">_
_<meta property="og:site_name" content="{{ site.Title }}">

<!-- Twitter Card -->
<meta name="twitter:card" content="{{ site.Params.twitter_card }}">_
_<meta name="twitter:site" content="{{ site.Params.twitter_site }}">
<meta name="twitter:title" content="{{ .Title }}">
<meta name="twitter:description" content="{{ if .Description }}{{ .Description }}{{ else }}{{ site.Params.description }}{{ end }}">
<meta name="twitter:image" content="{{ if .Params.image }}{{ .Params.image | absURL }}{{ else }}{{ site.Params.og_image }}{{ end }}">

<!-- Canonical URL -->
<link rel="canonical" href="{{ .Permalink }}">
```

## 🚀 Фінальна перевірка SEO

Після того, як все налаштовуєте, перевірте:

1. **Консоль пошуку Google** : [https://search.google.com/search-console](https://search.google.com/search-console)\*\*
**2. \*\*Налагоджувач Facebook** : [https://developers.facebook.com/tools/debug/](https://developers.facebook.com/tools/debug/)\*\*
**3. \*\*Валідатор карток у Твіттері** : [https://cards-dev.twitter.com/validator](https://cards-dev.twitter.com/validator)

# ✅ ПІДСУМОК

## 📂 Що де є:

```plain
Файл

Що набувається

config.yml

Загальні SEO, назва, опис, ключові слова

content/_index.md

SEO головної сторінки

content/blog/_index.md

SEO сторінки блогу

content/blog/post.md

SEO окремої статті (через CMS)

layouts/partials/head.html

Мета-теги, Open Graph, Twitter Cards

static/admin/config.yml

Налаштування CMS

static/admin/index.html

Інтерфейс CMS
```

        **Напишіть "CMS працює!" коли можете увійти в адмін-панель! 🎉**

![1764276453120_зображення.png](https://ai24q.pages.dev/api/d3122760-b659-42ee-b097-ae6beeac73a1/files/0d82ecd5-f263-4416-b9bb-115b91660967/preview)

Перейти до місця, на якому ви зупинилися (пост 10)Перейти до останньої відповідіПерейти до початку Перейти до основного вмісту [Посібник із підтримки] Я розгорнув сайт, але все ще не бачу повідомлення «Сторінку не знайдено» Підтримка Посібники Посібник з підтримки конфігурації налаштування, [РОЗСЛІДУВАННЯ]: Ручні п

вклеєний

Пн.

Не можу зайти!!!!

# 🔴 ПРОБЛЕМА: "Автентифікацію перервано. Повторіть спробу."

Ця помилка виникає через **неправильне налаштування OAuth App** . Давайте виправимо!

## ✅ РІШЕННЯ: Крок за кроком

### 🔧 Крок 1: Перевірте налаштування GitHub OAuth App

1. Перейдіть на: [https://github.com/settings/developers](https://github.com/settings/developers)
2. Знайдіть свою програму **"Ai24q"**
3. Натисніть на неї
4. **ПЕРЕВІРТЕ ці поля:**

#### ✏️ Має бути **ТОЧНО** так:

```plain
Поле

Значення

Назва програми

Ai24q(або будь-яка назва)

URL-адреса головної сторінки

https://ai24q.pages.dev

URL-адреса зворотного виклику авторизації

https://sveltia-cms-auth.netlify.app/callback
```

5. Якщо щось не так — **виправте** і натисніть **«Оновити додаток»**

### 🔧 Крок 2: Віддати старі секрети (якщо є проблеми)

Ви маєте **2 секрети клієнта** . Якщо вони обидва не працюють:

1. На сторінці вашої програми OAuth
2. Знайдіть **«Клієнтські секрети»**
3. Натисніть **«Видалити»** на обох старих секретах\*\*
**4. Натисніть \*\*«Створити новий секрет клієнта»**
4. **НІЧОГО НЕ КОПІЮЙТЕ** — Sveltia CMS не потребує секрету!

### 🔧 Крок 3: Переконайтеся, що `config.yml`правильний

Відкрийте файл **`static/admin/config.yml`**і **ПЕРЕВІРТЕ** ці рядки:

ЯМЛ

```plain
backend:
  name: github
  repo: Ai24q/site
  branch: main
  base_url: https://sveltia-cms-auth.netlify.app
```

  **ВАЖЛИВО:**

- `repo:`має бути **точно** `Ai24q/site` (з великої букви!)\`
`- `branch:`має бути `main`(не `master\`)

### 🔧 Крок 4: Очистіть кеш браузера

Іноді браузер кешує стару інформацію:

1. Натисніть **Ctrl + Shift + Delete** (Windows) або **Cmd + Shift + Delete** (Mac)\*\*
**2. Виберіть \*\*"Cookies та інші веб-сайти"**
2. Виберіть **"Зображення та файли в кеші"**
3. Натисніть **"Видалити дані"**

### 🔧 Крок 5: Спробуйте ще раз

1. Перейдіть на: [**https://ai24q.pages.dev/admin**](https://ai24q.pages.dev/admin)\*\*
**2. Натисніть \*\*«Увійти за допомогою GitHub»**
2. GitHub запитати авторизацію — **натисніть «Авторизувати Ai24q»**
3. Має відкритися CMS! ✅

## 🚨 Якщо все ще не працює

### Альтернативний метод: використання персонального маркера доступу (PAT)

Це більш надійний спосіб для тестування.

#### 1️⃣ Створіть особистий маркер доступу на GitHub:

1. Перейдіть на: [https://github.com/settings/tokens](https://github.com/settings/tokens)
2. Натисніть **«Створити новий маркер»** → **«Створити новий маркер (класичний)»**
3. Назвіть його:`Sveltia CMS`
4. Виберіть **такі дозволи:**

        - ✅ `repo`(всі підпункти)

5. Натисніть **«Створити маркер»**
6. **СКОПІЮЙТЕ токен** (він показується тільки раз!)

#### 2️⃣ Увійдіть через PAT:

1. Перейдіть на: [**https://ai24q.pages.dev/admin**](https://ai24q.pages.dev/admin)\*\*
**2. Натисніть \*\*«Увійти за допомогою GitHub за допомогою PAT»**
2. Вставте свій токен
3. Натисніть **«Увійти»**

## 🔍 Додаткова діагностика

### Перевірте консоль браузера:

1. Натисніть **F12** (відкриті DevTools)\*\*
**2. Перейдіть на вкладку \*\*"Консоль"**
2. Спробуйте увійти знову
3. Якщо з'являться помилки — **скопіюйте** їх і надішліть мені

## 📝 Найпоширеніші причини помилки:

1. ❌ Неправильна URL-адреса в **URL-адресу зворотного виклику авторизації**
2. ❌ Неправильна назва репозиторію в`config.yml`
3. ❌ Неправильна гілка ( `master`замість `main`)
4. ❌ Кеш браузера
