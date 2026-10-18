# Sprout 2.0 — Design Rationale

> Practice design exercise. Fictional company, fictional data. This document defends
> the decisions in the prototype the way a designer would in a review: with the
> funnel data, the constraints, and the trade-offs — not with adjectives.

## 1. Funnel diagnosis — which screen attacks which metric

The funnel is brutal: **40,000 downloads → 9,000 KYC completions → 3,000 first investments.**
Two leaks, two different causes, two different fixes.

| Leak | Data | Diagnosis | Screen that attacks it |
|---|---|---|---|
| Download → KYC (77% lost) | 62% drop-off *at* the KYC step; #1 support complaint is "didn't know I needed PAN and Aadhaar ready" | The failure happens **before** KYC starts. Users arrive unprepared, hit a document request mid-flow, and leave. | **Onboarding 3** — a readiness checklist *before* KYC, with the CTA disabled until both boxes are ticked. Preparation is a commitment device. |
| KYC → first investment (67% lost) | Complaint #2: "the app asked me to wait and I thought it crashed"; 65% of users say "investing feels scary" | Two compounded fears: *is this thing working?* and *is this thing safe for me?* Silent spinners answer neither. | **Onboarding 4** — named verification steps with time expectations ("about 20 seconds") and normalisers ("nothing is stuck"). **Onboarding 5** — ₹100 default, risk disclaimer above the fold, honest copy. |
| Homepage bounce 78% | Measured over 2 weeks *including a paid ad campaign* — i.e. expensive traffic bouncing | The homepage is where paid users decide in seconds whether this is for them. Clutter (pop-ups, banners, video) raises cognitive load at the exact moment trust is lowest. | **Both homepages** — one story, one CTA, zero pop-ups. The bounce problem is treated as a *trust + clarity* problem, not a *more stimuli* problem. |

