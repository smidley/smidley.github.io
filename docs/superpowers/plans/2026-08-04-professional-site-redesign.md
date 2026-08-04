# smidley.github.io Professional Site Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the minimal smidley.github.io page with a single-page professional site (bio, experience highlights, book, skills/certs, resume download) positioned for engineering-leadership and cloud/DevOps roles.

**Architecture:** One hand-written `index.html` + one `style.css`, served directly by GitHub Pages from `main` — no build step, no framework, no required JavaScript. Static assets (resume PDF, book cover, favicon) live in the repo.

**Tech Stack:** HTML5, CSS3, GitHub Pages. macOS `sips` for image processing.

## Global Constraints

- Repo: `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/smidley.github.io` (branch `main`, deploys on push).
- Git identity: `Scott Brant <smidley@gmail.com>`; `gh auth switch --user smidley` before pushing.
- MUST NOT touch `.well-known/appspecific/com.tesla.3p.public-key.pem` or `.nojekyll` — GridMind production depends on the Tesla key path.
- MUST NOT include: LinkedIn, phone number, street address, any JavaScript required for core function.
- Contact channels are exactly: `mailto:smidley@gmail.com`, `https://github.com/smidley`, `https://degreed.com/profile/sbrant`.
- Book link is exactly: `https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5`.
- Verification is via local HTTP server + curl + user visual check (no test framework for a static page).

---

### Task 1: Assets — resume PDF, book cover, favicon

**Files:**
- Create: `resume.pdf` (converted from docx)
- Create: `assets/book-cover.jpg`
- Create: `favicon.svg`

**Interfaces:**
- Produces: `resume.pdf`, `assets/book-cover.jpg`, `favicon.svg` at repo root — referenced by `index.html` in Task 2 with exactly those paths.

- [ ] **Step 1: Convert the resume docx to PDF**

Source: `/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Scott_Brant_Resume_2025_TwoColumn_Publications.docx`
Target: `<repo>/resume.pdf`

Try these in order; use the first that works:

(a) LibreOffice, if installed:
```bash
SRC="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Scott_Brant_Resume_2025_TwoColumn_Publications.docx"
REPO="/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/smidley.github.io"
/Applications/LibreOffice.app/Contents/MacOS/soffice --headless --convert-to pdf --outdir "$REPO" "$SRC" \
  && mv "$REPO/Scott_Brant_Resume_2025_TwoColumn_Publications.pdf" "$REPO/resume.pdf"
```

(b) Microsoft Word via AppleScript, if installed:
```bash
osascript <<'EOF'
set srcFile to POSIX file "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Scott_Brant_Resume_2025_TwoColumn_Publications.docx"
set outFile to "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/smidley.github.io/resume.pdf"
tell application "Microsoft Word"
	open srcFile
	set theDoc to active document
	save as theDoc file name outFile file format format PDF
	close theDoc saving no
end tell
EOF
```

(c) Apple Pages via AppleScript, if installed:
```bash
osascript <<'EOF'
set srcFile to POSIX file "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Scott_Brant_Resume_2025_TwoColumn_Publications.docx"
set outFile to POSIX file "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/solar/smidley.github.io/resume.pdf"
tell application "Pages"
	set theDoc to open srcFile
	export theDoc to outFile as PDF
	close theDoc saving no
end tell
EOF
```

(d) If none of the above work: STOP and ask Scott to export the docx to PDF manually and place it at `<repo>/resume.pdf`. Do not proceed with a placeholder PDF.

- [ ] **Step 2: Verify the PDF**

Run: `file "$REPO/resume.pdf" && du -h "$REPO/resume.pdf"`
Expected: `PDF document` type, size roughly 30 KB–2 MB. Also open it (`open "$REPO/resume.pdf"`) if running interactively to confirm the layout survived conversion; if the two-column layout is mangled, fall back to option (d).

- [ ] **Step 3: Create the optimized book cover**

```bash
mkdir -p "$REPO/assets"
sips -Z 900 -s format jpeg -s formatOptions 80 \
  "/Users/sbrant/Library/Mobile Documents/com~apple~CloudDocs/Documents/Agentic AI & DevOps/Ebook Cover.jpg" \
  --out "$REPO/assets/book-cover.jpg"
```

Verify: `sips -g pixelWidth -g pixelHeight "$REPO/assets/book-cover.jpg"` — longest side 900px, file under ~250 KB (`du -h`).

- [ ] **Step 4: Create the favicon**

