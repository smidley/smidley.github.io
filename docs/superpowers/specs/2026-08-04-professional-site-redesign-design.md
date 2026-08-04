# smidley.github.io — Professional Site Redesign

**Date:** 2026-08-04
**Status:** Approved

## Purpose

Replace the current minimal page (name + three link buttons) with a single-page
professional site that acts as a gateway for job applications. Positioning:
**engineering leadership + cloud/DevOps/platform engineering**. The URL will be
given directly to prospective employers.

## Approach

Hand-written static HTML + CSS, single page, no build step, no framework.
Push to `main` → GitHub Pages serves it (same workflow as today and as the
Cogniscale site). No JavaScript required; a small amount is acceptable only
for progressive enhancement (e.g., smooth scroll) and the page must be fully
functional without it.

## Visual Direction

- Dark, modern "cloud platform" aesthetic: deep navy/slate background,
  azure-blue accent color (nod to Azure expertise).
- Clean typography, generous spacing, subtle gradient or grid texture in the
  hero. Reference feel: modern infra-product landing pages (Vercel,
  Tailscale) — professional, not flashy.
- Fully responsive (mobile through desktop), fast, accessible (semantic HTML,
  sufficient contrast, alt text, keyboard-friendly).

## Page Structure (top to bottom)

1. **Hero**
   - Name: Scott Brant
   - Title: Senior Cloud Infrastructure & DevOps Engineer
   - One-sentence hook: 15+ years of infrastructure and leadership
     experience, Azure expert, published author.
   - Buttons: Download Resume (PDF), GitHub, Email.

2. **About**
   - 3–4 sentence bio derived from the 2025 resume profile: Azure/DevOps
     depth, leadership background, agentic-AI-in-DevOps angle, author.
   - No headshot for now; layout must look complete without one and allow a
     photo to be added later without redesign.

3. **Experience highlights**
   - Compact cards or timeline (not the full resume):
     - Senior Cloud Infrastructure DevOps Engineer, Degreed — 2018–Present
       (Azure-only infra at scale, Kubernetes/Terraform migration,
       multi-region Azure design, agentic AI in DevOps workflows).
     - Sr. Infrastructure Engineer, Atmosera — 2015–2018 (Azure/hybrid/private
       cloud architecture, PCI/HIPAA/HITRUST hardening, 99.999% VDI uptime).
     - IT Manager, Xerox — 2011–2015 (18k+ users / 35 sites, $1.3M saved via
       virtualization, $2M refresh savings, budgets and staff leadership).
     - Network Administrator, Xerox — 2007–2011 (Cisco routing/switching,
       record-setting uptime).
   - 2–3 accomplishment lines per role, quantified where possible.

4. **Book**
   - Featured card for *Agentic AI & DevOps* (Scott Brant), linking to
     https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5
   - Use a cover image if one can be sourced from
     `iCloud Documents/Agentic AI & DevOps/`; otherwise a styled text card.

5. **Skills & certifications**
   - Tag-style chips grouped by resume categories: Cloud & DevOps, Security &
     Compliance, Networking & Infrastructure, AI Tools, Platforms.
   - Certifications: Microsoft Certified Azure Administrator Associate,
     VCA-C, VCA-DCV, MCP.

6. **Footer**
   - Email (smidley@gmail.com), GitHub (github.com/smidley), Degreed profile
     (degreed.com/profile/sbrant).
   - **Excluded everywhere:** LinkedIn (not in use), phone number, street
     address.

## Assets

- **Resume PDF:** convert `Scott_Brant_Resume_2025_TwoColumn_Publications.docx`
  (iCloud Documents root) to `resume.pdf` in the repo. Try automated
  conversion (Word/AppleScript or similar); if unavailable, ask Scott to
  export the PDF manually.
- **Book cover:** source from `iCloud Documents/Agentic AI & DevOps/` if a
  usable cover exists.
- **Favicon:** simple generated monogram/mark; optional, low priority.

## Metadata / SEO

- `<title>`: "Scott Brant — Senior Cloud & DevOps Engineer" (or similar).
- Meta description and Open Graph tags (title, description, og:type) so the
  URL previews well when pasted into applications and emails.

## Must Not Break

- `.well-known/` directory (Tesla Fleet API public key — GridMind production
  depends on this path) must remain untouched and reachable.
- `.nojekyll` stays.

## Out of Scope

- Blog, multiple pages, project showcase section (Cogniscale site covers
  projects), analytics, contact forms, dark/light theme toggle.

## Testing / Acceptance

- Page renders correctly on mobile (~375px), tablet, and desktop widths.
- All links resolve (Amazon, GitHub, Degreed, mailto, resume.pdf).
- https://smidley.github.io/.well-known/appspecific/... still serves the
  Tesla key after deploy.
- Lighthouse-level sanity: no console errors, images have alt text,
  contrast is accessible.
