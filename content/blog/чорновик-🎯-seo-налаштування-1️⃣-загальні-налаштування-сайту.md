---
title: Чорновик 🎯 SEO налаштування 1️⃣ Загальні налаштування сайту
description: ''
date: 2025-11-28
image: ''
tags: []
categories: []
draft: true
---
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
  twitter_card: "summary_large_image"
  twitter_site: "@ai24q"
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
<meta property="og:image" content="{{ if .Params.image }}{{ .Params.image | absURL }}{{ else }}{{ site.Params.og_image }}{{ end }}">
<meta property="og:site_name" content="{{ site.Title }}">

<!-- Twitter Card -->
<meta name="twitter:card" content="{{ site.Params.twitter_card }}">
<meta name="twitter:site" content="{{ site.Params.twitter_site }}">
<meta name="twitter:title" content="{{ .Title }}">
<meta name="twitter:description" content="{{ if .Description }}{{ .Description }}{{ else }}{{ site.Params.description }}{{ end }}">
<meta name="twitter:image" content="{{ if .Params.image }}{{ .Params.image | absURL }}{{ else }}{{ site.Params.og_image }}{{ end }}">

<!-- Canonical URL -->
<link rel="canonical" href="{{ .Permalink }}">
```

## 🚀 Фінальна перевірка SEO

Після того, як все налаштовуєте, перевірте:

1. **Консоль пошуку Google** : [https://search.google.com/search-console](https://search.google.com/search-console)
2. **Налагоджувач Facebook** : [https://developers.facebook.com/tools/debug/](https://developers.facebook.com/tools/debug/)
3. **Валідатор карток у Твіттері** : [https://cards-dev.twitter.com/validator](https://cards-dev.twitter.com/validator)