Write `<repo>/favicon.svg` with exactly:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64">
  <rect width="64" height="64" rx="14" fill="#0b1120"/>
  <text x="32" y="43" font-family="-apple-system, 'Segoe UI', Helvetica, Arial, sans-serif" font-size="28" font-weight="700" fill="#38bdf8" text-anchor="middle">SB</text>
</svg>
```

- [ ] **Step 5: Commit**

```bash
cd "$REPO"
git add resume.pdf assets/book-cover.jpg favicon.svg
git -c user.name="Scott Brant" -c user.email="smidley@gmail.com" commit -m "Add resume PDF, book cover, and favicon assets"
```

---

### Task 2: index.html — full page structure and content

**Files:**
- Modify: `index.html` (full replacement of the 788-byte current file)

**Interfaces:**
- Consumes: `resume.pdf`, `assets/book-cover.jpg`, `favicon.svg` from Task 1.
- Produces: element IDs `about`, `experience`, `book`, `skills` and classes consumed by `style.css` in Task 3: `hero`, `eyebrow`, `title`, `hook`, `cta`, `btn`, `btn-primary`, `container`, `section-label`, `cards`, `card`, `card-meta`, `book-card`, `book-cover`, `book-body`, `chip-groups`, `chip-group`, `chips`, `chip`, `certs`, `site-footer`, `footer-links`.

- [ ] **Step 1: Replace index.html with the new page**

Write `<repo>/index.html` with exactly:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Scott Brant — Senior Cloud &amp; DevOps Engineer</title>
  <meta name="description" content="Senior Cloud Infrastructure &amp; DevOps Engineer with 15+ years of infrastructure and leadership experience. Azure expert, Kubernetes and Terraform practitioner, author of Agentic AI &amp; DevOps.">
  <meta property="og:title" content="Scott Brant — Senior Cloud &amp; DevOps Engineer">
  <meta property="og:description" content="15+ years of cloud infrastructure and leadership. Azure expert, published author on agentic AI in DevOps.">
  <meta property="og:type" content="website">
  <meta property="og:url" content="https://smidley.github.io">
  <meta property="og:image" content="https://smidley.github.io/assets/book-cover.jpg">
  <link rel="icon" href="favicon.svg" type="image/svg+xml">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <main>
    <header class="hero">
      <div class="container">
        <p class="eyebrow">Portland, Oregon · Remote</p>
        <h1>Scott Brant</h1>
        <p class="title">Senior Cloud Infrastructure &amp; DevOps Engineer</p>
        <p class="hook">15+ years building, scaling, and leading cloud infrastructure — Azure expert, Kubernetes &amp; Terraform practitioner, and published author on agentic AI in DevOps.</p>
        <div class="cta">
          <a class="btn btn-primary" href="resume.pdf">Download Resume</a>
          <a class="btn" href="https://github.com/smidley">GitHub</a>
          <a class="btn" href="mailto:smidley@gmail.com">Email Me</a>
        </div>
      </div>
    </header>

    <section id="about" class="container">
      <h2><span class="section-label">About</span></h2>
      <p>I'm a detail-oriented senior cloud engineer with 15+ years of hands-on technical and leadership experience. At Degreed, I've run the Azure infrastructure behind degreed.com since 2018, helping scale it from startup to global enterprise — multi-region architecture, Kubernetes microservices, Terraform IaC, and the security and compliance posture to match.</p>
      <p>Before that I architected Azure, hybrid, and private cloud environments for a managed cloud provider, and spent eight years at Xerox leading IT operations for 18,000+ users across 35 sites. I'm a strong communicator who translates complex systems into clear solutions, and I wrote a book about the thing I care most about right now: putting agentic AI to work in real DevOps practice.</p>
    </section>

    <section id="experience" class="container">
      <h2><span class="section-label">Experience</span></h2>
      <div class="cards">
        <article class="card">
          <h3>Senior Cloud Infrastructure DevOps Engineer</h3>
          <p class="card-meta">Degreed · Remote · 2018–Present</p>
          <ul>
            <li>Ran Azure-only infrastructure for degreed.com, scaling from startup to global enterprise.</li>
            <li>Led multi-region Azure design — App Services, Front Door, SQL, Key Vault, AVD.</li>
            <li>Shifted workloads to Kubernetes microservices and Terraform IaC, reducing costs.</li>
            <li>Introduced agentic AI tools into DevOps workflows, accelerating delivery.</li>
          </ul>
        </article>
        <article class="card">
          <h3>Sr. Infrastructure Engineer</h3>
          <p class="card-meta">Atmosera · Beaverton, OR · 2015–2018</p>
          <ul>
            <li>Architected Azure, hybrid, and private cloud environments across datacenters.</li>
            <li>Hardened environments for PCI, HIPAA, and HITRUST compliance.</li>
            <li>Led VDI deployments with 99.999% uptime for manufacturing clients.</li>
          </ul>
        </article>
        <article class="card">
          <h3>IT Manager</h3>
          <p class="card-meta">Xerox · Portland, OR · 2011–2015</p>
          <ul>
            <li>Led full IT operations for 18,000+ global users across 35 sites.</li>
            <li>Saved $1.3M via virtualization; delivered $2M savings in refresh projects.</li>
            <li>Directed IT budgets, audits, and staff across multiple regions.</li>
          </ul>
        </article>
        <article class="card">
          <h3>Network Administrator</h3>
          <p class="card-meta">Xerox · Portland, OR · 2007–2011</p>
          <ul>
            <li>Managed network and domain access for 700+ users — Cisco routing/switching, Windows servers.</li>
            <li>Delivered record-setting uptime with redundant systems.</li>
          </ul>
        </article>
      </div>
    </section>

    <section id="book" class="container">
      <h2><span class="section-label">Book</span></h2>
      <div class="book-card">
        <img class="book-cover" src="assets/book-cover.jpg" alt="Cover of Agentic AI &amp; DevOps by Scott Brant" width="200">
        <div class="book-body">
          <h3>Agentic AI &amp; DevOps</h3>
          <p>Strategies for integrating agentic AI into Azure and DevOps practice — architecture, cost optimization, and real-world case studies. Written and self-published in 2025.</p>
          <a class="btn btn-primary" href="https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5">View on Amazon</a>
        </div>
      </div>
    </section>

    <section id="skills" class="container">
      <h2><span class="section-label">Skills &amp; Certifications</span></h2>
      <div class="chip-groups">
        <div class="chip-group">
          <h3>Cloud &amp; DevOps</h3>
          <ul class="chips">
            <li class="chip">Azure</li><li class="chip">Terraform</li><li class="chip">Kubernetes</li><li class="chip">Helm</li><li class="chip">IaC</li><li class="chip">App Services</li><li class="chip">SQL</li><li class="chip">Redis</li><li class="chip">CDN</li><li class="chip">WAF</li>
          </ul>
        </div>
        <div class="chip-group">
          <h3>Security &amp; Compliance</h3>
          <ul class="chips">
            <li class="chip">Cloud governance</li><li class="chip">FinOps</li><li class="chip">Disaster Recovery</li><li class="chip">PCI</li><li class="chip">HIPAA</li><li class="chip">HITRUST</li>
          </ul>
        </div>
        <div class="chip-group">
          <h3>Networking &amp; Infrastructure</h3>
          <ul class="chips">
            <li class="chip">Cisco</li><li class="chip">Palo Alto</li><li class="chip">Fortigate</li><li class="chip">VMware</li><li class="chip">SANs</li><li class="chip">NetApp</li><li class="chip">PureStorage</li>
          </ul>
        </div>
        <div class="chip-group">
          <h3>AI Tooling</h3>
          <ul class="chips">
            <li class="chip">Factory.ai</li><li class="chip">Cursor</li><li class="chip">Augment</li><li class="chip">GitHub Copilot</li>
          </ul>
        </div>
        <div class="chip-group">
          <h3>Platforms</h3>
          <ul class="chips">
            <li class="chip">macOS</li><li class="chip">Linux</li><li class="chip">Windows</li>
          </ul>
        </div>
      </div>
      <p class="certs">Microsoft Certified: Azure Administrator Associate · VMware VCA-Cloud · VMware VCA-DCV · Microsoft Certified Professional</p>
    </section>
  </main>

  <footer class="site-footer">
    <div class="container">
      <ul class="footer-links">
        <li><a href="mailto:smidley@gmail.com">smidley@gmail.com</a></li>
        <li><a href="https://github.com/smidley">github.com/smidley</a></li>
        <li><a href="https://degreed.com/profile/sbrant">degreed.com/profile/sbrant</a></li>
      </ul>
      <p>© 2026 Scott Brant · smidley.github.io</p>
    </div>
  </footer>
</body>
</html>
```

