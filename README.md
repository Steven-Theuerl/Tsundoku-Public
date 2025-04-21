# Tsundoku - Personal Blog Platform

**A fast, beautiful blog engine built with Sanity & Next.js — custom CMS, optimized delivery, and developer-first architecture.**

Tsundoku is a performant, CMS-powered blog site built with Next.js, Sanity, and TypeScript—designed for long-form writing, clean architecture, and near-perfect Lighthouse scores. What began as a collaboration between myself and my partner quickly evolved into a full-stack playground for refining front-end engineering, API design, and CMS-driven architecture.

The platform is powered by a custom-built Sanity Studio backend, using server-side rendering (SSR) and incremental static regeneration (ISR) strategies to deliver content efficiently at scale. A recent deep refactor further optimized performance, reduced page weight, and elevated Tsundoku into one of my most technically satisfying builds to date.

<img width="1370" alt="tsundoku-preview" src="https://github.com/user-attachments/assets/5624b7c3-1049-45b4-8c94-26d3b4992c70" />

---

## 🔗 Live Site

👉 [https://www.tsundoku.blog/](https://www.tsundoku.blog/)

If you’d like to discuss the code, implementation decisions, or the future of the platform, I’d love to connect: [mr.steven.theuerl@gmail.com](mailto:mr.steven.theuerl@gmail.com)

---

## 🔒 Code Access

This repository is for showcase and portfolio purposes only.  
The source code is private and not included here.

If you're an employer, collaborator, or reviewer interested in viewing the codebase,  
please reach out directly to discuss access.

---

## 🧱 Architecture Overview

- **Frontend**: Next.js (App Router) + Tailwind CSS + TypeScript
- **Backend**: Sanity.io (Headless CMS), GROQ for content queries
- **Rendering**: Server-side rendering (SSR) for HTML content, followed by client hydration for interactivity and image handling
- **Media Handling**: Sharp + FFmpeg CLI pipelines, Sanity CDN
- **SEO**: robots.txt + sitemap - dynamic OG generation (WIP)

---

## 🔧 Technologies Used

- Next.js 14 (App Router)
- TypeScript 5+
- TailwindCSS 4+
- Sanity.io (v3)
- GROQ, Portable Text
- Sharp, FFmpeg
- Lighthouse / DevTools

---

## 🧠 Project Overview

- Built using **Next.js (App Router)**, **TypeScript**, **Tailwind CSS**, and **Sanity.io** as a headless CMS  
- Designed to support structured content, real-time previewing, and rich article formatting  
- Clean and responsive layout with modular components and optimized static and dynamic rendering paths  
- Content managed through a fully customized studio with cookie-based draft previewing and custom schema validation  
- Powered by server-side GROQ queries and portable text rendering for maximum control over nested content blocks  

---

## 🚀 Refactor & Optimization Highlights

During a recent deep-dive optimization, I rebuilt major portions of Tsundoku’s rendering logic, asset handling, and routing behavior:

- Replaced legacy PNG and GIF assets with **WebP** and **MP4**, using **Sharp** and **FFmpeg** via custom CLI tools  
- Rewrote all media rendering through a centralized `<SanityImage>` component, supporting image/video detection, eager/priority loading, and responsive layouts  
- Refactored font loading to use **self-hosted WOFF2** bundles and preload strategies  
- Reorganized dynamic route logic and reduced the main content rendering code from ~800 lines to ~30 lines of array-driven logic with safety guards
```ts
  const contentSections = Array.from({ length: 25 }, (_, i) => {
    const index = i + 1;
    return {
      id: `BodyText${index}`,
      text: post[`BodyText${index}`],
      quote: post[`BodyQuote${index}`],
      image: post[`BodyImage${index}`],
      imageAlt: post[`BodyImage${index}Alt`],
      caption: post[`BodyImageCaption${index}`],
    };
  });
```
- Fixed mobile Safari layout quirks with explicit `flex` sizing CSS logic like max-height and align-self: stretch
- cleaned up robots/sitemap configuration for SEO
- Added explicit cache-control strategies (WIP)

---

## 🔍 Average Results

| Metric                | Before         | After          |
|----------------------|----------------|----------------|
| LCP                  | ~1.2s          | **>0.8s**       |
| DOM Load Time        | ~1200ms        | **~300ms**      |
| Total Media Size     | ~60MB          | **~4.5MB**      |
| Lighthouse Scores    | 65–70          | **95–100**      |


## 📊 PageSpeed Insights Audit Results

Here’s a recent performance audit for Tsundoku’s homepage after the full optimization refactor, using Google’s official PageSpeed Insights tool.

---

### 🖥️ Desktop

<img width="770" alt="Tsundoku-PageInsights" src="https://github.com/user-attachments/assets/6d5e265f-b51d-4fdb-99cd-90ff39cdb333" />

| Metric                   | Value  |
|--------------------------|--------|
| Performance              | 100    |
| Accessibility            | 94     |
| Best Practices           | 96     |
| SEO                      | 100    |
| First Contentful Paint   | 0.2s   |
| Largest Contentful Paint | 0.7s   |
| Total Blocking Time      | 0ms    |
| Cumulative Layout Shift  | 0.003  |
| Speed Index              | 0.8s   |

---

### 📱 Mobile

<img width="778" alt="Tsundoku_Mobile-PageInsights" src="https://github.com/user-attachments/assets/80b2cb9a-425b-49f2-8adf-769fa73c9666" />

| Metric                   | Value  |
|--------------------------|--------|
| Performance              | 93     |
| Accessibility            | 93     |
| Best Practices           | 96     |
| SEO                      | 100    |
| First Contentful Paint   | 1.2s   |
| Largest Contentful Paint | 3.2s   |
| Total Blocking Time      | 0ms    |
| Cumulative Layout Shift  | 0.032  |
| Speed Index              | 1.8s   |

---

These results were achieved by reducing media weight by 90%+, replacing PNGs and GIFs with WebP and MP4, locally bundling WOFF2 fonts, tightening typing guards, and delegating rendering between server (SSR) and client (CSR) for optimal load behavior.

> 🛠️ **Planned:** I intend to reduce mobile LCP further by conditionally rendering lower-resolution Main Visuals for small screens.

---

## ✨ Core Features

- Dynamic article generation with Sanity slugs  
- Indexed content sections with in-page anchor links, nested filtering, and search support 
- Related content logic using tag relevance and weighted category intersection scoring
```ts
  const sortedRelatedPosts = post.relatedPosts
    .map((relatedPost: any) => {
      let result = 0;
      if (post.category1 === relatedPost.category1) result += 4;
      if (post.category1 === relatedPost.category2) result += 3;
      if (post.category1 === relatedPost.category3) result += 2;
      if (post.category2 === relatedPost.category1) result += 2;
      if (post.category2 === relatedPost.category2) result += 2;
      if (post.category2 === relatedPost.category3) result += 1;
      if (post.category3 === relatedPost.category3) result += 2;
      if (post.category3 === relatedPost.category2) result += 1;
      if (post.category3 === relatedPost.category1) result += 1;
      return { ...relatedPost, result };
    })
    .sort((a: any, b: any) => b.result - a.result)
    .slice(0, 3); 
```
- Fully responsive layout and stateful navigation  
- Serverless email contact form using client-side submission 
- Optimized asset delivery via Sanity CDN + SSR-aware fetch logic and IST delivery 
- Cookie-based draft preview system integrated with the CMS via custom APIs
- Self-hosted Fonts that are bundled locally with preload strategies using WOFF2 to eliminate fetch delays and blockages from third-party CDNs

---

## 🧩 Key Challenges

- Implemented a custom draft preview system using cookies and middleware to protect unpublished content
- Converted GIFs to MP4 with FFmpeg and integrated conditional rendering for optimized media delivery
- Refactored a bloated rendering system into a dynamic, index-safe array model (~800 lines → ~30 lines)
- Created a modular component that fetches and renders images and videos with conditional styling based on parsed property names
- Strategically delegated fetching and rendering responsibilities between server and client to optimize load performance

---

## 📈 Roadmap

- Add Dark Mode for better accessibility and user comfort
- Implement server-side Tailwind styling where applicable for bundled, faster styling
- Add multi-language (i18n) routing for global audiences
- Additional content types: timelines, project breakdowns, media essays
- Define explicit cache-control strategies for smarter asset delivery
- Implement dynamic Open Graph image generation for each article
- Add support for smaller Main Visuals on mobile to reduce LCP without sacrificing design
- Enhance offline-first capabilities and enable progressive enhancement
- Add a Low-Data Mode toggle to better serve international users on limited networks

---

## 🙌 Acknowledgments
Built and maintained by [Steven Theuerl](https://www.linkedin.com/in/steven-theuerl-919175209/)

Special thanks to my partner, [Yu Ito](https://www.linkedin.com/in/yu-ito/), for creative direction and content collaboration. Her design expertise played a key role in shaping Tsundoku’s visual identity and editorial flow.

---

## ⚠️ License & Use

This is a private portfolio project. Please do not copy, clone, or reuse any part of the design, content, or logic.

All content © 2025 Steven Theuerl. Reach out directly for collaboration or licensing inquiries.

**Note: This project does not store or persist user data. The contact form sends email via EmailJS and does not log or track submissions.***

**© 2025 Steven Theuerl. All rights reserved.**
