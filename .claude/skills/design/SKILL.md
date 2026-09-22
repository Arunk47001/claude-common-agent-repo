---
version: "alpha"
name: "design"
description: "Clean, vibrant, Material Design-inspired visual style. Well-suited to AI/developer-facing products, but broadly usable for landing pages, marketing sites, SaaS dashboards, and modern web/app UI in general."
colors:
  primary: "#4285F4"
  secondary: "#EA4335"
  tertiary: "#FBBC05"
  neutral: "#34A853"
  surface: "#FFFFFF"
  accent: "#F8F9FA"
typography:
  h1:
    fontFamily: Roboto
    fontSize: 2.5rem
    fontWeight: 700
  body-md:
    fontFamily: Roboto
    fontSize: 1rem
    fontWeight: 400
rounded:
  sm: 8px
  md: 16px
  lg: 24px
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral}"
    rounded: "{rounded.sm}"
    padding: 12px
---

## Overview

A clean, vibrant, Material Design-inspired visual style. It grew out of AI-product design work, so it's a natural fit for developer tools, generative-AI platforms, and SaaS dashboards — but nothing about it is AI-specific; the same restrained-color, physics-based approach works just as well for a marketing landing page, a content site, or any modern web/app UI. Material Design was always opinionated about physics. Surfaces had weight, shadows told you where things lived in z-space, motion followed real-world curves. Then Google did something interesting — they stopped pretending the screen was paper.

Material You introduced dynamic color extraction, pulling palettes from wallpapers and user preferences. Surfaces became adaptive. But the real shift came when Gemini needed a face. AI features couldn't just inherit the same button styles and card layouts. They needed something that communicated intelligence without pretending to be human. The shimmer effects, the generative text animations, the way responses build themselves on screen — that's a distinct visual language growing inside Material's skeleton.

The adaptive color system now serves double duty: personal expression for the user, semantic signaling for AI states. Processing looks different from responding. Confidence has a color. Uncertainty has one too. It's Material Design learning to speak a new dialect.

- Density: 3/10 — Airy
- Variance: 3/10 — Restrained
- Motion: 4/10 — Subtle

- **Style:** Clean, Vibrant, User-Friendly
- **Keywords:** modern, vibrant, clean, intuitive, versatile; equally at home for AI/developer products, SaaS, or general marketing/web use
- **Era:** Contemporary
- **Light/Dark:** Light by default; dark-mode theming can be layered onto the same token structure (swap `surface`/`accent`/text colors for their dark equivalents)

## Colors