- [ ] **Step 2: Verify content and constraints**

```bash
cd "$REPO"
grep -c "linkedin" index.html; grep -c "503-405" index.html   # both must output 0
grep -o 'href="[^"]*"' index.html | sort -u                    # eyeball: only expected URLs
```
Expected: no LinkedIn, no phone. hrefs are exactly: `resume.pdf`, `style.css` (link tag), `favicon.svg`, `mailto:smidley@gmail.com`, `https://github.com/smidley`, `https://degreed.com/profile/sbrant`, `https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git -c user.name="Scott Brant" -c user.email="smidley@gmail.com" commit -m "Rebuild index.html as single-page professional site"
```

---

### Task 3: style.css — full design

**Files:**
- Modify: `style.css` (full replacement of the 416-byte current file)

**Interfaces:**
- Consumes: class names and IDs produced by Task 2 (listed in Task 2 Interfaces).

- [ ] **Step 1: Replace style.css**

Write `<repo>/style.css` with exactly:

```css
:root {
  --bg: #0b1120;
  --bg-raised: #111a2e;
  --border: #1f2b45;
  --text: #e2e8f0;
  --text-muted: #94a3b8;
  --accent: #38bdf8;
  --accent-strong: #0ea5e9;
  --radius: 12px;
  --max: 960px;
}

* { box-sizing: border-box; }

body {
  margin: 0;
  background: var(--bg);
  color: var(--text);
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

.container {
  max-width: var(--max);
  margin: 0 auto;
  padding: 0 24px;
}

/* ---------- Hero ---------- */

.hero {
  padding: 96px 0 72px;
  border-bottom: 1px solid var(--border);
  background:
    radial-gradient(ellipse 90% 60% at 50% -10%, rgba(56, 189, 248, 0.14), transparent),
    linear-gradient(rgba(31, 43, 69, 0.35) 1px, transparent 1px),
    linear-gradient(90deg, rgba(31, 43, 69, 0.35) 1px, transparent 1px);
  background-size: auto, 44px 44px, 44px 44px;
}

.eyebrow {
  margin: 0 0 8px;
  color: var(--accent);
  font-size: 0.85rem;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.hero h1 {
  margin: 0;
  font-size: clamp(2.4rem, 6vw, 3.6rem);
  font-weight: 800;
  letter-spacing: -0.02em;
}

.hero .title {
  margin: 8px 0 0;
  font-size: clamp(1.1rem, 3vw, 1.4rem);
  font-weight: 600;
  color: var(--accent);
}

.hero .hook {
  margin: 20px 0 0;
  max-width: 640px;
  font-size: 1.05rem;
  color: var(--text-muted);
}

.cta {
  margin-top: 32px;
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.btn {
  display: inline-block;
  padding: 12px 22px;
  border: 1px solid var(--border);
  border-radius: 999px;
  background: var(--bg-raised);
  color: var(--text);
  font-weight: 600;
  text-decoration: none;
  transition: border-color 0.15s ease, background 0.15s ease, color 0.15s ease;
}

.btn:hover { border-color: var(--accent); color: var(--accent); }

.btn-primary {
  background: var(--accent-strong);
  border-color: var(--accent-strong);
  color: #04121f;
}

.btn-primary:hover { background: var(--accent); border-color: var(--accent); color: #04121f; }

/* ---------- Sections ---------- */

section { padding: 64px 0; }

section h2 { margin: 0 0 28px; }

.section-label {
  color: var(--accent);
  font-size: 0.9rem;
  font-weight: 700;
  letter-spacing: 0.14em;
  text-transform: uppercase;
}

#about p {
  max-width: 720px;
  margin: 0 0 16px;
  color: var(--text-muted);
  font-size: 1.05rem;
}

/* ---------- Experience cards ---------- */

.cards {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.card {
  padding: 24px;
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: var(--radius);
}

.card h3 { margin: 0; font-size: 1.05rem; }

.card-meta {
  margin: 4px 0 12px;
  color: var(--accent);
  font-size: 0.85rem;
  font-weight: 600;
}

.card ul { margin: 0; padding-left: 18px; color: var(--text-muted); font-size: 0.95rem; }

.card li { margin-bottom: 6px; }

/* ---------- Book ---------- */

.book-card {
  display: flex;
  gap: 32px;
  align-items: flex-start;
  padding: 28px;
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: var(--radius);
}

.book-cover {
  width: 200px;
  height: auto;
  flex-shrink: 0;
  border-radius: 6px;
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.45);
}

.book-body h3 { margin: 0 0 10px; font-size: 1.3rem; }

.book-body p { margin: 0 0 20px; color: var(--text-muted); }

/* ---------- Skills ---------- */

.chip-groups {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;
}

.chip-group h3 {
  margin: 0 0 10px;
  font-size: 0.85rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--text-muted);
}

.chips {
  margin: 0;
  padding: 0;
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.chip {
  padding: 5px 14px;
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 999px;
  font-size: 0.85rem;
  color: var(--text);
}

.certs {
  margin: 28px 0 0;
  color: var(--text-muted);
  font-size: 0.95rem;
}

/* ---------- Footer ---------- */

.site-footer {
  border-top: 1px solid var(--border);
  padding: 40px 0;
  color: var(--text-muted);
  font-size: 0.9rem;
}

.footer-links {
  margin: 0 0 12px;
  padding: 0;
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 24px;
}

.footer-links a { color: var(--text); text-decoration: none; }

.footer-links a:hover { color: var(--accent); }

.site-footer p { margin: 0; }

/* ---------- Responsive ---------- */

@media (max-width: 720px) {
  .hero { padding: 64px 0 48px; }
  section { padding: 48px 0; }
  .cards, .chip-groups { grid-template-columns: 1fr; }
  .book-card { flex-direction: column; }
  .book-cover { width: 160px; }
}
```

