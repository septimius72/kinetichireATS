# KineticHire ATS

> **KineticHire ATS: Zero-dependency, single-file applicant tracking suite for modern recruiters.**

KineticHire is a high-speed, enterprise-grade Applicant Tracking System packaged entirely into a **single, standalone `.html` file**. It requires no Node.js runtime, no backend microservices, and no database configuration. Open it directly in any modern desktop or mobile browser to run an end-to-end recruitment pipeline with real-time analytics, synthesised audio cues, multi-criteria scoring, and automated candidate velocity tracking.

## Preview


*Figure 1: Workspace split view featuring real-time funnel velocity metrics, candidate sorting, and multi-criteria scorecards.*

## Table of Contents

* [Core Highlights](#core-highlights)

* [Recruiter Operations Manual](#recruiter-operations-manual)

  * [1. Lifecycle Stages & Candidate Flow](#1-lifecycle-stages--candidate-flow)

  * [2. Multi-Criteria Scorecards & Evaluation](#2-multi-criteria-scorecards--evaluation)

  * [3. Pipeline Velocity & Stalled Detection](#3-pipeline-velocity--stalled-detection)

  * [4. Templated Email System](#4-templated-email-system)

  * [5. Resumes, Links & Notes](#5-resumes-links--notes)

  * [6. Keyboard Navigation](#6-keyboard-navigation)

* [Technical Architecture](#technical-architecture)

  * [Client-Side State Engine](#client-side-state-engine)

  * [Web Audio API Sound Synthesis](#web-audio-api-sound-synthesis)

  * [DOM Draggable Splitter Implementation](#dom-draggable-splitter-implementation)

  * [Chart.js Lifecycle & Color Management](#chartjs-lifecycle--color-management)

  * [Data Schemas (JSON & CSV)](#data-schemas-json--csv)

* [Data Security & Compliance](#data-security--compliance)

* [Quick Start & Deployment](#quick-start--deployment)

  * [Option A: Local Double-Click](#option-a-local-double-click-no-install-required)

  * [Option B: GitHub Pages](#option-b-github-pages-1-minute-setup)

* [Contributing](#contributing)

* [License](#license)

## Core Highlights

* **Zero-Dependency Single File Architecture:** Complete application code (HTML structure, responsive design system, and reactive JavaScript) lives inside one lightweight `.html` document.

* **Power-User Ergonomics:** Complete keyboard workflow (`J`/`K` navigation, `A` to advance, `R` to reject, `E` for emails, `/` for search).

* **Data Portability:** Lossless JSON state backup/restore alongside formatted CSV exports tailored for Excel, Google Sheets, or BI systems.

* **Synthesized UI Audio Engine:** Native Web Audio API tone synthesis for auditory feedback without external audio assets or network requests.

* **Adaptive Visual System:** Default high-contrast light theme with an accessible, high-legibility dark mode and dynamic Chart.js canvas restyling.

## Recruiter Operations Manual

### 1. Lifecycle Stages & Candidate Flow

KineticHire enforces a standardized 11-stage recruitment pipeline:

| Index | Stage | Color Indicator | Typical Action | 
| ----- | ----- | ----- | ----- | 
| `1` | **New Applicant** | Slate Grey | Triage resume & basic requirements | 
| `2` | **Reached Out** | Sky Blue | Initial recruiter sourcing outreach | 
| `3` | **Screen** | Teal | 30-minute initial phone/video screen | 
| `4` | **Assessment Sent** | Amber | Take-home assignment / technical challenge | 
| `5` | **HM Review** | Yellow | Hiring Manager portfolio/resume evaluation | 
| `6` | **1st Round** | Indigo | Technical or functional deep dive | 
| `7` | **2nd Round** | Violet | Cross-functional or team panel interview | 
| `8` | **Final Round** | Rose | Executive / Culture leadership chat | 
| `9` | **Offer** | Emerald | Offer package extended & negotiation | 
| `10` | **Background** | Forest Green | Reference checks, background verification | 
| `11` | **Rejected** | Crimson | Polite rejection dispatch & talent pool archive | 

* **One-Click Progression:** Click **Advance Stage** (or press `A`) to step the candidate forward by one stage.

* **One-Click Rejection:** Click **Reject** (or press `R`) to mark the candidate as *Rejected* and remove them from active pipeline totals while preserving their history.

### 2. Multi-Criteria Scorecards & Evaluation

Instead of a generic single rating, candidate profiles include broken-out evaluation across 4 operational dimensions:

* **Technical Skill:** Domain execution, code quality, technical depth.

* **Communication:** Clarity, responsiveness, structure, empathy.

* **Domain Knowledge:** Market familiarity, architectural context, tooling familiarity.

* **Culture & Values:** Collaboration, cross-functional alignment, mission fit.

The application calculates an unweighted composite score displayed prominently as `★ X.X / 5.0` on both the detail view and summary list cards.

### 3. Pipeline Velocity & Stalled Detection

Every movement between stages writes an immutable entry into the candidate's audit log with a micro-timestamp.

* **Stalled Alerts (`>7 Days`):** An amber badge (`⏱ 8d in stage`) appears on cards when a candidate remains in the same stage for more than seven calendar days.

* **Critical Alerts (`>10 Days`):** Turns crimson (`⚠️ 12d stalled`) to prevent high-priority candidates from going cold.

* **Funnel Conversion Velocity:** The top analytics drawer visualizes step-by-step conversion ratios (e.g., `Screen [60%] → 1st Round [45%] → Offer`).

### 4. Templated Email System

Send candidate communications via the built-in email modal (press `E` or click **Email**):

* **Interview Invitation:** Pre-formatted request for availability and logistics.

* **Formal Offer Letter:** Professional welcome note highlighting compensation terms.

* **Respectful Rejection:** Warm, constructive dispatch for unsuccessful candidates.

* **Take-Home Assessment:** Challenge brief delivery with turnaround guidelines.

Click **Open in Mail Client** to launch your native desktop client (Apple Mail, Outlook, Thunderbird) via an encoded `mailto:` link, or click **Copy Text** to paste into Gmail or webmail.

### 5. Resumes, Links & Notes

* **Resume Uploads:** Upload standard PDF resumes directly into the browser. Files are stored locally as Base64 data within candidate objects.

* **One-Click Portfolio Links:** Dedicated fields for LinkedIn and GitHub/Portfolio with one-click external launch buttons (`↗`).

* **Markdown Notes:** Toggle between an interactive editing textarea and a formatted preview pane supporting bold text (`**bold**`), italics (`*italic*`), and itemized bullet lists (`- item`).

### 6. Keyboard Navigation

KineticHire is optimized for keyboard-only pipeline audits:

| Key | Action | Scope | 
| ----- | ----- | ----- | 
| J or ↓ | Move selection to next candidate | Global | 
| K or ↑ | Move selection to previous candidate | Global | 
| A | Advance candidate to next pipeline stage | Profile Selected | 
| R | Reject current candidate | Profile Selected | 
| E | Open templated communication modal | Profile Selected | 
| N | Open blank candidate intake form | Global | 
| / | Instantly focus search bar | Global | 
| Esc | Close active modal, deselect profile, or unfocus input | Global | 

## Technical Architecture

```
KineticHire Architecture (Client-Side Only)
┌───────────────────────────────────────────────────────────┐
│                     Browser Window                        │
│                                                           │
│  ┌────────────────────┐   ┌────────────────────────────┐  │
│  │   UI View Layer    │   │      Web Audio API         │  │
│  │ HTML5 / CSS Grid   │   │ Synthesizer (Zero Assets)  │  │
│  └─────────▲──────────┘   └──────────────▲─────────────┘  │
│            │                             │                │
│  ┌─────────┴─────────────────────────────┴─────────────┐  │
│  │           Vanilla ES6+ Reactive State Engine        │  │
│  │                                                     │  │
│  │  - candidates[] State Array                         │  │
│  │  - Search / Stage Filter Invalidation               │  │
│  │  - Stage Transition Audit Log Engine                │  │
│  │  - Draggable Splitter Physics                       │  │
│  └─────────┬─────────────────────────────┬─────────────┘  │
│            │                             │                │
│  ┌─────────▼──────────┐   ┌──────────────▼─────────────┐  │
│  │    localStorage    │   │      File API Engine       │  │
│  │ Auto-Persist (JSON)│   │  - JSON Backup / Restore   │  │
│  │                    │   │  - CSV Report Generation   │  │
│  └────────────────────┘   └────────────────────────────┘  │
└───────────────────────────────────────────────────────────┘

```

### Client-Side State Engine

Application state is held in a centralized JavaScript array `candidates`. State mutations follow a predictable flow:

1. User triggers an action (stage advance, form submission, rating change).

2. The mutation logic updates the corresponding object inside `candidates`.

3. An audit log item is generated: `{ timestamp: "YYYY-MM-DD HH:MM", text: "..." }`.

4. `triggerSaveState()` pushes a snapshot of the array to browser `localStorage`.

5. The view layer updates: `renderList()` repopulates visible DOM nodes, `updateAnalytics()` updates Chart.js metrics, and visual counters recalculate.

### Web Audio API Sound Synthesis

KineticHire generates sound programmatically through the browser's native `AudioContext`, requiring no `.mp3` or `.wav` network requests:

* **Selection Tone (`'select'`):** A brief sine-wave frequency sweep (`480Hz → 320Hz`) over 40 milliseconds with exponential gain decay.

* **Success Chime (`'save'` / `'advance'`):** A dual-oscillator musical sequence playing a C5 (`523.25Hz`) triangle wave resolving into an E5 (`659.25Hz`) over 150 milliseconds.

* **Destructive Cue (`'delete'` / `'reject'`):** A low sawtooth drop (`260Hz → 90Hz`) across 180 milliseconds signaling rejection or profile deletion.

### DOM Draggable Splitter Implementation

The divider between the left candidate list and right candidate profile uses standard DOM mouse events without third-party layout libraries:

```
divider.addEventListener('mousedown', () => { isDragging = true; });
document.addEventListener('mousemove', (e) => {
  if (!isDragging) return;
  const newWidth = e.clientX;
  if (newWidth >= 280 && newWidth <= 650) {
    listPane.style.width = `${newWidth}px`;
  }
});
document.addEventListener('mouseup', () => { isDragging = false; });

```

### Chart.js Lifecycle & Color Management

Pipeline distribution is rendered using Chart.js delivered via public CDN. When toggling dark mode:

* CSS custom variables (e.g., `--bg-surface`, `--border-subtle`) shift automatically via `document.body.classList.toggle('dark-mode')`.

* `updateChartTheme(isDark)` directly updates Chart.js instance properties:

```
chartInstance.options.scales.x.ticks.color = textColor;
chartInstance.options.scales.y.ticks.color = textColor;
chartInstance.options.scales.y.grid.color = gridColor;
chartInstance.update();

```

### Data Schemas (JSON & CSV)

#### JSON State Structure

```
[
  {
    "id": "cand-1718000000000",
    "name": "Jane Doe",
    "role": "Lead Systems Architect",
    "email": "jane.doe@example.com",
    "phone": "+1 555-0199",
    "linkedin": "https://linkedin.com/in/janedoe",
    "portfolio": "https://github.com/janedoe",
    "resumeName": "jane_doe_cv.pdf",
    "resumeData": "data:application/pdf;base64,...",
    "stage": "screen",
    "scorecard": { "tech": 5, "comm": 4, "domain": 5, "culture": 4 },
    "tags": ["Distributed Systems", "Go", "AWS"],
    "dateAdded": "2026-10-15",
    "notes": "**Initial Screen Notes**\n- 8+ years designing high-throughput backends",
    "history": [
      { "timestamp": "2026-10-15 09:30", "text": "Added to New Applicant" },
      { "timestamp": "2026-10-18 11:15", "text": "Moved to Screen" }
    ]
  }
]

```

#### CSV Export Format

```
"ID","Name","Role","Email","Phone","Stage","Days in Stage","Composite Rating","Tags","LinkedIn","Portfolio","Notes"
"cand-1","Alexandra Chen","Staff Infrastructure Engineer","a.chen@example.com","+1 415-555-0192","Screen","7","4.8","Kubernetes, AWS, Terraform, Staff","https://linkedin.com","https://github.com","**Strong Kubernetes performance**"

```

## Data Security & Compliance

KineticHire is designed with a **zero-trust, offline-first data model**:

* **No Remote Databases:** Candidate information is never sent to third-party cloud backends.

* **Strict Network Isolation:** Outside of pulling the initial Chart.js bundle from CDN on page load, the application makes no external API requests, telemetries, or pingbacks.

* **GDPR / CCPA Friendly:** Because candidate data resides in the local browser context, data deletion is immediate and comprehensive when using the **Delete Candidate** control or clearing browser storage.

## Quick Start & Deployment

### Option A: Local Double-Click (No Install Required)

1. Clone or download the repository:

   ```
   git clone https://github.com/your-username/kinetichire.git
   cd kinetichire
   
   ```

2. Double-click `index.html` to open it in Chrome, Safari, Edge, or Firefox.

### Option B: GitHub Pages (1-Minute Setup)

Host your own private or public instance on GitHub Pages:

1. Fork this repository to your personal or organization account.

2. In your repository settings, navigate to **Settings** > **Pages**.

3. Under **Build and deployment** > **Source**, choose **Deploy from a branch**.

4. Select `main` (or `master`) branch and set folder to `/ (root)`.

5. Click **Save**. Within 60 seconds, your ATS will be live at:

   ```
   https://<your-username>.github.io/kinetichire/
   
   ```

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss proposed updates:

1. Fork the Project

2. Create your Feature Branch (`git checkout -b feature/NewFeature`)

3. Commit your Changes (`git commit -m 'Add NewFeature'`)

4. Push to the Branch (`git push origin feature/NewFeature`)

5. Open a Pull Request

## License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for more information.

```
MIT License

Copyright (c) 2026 KineticHire Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


[kinetichire_readme.md](https://github.com/user-attachments/files/34332107/kinetichire_readme.md)

```