- **Vibrant Blue** (#4285F4) — Accent highlight, links and focus states
- **Bold Red** (#EA4335) — Error states, destructive actions
- **Energetic Yellow** (#FBBC05) — Warning states, attention indicators
- **Vivid Green** (#34A853) — Supporting palette color
- **White** (#FFFFFF) — Secondary surface
- **Light Gray** (#F8F9FA) — Secondary text, borders, muted elements
- **Dark Gray** (#3C4043) — Deep contrast surface
- **Cyan** (#00BCD4) — Extended palette, decorative use

This four-color-plus-neutrals palette reads as "Google Material," which is exactly why it works well for AI/developer products — but the same structure (one primary accent, a semantic error/warning/success trio, and a neutral surface scale) is a solid basis for any product's palette. Swap the hues while keeping the roles if the target product has its own brand colors.


## Typography

- **Display / Hero:** Roboto — Weight 700, tight tracking, used for headline impact
- **Body:** Roboto — Weight 400, 16px/1.6 line-height, max 72ch per line
- **UI Labels / Captions:** Roboto — 0.875rem, weight 500, slight letter-spacing
- **Monospace:** JetBrains Mono — Used for code, metadata, and technical values

Scale:
- Hero: clamp(2.5rem, 5vw, 4rem)
- H1: 2.25rem
- H2: 1.5rem
- Body: 1rem / 1.6
- Small: 0.875rem


## Layout

- **Grid:** CSS Grid primary. Max-width containment: 1280px centered with 1.5rem side padding.
- **Spacing rhythm:** Balanced. Base unit: 0.5rem (8px).
- **Section vertical gaps:** clamp(4rem, 8vw, 8rem).
- **Hero layout:** Split-screen (text left, visual right) — for marketing/landing pages. A dashboard or in-app screen skips the hero entirely and starts from its primary content/data view.
- **Feature sections:** Zig-zag alternating text+image rows. No 3-equal-columns. (Applies to presentational/marketing content; data-dense app screens should use whatever grid best serves the data instead.)
- **Mobile collapse:** All multi-column layouts collapse below 768px. No horizontal overflow.
- **z-index contract:** base (0) / sticky-nav (100) / overlay (200) / modal (300) / toast (500).


## Elevation & Depth

Subtle shadows (Material Design), restrained dynamic gradients, responsive micro-interactions, legible sans-serif typography, floating elements, and — where relevant to the product — AI-style loading animations and abstract data illustrations. The AI-flavored motion cues are optional flourishes, not a requirement: a non-AI product simply omits them and keeps everything else.

- **Physics:** Ease-out curves, 200-300ms duration. Smooth and predictable.
- **Entry animations:** Fade + translate-Y (16px → 0) over 420ms ease-out. Staggered cascades for lists: 80ms between items.
- **Hover states:** Subtle color shift + shadow adjustment over 200ms.
- **Page transitions:** Fade only (200ms).
- **Performance:** Only transform and opacity animated. No layout-triggering properties.


## Shapes

Base corner radius: 8px. See rounded tokens in front matter for the full scale.


## Components

- **Primary Button:** Rounded (8px) shape. Accent color fill. Hover: 8% darken + subtle lift shadow. Active: -1px translate tactile press. Font weight 600. No outer glows.
- **Secondary / Ghost Button:** Outline variant. 1.5px border in muted color. Text in primary color. Hover: subtle background fill.
- **Cards:** Rounded (8px) corners. Surface background. Subtle shadow (0 2px 12px rgba(0,0,0,0.06)). 1px border stroke.
- **Inputs:** Label above input. 1px border stroke. Focus ring: 2px accent color offset 2px. Error text below in semantic red. No floating labels.
- **Navigation:** Primary surface background. Active item: accent color indicator. Font weight 500 when active.
- **Skeletons:** Shimmer animation matching component dimensions. No circular spinners.
- **Empty States:** Icon-based composition with descriptive text and action button.


## Do's and Don'ts

- No emojis in UI — use icon system only (Lucide, Heroicons, or the product's existing icon set)
- No pure black (#000000) — use off-black or charcoal variants
- No oversaturated accent colors (saturation cap: 80%)
- No 3-column equal-width feature layouts on marketing/presentational content — use zig-zag or asymmetric grid
- No `h-screen` — use `min-h-[100dvh]`
- No generic copywriting clichés: "Elevate", "Seamless", "Unleash", "Next-Gen", "Revolutionize" (common in AI marketing, but worth avoiding everywhere)
- No broken external image links — use picsum.photos or inline SVG
- No generic lorem ipsum in demos — use realistic sample content relevant to the actual product

- Do use vibrant, purposeful brand colors (the product's own, once known — see Colors)
- Do use Material Design-style subtle shadows for depth
- Do use dynamic gradients sparingly, only where they serve a purpose
- Do use legible, readable typography over stylistic flourish
- Do reserve AI-style loading/generative animations for products that are actually AI-driven
- Do keep the actual target audience in focus over generic design trends


## Use Case

Landing pages and marketing sites, generative-AI/developer-tool products, SaaS dashboards, and modern web/app UI in general. Strongest fit for AI-forward and developer-facing products (where the Material-inspired motion and shadow language reads as native), but nothing here is exclusive to that category — treat it as a solid default style for any clean, modern digital product.