- [ ] **Step 2: Serve locally and verify**

```bash
cd "$REPO" && python3 -m http.server 8931 &
sleep 1
curl -s -o /dev/null -w "%{http_code} index\n" http://localhost:8931/
curl -s -o /dev/null -w "%{http_code} css\n" http://localhost:8931/style.css
curl -s -o /dev/null -w "%{http_code} resume\n" http://localhost:8931/resume.pdf
curl -s -o /dev/null -w "%{http_code} cover\n" http://localhost:8931/assets/book-cover.jpg
curl -s -o /dev/null -w "%{http_code} favicon\n" http://localhost:8931/favicon.svg
curl -s -o /dev/null -w "%{http_code} tesla-key\n" http://localhost:8931/.well-known/appspecific/com.tesla.3p.public-key.pem
```
Expected: six lines, all `200`. Then `open http://localhost:8931/` for a visual check (desktop width + narrow the window to ~375px to verify the single-column layout). Kill the server afterward (`kill %1`).

- [ ] **Step 3: Commit**

```bash
git add style.css
git -c user.name="Scott Brant" -c user.email="smidley@gmail.com" commit -m "Add dark cloud-focused design system"
```

---

### Task 4: Deploy and verify live

**Files:**
- None new — push existing commits.

**Interfaces:**
- Consumes: all commits from Tasks 1–3.