The through-line: every screen reduces **one specific fear** — fear of the unknown (screen 2 previews all steps), fear of being unprepared (screen 3), fear of a broken app (screen 4), fear of losing money (screen 5's disclaimer + ₹100 default).

## 2. Stakeholder conflicts — demand → decision → why

| Stakeholder demand | What I did | Why (with data) |
|---|---|---|
| **CEO:** Apple-like minimal, premium. *No cartoon characters.* | Beej is a geometric seedling: two leaf shapes, one rounded body, dot eyes, a 3px smile. No limbs, no speech-bubble gags, no bounce physics. It also *is* the logo mark. | A mascot and "premium minimal" aren't opposites — *goofy* and premium are. Geometric abstraction keeps the Apple-like restraint while giving marketing its character. The CEO's real fear ("investors won't trust a cartoon") is addressed by never letting Beej *joke*; it only guides and reassures. |
| **Marketing:** mascot + video + live ticker + 3 pop-ups + referral banner | Mascot: yes (see above). Video: **no**. Ticker: yes, but muted, below the hero, decorative, pauses on hover. Pop-ups: **zero**. Referral banner: yes, but **only on the post-investment success screen**. | The 78% bounce rate is the trump card: pop-ups are the highest-bounce element class in the industry, and stacking three of them onto a page that's already losing 78% of paid traffic is arson. The ticker survives as a *subdued* nod to "live market" energy — 36s CSS loop, no JS, ~0KB. Referral converts best at moments of delight, not moments of suspicion — hence post-first-investment, where it becomes a celebration mechanic instead of a trust tax. Video dies on the dev constraint (weight) and the 6.2s load time. |
| **Compliance:** disclaimer ≥12px, visible without scrolling, on every investment-content page; never "guaranteed / safe / risk-free" | A structural `.disclaimer` component: 12.5px, amber left-border, placed above the fold on both homepages and screens 1, 2, 5 — plus a sticky 12px bottom bar inside every mobile frame. Copy audit: the banned words appear nowhere in the prototype. | Compliance isn't a footer, it's a layout constraint. Making it a *component* means it can't be accidentally dropped from a future screen. "Subject to market risks… possible loss of principal" is stated plainly on the investment screen itself — which, paradoxically, builds the trust the funnel needs. |
| **Lead dev:** <1.5MB page, animations CSS/Lottie <100KB, 1 dev, 3 weeks | Entire prototype (8 pages + CSS) is a fraction of the budget (measured below). All animation is CSS keyframes; the mascot is inline SVG (~2KB per instance); system fonts only, zero webfont downloads, zero JS frameworks, zero images. | The constraint became the aesthetic: no framework, no build step, no images = a one-dev, three-week build is actually feasible. Lottie was never needed — two keyframes (bob, sway) do the job. |
| **Support:** "didn't know I needed PAN/Aadhaar" + "thought it crashed" | Screen 3 (readiness checklist) and screen 4 (named wait states with time expectations) — see funnel table. | Support tickets are user research with the politeness removed. Both complaints describe *the same root cause*: the app never told the user what was happening. The fix is copy and sequencing, not engineering. |

## 3. Mascot rationale — Beej

**Name:** *Beej* (बीज) — "seed" in Hindi. A tiny seed, like a first investment.

**One-line personality:** *A quiet companion that grows with you — it never jokes, never rushes, never sells.*

**Why this resolves the CEO-vs-Marketing war:**
- *For the CEO:* it's geometry, not a cartoon — two leaves, one rounded body, drawn from the same shapes as a logo mark. It appears at small sizes as a guide badge and never performs slapstick. Premium minimalism survives.
- *For Marketing:* it's a *character* with a name, a face, and a story arc — it can absolutely carry an Instagram reel. The 2M-view competitor proved mascots work; Beej proves they don't have to be goofy to work.
- *For the user:* continuity. The same seedling from the homepage greets you in onboarding, shrinks to a badge while you work, and visibly "grows" (larger leaves, bigger smile) on the success screen. The product's story and the mascot's story are the same story: small start, steady growth.

**Why SVG + CSS:** ~2KB per instance, infinitely scalable, animatable with two keyframes, themeable via `currentColor`-style fills, and — critically for the Hindi requirement — never bakes text into an image.

## 4. Accessibility, Hindi-readiness, performance

**Accessibility (WCAG AA):**
- Text/background pairs used (measured): ink `#182420` on paper `#faf7f0` (15.0:1), green-700 `#0e5c40` on paper (7.5:1), white on green-700 (8.0:1), ink-soft `#43544c` on paper (7.5:1). All ≥ 4.5:1. Amber `#b97f1f` is restricted to large graphics and the disclaimer border — never body text.
- Every interactive element is ≥44px (buttons are 52px; checkbox rows are full-width 44px+ tap areas).
- Animations respect `prefers-reduced-motion`; the ticker is `aria-hidden` (decorative); the mascot carries `role="img"` with labels.

**Hindi-readiness (English first, no redesign later):**
- System font stack includes `Noto Sans Devanagari` / `Mangal` fallbacks — Devanagari renders without a webfont download.
- Screen 1 ships a working EN/हिं toggle: strings swap from a dictionary, layout doesn't move. This *proves* the layout absorbs Hindi — Devanagari strings run ~15–30% longer, and the generous line lengths, wrapping headings, and non-fixed-height cards were designed for exactly that.
- No text baked into images (there are no images). Logical-property awareness is noted for a future RTL pass, though Hindi doesn't need it.

**Performance (the 1.5MB budget):**
- Measured total prototype weight: **~50KB of HTML+CSS** (84KB on disk including this rationale doc) — roughly **3% of the 1.5MB budget**. The heaviest asset class (images, webfonts, frameworks, video) was eliminated entirely rather than optimised.
- 91% of users are on mobile and 40% on sub-₹10,000 devices: hence no autoplay, no video, one CSS ticker, `prefers-reduced-motion` support, and tap targets sized for low-end touchscreens.

## 5. What I'd test first after launch

1. **Screen 3's disabled CTA vs. the 62% KYC drop-off.** A/B: checklist-gated KYC vs. today's direct-to-KYC. Primary metric: KYC completion rate. Hypothesis: +15–25% relative lift, because the drop-off was a *preparation* problem.
2. **Wait-state copy vs. support tickets.** Ship screen 4's named waits, then watch the "thought it crashed" ticket category for 2 weeks. It's a support-ticket metric, not a funnel metric — the cheapest experiment on this list.
3. **Zero pop-ups vs. bounce.** Homepage bounce is 78% *with* paid traffic. Hold out: this minimal homepage vs. marketing's preferred version (pop-ups + video + referral banner). If bounce doesn't improve by ≥10 points, the "clarity beats stimuli" thesis is wrong and I'll eat the pop-ups.

## 6. Honest trade-offs / deliberately left out

- **Marketing didn't get everything.** No video, no pop-ups, no homepage referral banner. If the A/B tests prove me wrong, those are the first things back in — the rationale documents *where* I'd concede.
- **The mascot is a bet.** Geometric restraint could read as cold next to a competitor's 2M-view cartoon. Mitigation: Beej earns warmth through *behavior* (it waits with you, celebrates with you), not through cuteness.
- **No returns calculator, no fund comparison.** A first-timer choosing between funds is a *second-session* problem. The funnel says the emergency is KYC → first investment; scope was cut to serve it.
- **The Hindi toggle is a demo, not a translation.** Real localisation needs professional copy review — the prototype only proves the layout survives it.
- **KYC waits are simulated** with timers in the prototype; production needs real API polling with timeout and retry copy ("still working — 30 more seconds?") for the failure cases.
- **Desktop gets less love than mobile** — deliberately. 91% mobile, and the brief's energy is mobile-first. The desktop page is a responsive sibling, not a separate concept.