- [ ] **Step 1: Confirm working tree is clean and push**

```bash
cd "$REPO"
git status -sb        # expect: clean, ahead of origin/main
gh auth switch --user smidley
git push origin main
```

- [ ] **Step 2: Verify the live site (GitHub Pages may take 1–2 min)**

```bash
sleep 90
curl -s -o /dev/null -w "%{http_code} index\n" https://smidley.github.io/
curl -s -o /dev/null -w "%{http_code} resume\n" https://smidley.github.io/resume.pdf
curl -s -o /dev/null -w "%{http_code} cover\n" https://smidley.github.io/assets/book-cover.jpg
curl -s -o /dev/null -w "%{http_code} tesla-key\n" https://smidley.github.io/.well-known/appspecific/com.tesla.3p.public-key.pem
curl -s https://smidley.github.io/ | grep -c "Senior Cloud Infrastructure"
```
Expected: all `200`; grep count ≥ 1. The tesla-key check is the critical regression gate — if it is not 200, investigate immediately (GridMind production depends on it).

- [ ] **Step 3: External link spot-check**

```bash
curl -s -o /dev/null -w "%{http_code}\n" -L "https://www.amazon.com/Agentic-AI-DevOps-Architecture-Partnership/dp/B0FLWVRDV5" -A "Mozilla/5.0"
curl -s -o /dev/null -w "%{http_code}\n" -L "https://degreed.com/profile/sbrant" -A "Mozilla/5.0"
```
Expected: 200 (Amazon sometimes returns 503 to curl — if so, verify manually in a browser).

- [ ] **Step 4: Final visual check with user**

Ask Scott to open https://smidley.github.io on desktop and phone and confirm the design before calling it done.
