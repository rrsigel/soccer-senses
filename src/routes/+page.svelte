<script>
  import { onMount } from 'svelte';

  let activeSection = null;
  let hoveredNode = null;
  let isAnimating = false;

  // Scrollytelling state
  let headerFixed = false;       // title bar is pinned to top
  let currentStep = 0;           // which paragraph step is active (0 = none, 1-3 = paragraphs)
  let hubVisible = false;        // hub has entered view

  const paragraphs = [
    "In a society where connection is increasingly curated by algorithms and mediated by screens, certain spaces still force us into physical proximity — across difference, across distance, across the boundaries we'd otherwise never cross.",
    "What are those spaces? Where does common necessity still bring us together, body to body, sense to sense? And what can we learn from them?",
    "One such space: the youth travel soccer field. Before children age into elite programs divided by the privilege of training and zip code, before participation tapers off, there is the common bond of the town travel team — the one that draws from wherever you happen to live. What sustains this world in a divided society? What quietly threatens it?"
  ];

  const senses = [
    {
      id: 'hearing',
      emoji: '👂',
      label: 'HEARING',
      subtitle: 'Sideline Cheers',
      angle: -90,
      color: '#7a2a08',
      accent: '#e09a40',
      image: 'https://i.ibb.co/fzX4PQyD/Shouting-faces-in-sepia-tones.png',
      popout: 'What do parents actually say from the sideline — and what does it reveal about us?',
      summary: 'What do parents actually say from the sideline — and what does it reveal about us?',
      content: `<p class="tbd">Content coming soon.</p>`
    },
    {
      id: 'taste',
      emoji: '🍊',
      label: 'TASTE',
      subtitle: 'Sliced Oranges',
      angle: -18,
      color: '#7a3d00',
      accent: '#d4a040',
      image: 'https://i.ibb.co/jPLn2fdY/Freshly-sliced-orange-delight.png',
      popout: 'A piece of fruit. A halftime ritual. A social obligation that nobody remembers starting.',
      summary: 'A piece of fruit. A halftime ritual. A social obligation that nobody remembers starting.',
      content: `<p class="tbd">Content coming soon.</p>`
    },
    {
      id: 'sight',
      emoji: '👁️',
      label: 'SIGHT',
      subtitle: 'Team Colors',
      angle: 54,
      color: '#0e2e48',
      accent: '#6ab4d8',
      image: 'https://i.ibb.co/WWdGyvfR/Colorful-soccer-jerseys-neatly-stacked.png',
      popout: 'No mascots — only colors. What does it mean to be the Navy Blue, the Maroon, the Cardinal/Navy?',
      summary: 'No mascots. Only colors. What does it mean to be the Navy Blue, the Maroon, the Cardinal/Navy?',
      content: `<p class="tbd">Content coming soon.</p>`
    },
    {
      id: 'smell',
      emoji: '🌿',
      label: 'SMELL',
      subtitle: 'Cut Grass & Canada Geese',
      angle: 126,
      color: '#122d18',
      accent: '#6ab87e',
      image: 'https://i.ibb.co/5hHSkhjN/Goose-legs-in-freshly-mown-grass.png',
      popout: 'The field doesn\'t maintain itself. Someone schedules the mower. Someone battles the geese.',
      summary: 'The field doesn\'t maintain itself. Someone schedules the mower. Someone battles the geese.',
      content: `<p class="tbd">Content coming soon.</p>`
    },
    {
      id: 'touch',
      emoji: '🤝',
      label: 'TOUCH',
      subtitle: 'Sharing Space',
      angle: 198,
      color: '#240d50',
      accent: '#a068d4',
      image: 'https://i.ibb.co/jkG7NJrL/Standing-apart-with-pride.png',
      popout: 'What happens when the people the algorithm would never show you are standing next to you for 90 minutes every Saturday?',
      summary: 'What happens when the people the algorithm would never show you are standing next to you for 90 minutes every Saturday?',
      content: `<p class="tbd">Content coming soon.</p>`
    }
  ];

  function openSection(id) {
    if (isAnimating) return;
    isAnimating = true;
    activeSection = id;
    hoveredNode = null;
    setTimeout(() => { isAnimating = false; }, 400);
  }

  function closeSection() {
    if (isAnimating) return;
    isAnimating = true;
    activeSection = null;
    setTimeout(() => { isAnimating = false; }, 400);
  }

  function getActiveSense() {
    return senses.find(s => s.id === activeSection);
  }

  function setHover(id) { hoveredNode = id; }
  function clearHover() { hoveredNode = null; }

  onMount(() => {
    // IntersectionObserver for the hero title — when it leaves view, fix the header
    const heroObserver = new IntersectionObserver(
      ([entry]) => { headerFixed = !entry.isIntersecting; },
      { threshold: 0, rootMargin: '-80px 0px 0px 0px' }
    );

    // IntersectionObserver for each scroll step paragraph
    const stepObserver = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            const step = parseInt(entry.target.dataset.step);
            if (step > currentStep) currentStep = step;
          }
        });
      },
      { threshold: 0.5 }
    );

    // IntersectionObserver for the hub section
    const hubObserver = new IntersectionObserver(
      ([entry]) => { if (entry.isIntersecting) hubVisible = true; },
      { threshold: 0.15 }
    );

    const hero = document.getElementById('hero-title');
    const steps = document.querySelectorAll('.scroll-step');
    const hub = document.getElementById('hub-section');

    if (hero) heroObserver.observe(hero);
    steps.forEach(s => stepObserver.observe(s));
    if (hub) hubObserver.observe(hub);

    return () => {
      heroObserver.disconnect();
      stepObserver.disconnect();
      hubObserver.disconnect();
    };
  });
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=DM+Mono:wght@300;400&family=DM+Sans:wght@300;400;500&display=swap');

  :global(*, *::before, *::after) { box-sizing: border-box; }

  :global(body) {
    margin: 0;
    background: #f2ead8;
    color: #2a2318;
    font-family: 'DM Sans', sans-serif;
  }

  /* ── PAPER BACKGROUND ── */
  .paper-base {
    position: fixed;
    inset: 0;
    z-index: 0;
    background: #f2ead8;
  }

  .paper-grain {
    position: fixed;
    inset: 0;
    z-index: 1;
    pointer-events: none;
    opacity: 0.55;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='300' height='300'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3CfeColorMatrix type='saturate' values='0'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.4'/%3E%3C/svg%3E");
    background-size: 220px 220px;
  }

  .paper-lines {
    position: fixed;
    inset: 0;
    z-index: 2;
    pointer-events: none;
    background: repeating-linear-gradient(
      180deg,
      transparent 0px, transparent 28px,
      rgba(180,155,100,0.07) 28px, rgba(180,155,100,0.07) 29px
    );
  }

  .paper-vignette {
    position: fixed;
    inset: 0;
    z-index: 3;
    pointer-events: none;
    background: radial-gradient(ellipse at center, transparent 55%, rgba(160,130,80,0.18) 100%);
  }

  /* ── MAIN WRAPPER ── */
  .page {
    position: relative;
    z-index: 10;
  }

  /* ── HERO TITLE SECTION ── */
  /* Tall opening screen — title centered vertically */
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 4rem 2rem 6rem;
  }

  .meta-line {
    font-family: 'DM Mono', monospace;
    font-size: 0.6rem;
    letter-spacing: 0.22em;
    color: rgba(100,80,45,0.55);
    text-transform: uppercase;
    margin-bottom: 0.2rem;
  }

  .meta-sub {
    font-family: 'DM Mono', monospace;
    font-size: 0.6rem;
    letter-spacing: 0.22em;
    color: rgba(100,80,45,0.55);
    text-transform: uppercase;
    margin-bottom: 1.1rem;
  }

  .hero h1 {
    font-family: 'DM Sans', sans-serif;
    font-size: clamp(1.6rem, 4vw, 2.6rem);
    font-weight: 500;
    line-height: 1.1;
    margin: 0 0 0.2rem;
    color: #2a1a0e;
    letter-spacing: 0.04em;
    text-transform: uppercase;
  }

  .title-sub {
    font-family: 'DM Sans', sans-serif;
    font-size: clamp(1.2rem, 3vw, 2rem);
    font-weight: 300;
    color: #2a1a0e;
    letter-spacing: 0.01em;
  }

  /* Scroll prompt at bottom of hero */
  .scroll-cue {
    position: absolute;
    bottom: 2.5rem;
    left: 50%;
    transform: translateX(-50%);
    font-family: 'DM Mono', monospace;
    font-size: 0.52rem;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(100,80,45,0.38);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.4rem;
    animation: nudge 2s ease-in-out infinite;
  }

  .scroll-cue::after {
    content: '↓';
    font-size: 0.9rem;
    opacity: 0.4;
  }

  @keyframes nudge {
    0%, 100% { transform: translateX(-50%) translateY(0); }
    50%       { transform: translateX(-50%) translateY(5px); }
  }

  /* ── FIXED HEADER BAR (appears after scroll) ── */
  .fixed-header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 90;
    background: rgba(242, 234, 216, 0.95);
    border-bottom: 1px solid rgba(120,95,55,0.18);
    padding: 0.6rem 2rem;
    display: flex;
    align-items: baseline;
    gap: 1rem;
    backdrop-filter: blur(6px);
    transition: transform 0.35s cubic-bezier(0.16, 1, 0.3, 1),
                opacity 0.35s ease;
  }

  .fixed-header.hidden {
    transform: translateY(-100%);
    opacity: 0;
    pointer-events: none;
  }

  .fixed-header.visible {
    transform: translateY(0);
    opacity: 1;
  }

  .fixed-title {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.78rem;
    font-weight: 500;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: #2a1a0e;
  }

  .fixed-divider {
    font-family: 'DM Mono', monospace;
    font-size: 0.6rem;
    color: rgba(120,95,55,0.35);
  }

  .fixed-sub {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.72rem;
    font-weight: 300;
    color: rgba(60,42,18,0.55);
  }

  .fixed-meta {
    margin-left: auto;
    font-family: 'DM Mono', monospace;
    font-size: 0.52rem;
    letter-spacing: 0.16em;
    text-transform: uppercase;
    color: rgba(100,80,45,0.4);
  }

  /* ── SCROLL STEPS (paragraphs) ── */
  .scroll-steps {
    padding: 0 2rem;
  }

  /* Each step fills most of the viewport height */
  .scroll-step {
    min-height: 80vh;
    display: flex;
    align-items: center;
    justify-content: center;
    max-width: 640px;
    margin: 0 auto;
    padding: 4rem 0;
    opacity: 0;
    transform: translateY(28px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }

  .scroll-step.active {
    opacity: 1;
    transform: translateY(0);
  }

  .step-text {
    font-family: 'DM Sans', sans-serif;
    font-size: clamp(1rem, 2.2vw, 1.25rem);
    font-weight: 300;
    line-height: 1.8;
    color: rgba(42,26,14,0.82);
    text-align: center;
  }

  /* ── HUB SECTION ── */
  .hub-section {
    padding: 2rem 1.5rem 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    opacity: 0;
    transform: translateY(32px);
    transition: opacity 0.8s ease, transform 0.8s ease;
  }

  .hub-section.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .hub-instruction {
    font-family: 'DM Mono', monospace;
    font-size: 0.56rem;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(100,80,45,0.4);
    margin-bottom: 1.5rem;
  }

  .ball-container {
    position: relative;
    width: 360px;
    height: 360px;
  }

  .ball-svg-wrap {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 118px;
    height: 118px;
    z-index: 10;
    filter: drop-shadow(0 4px 14px rgba(0,0,0,0.25));
  }

  .ball-svg-wrap img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }

  .spoke {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 112px;
    height: 4px;
    transform-origin: 0 50%;
    background: linear-gradient(to right, rgba(120,95,55,0.55) 0%, transparent 100%);
    z-index: 1;
    border-radius: 2px;
  }

  /* ── SENSE NODES ── */
  .sense-node {
    position: absolute;
    width: 90px;
    height: 90px;
    border-radius: 50%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: transform 0.22s cubic-bezier(0.34, 1.56, 0.64, 1), box-shadow 0.2s ease;
    z-index: 5;
    text-align: center;
    overflow: visible;
    border: 2px solid rgba(120,95,55,0.25);
  }

  .sense-node:hover {
    transform: translate(-50%, -50%) scale(1.1) !important;
    z-index: 50;
    box-shadow: 0 6px 24px rgba(0,0,0,0.22);
  }

  .node-circle {
    position: absolute;
    inset: 0;
    border-radius: 50%;
    overflow: hidden;
  }

  .node-overlay {
    position: absolute;
    inset: 0;
    background: rgba(0,0,0,0);
    transition: background 0.2s ease;
  }

  .sense-node:hover .node-overlay {
    background: rgba(0,0,0,0.36);
  }

  .node-content {
    position: relative;
    z-index: 3;
  }

  .sense-label {
    font-family: 'DM Mono', monospace;
    font-size: 0.58rem;
    font-weight: 400;
    letter-spacing: 0.16em;
    text-transform: uppercase;
    line-height: 1;
    color: #ffffff;
    text-shadow: 0 1px 6px rgba(0,0,0,0.9), 0 0 12px rgba(0,0,0,0.6);
  }

  .hover-subtitle {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    white-space: nowrap;
    background: rgba(242, 234, 216, 0.97);
    border: 1px solid rgba(120,95,55,0.28);
    border-radius: 6px;
    padding: 0.4rem 0.7rem;
    z-index: 200;
    pointer-events: none;
    box-shadow: 0 4px 16px rgba(0,0,0,0.12);
    animation: chipIn 0.15s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  @keyframes chipIn {
    from { opacity: 0; transform: translateY(-50%) scale(0.92); }
    to   { opacity: 1; transform: translateY(-50%) scale(1); }
  }

  .hover-subtitle-text {
    font-family: 'DM Mono', monospace;
    font-size: 0.54rem;
    font-weight: 400;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: #2a1a0e;
    line-height: 1;
  }

  /* ── BELOW-HUB ATTRIBUTION + FOOTER ── */
  .below-hub {
    width: 100%;
    max-width: 680px;
    text-align: center;
    margin: 1.5rem auto 0;
    padding: 0 1.5rem;
    opacity: 0;
    transition: opacity 0.8s ease 0.3s;
  }

  .hub-section.visible ~ .below-hub,
  .below-hub.visible {
    opacity: 1;
  }

  .attribution {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.78rem;
    font-weight: 300;
    font-style: italic;
    color: rgba(80,58,30,0.55);
    line-height: 1.6;
  }

  footer {
    width: 100%;
    max-width: 680px;
    margin: 1.5rem auto 0;
    padding: 1.5rem 1.5rem 4rem;
    border-top: 1px solid rgba(120,95,55,0.15);
  }

  .footer-text {
    font-family: 'DM Mono', monospace;
    font-size: 0.56rem;
    letter-spacing: 0.1em;
    color: rgba(100,80,40,0.35);
    line-height: 2;
  }

  /* ── CONTENT PANEL ── */
  .panel-overlay {
    position: fixed;
    inset: 0;
    background: rgba(40,30,15,0.6);
    z-index: 100;
    display: flex;
    align-items: flex-end;
    justify-content: center;
    animation: fadeIn 0.22s ease;
  }

  @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

  .panel {
    background: #f5eedc;
    width: 100%;
    max-width: 820px;
    max-height: 84vh;
    border-radius: 16px 16px 0 0;
    overflow-y: auto;
    padding: 2.5rem 2.5rem 4rem;
    animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    position: relative;
    border-top: 2px solid rgba(120,95,55,0.25);
  }

  @keyframes slideUp {
    from { transform: translateY(40px); opacity: 0; }
    to   { transform: translateY(0);    opacity: 1; }
  }

  .panel-close {
    position: sticky;
    top: 0;
    float: right;
    background: transparent;
    border: 1px solid rgba(120,95,55,0.3);
    color: rgba(100,80,40,0.6);
    font-family: 'DM Sans', sans-serif;
    font-size: 0.75rem;
    letter-spacing: 0.06em;
    padding: 0.4rem 0.9rem;
    cursor: pointer;
    border-radius: 4px;
    margin-bottom: 0.5rem;
    transition: all 0.18s;
    z-index: 10;
  }

  .panel-close:hover {
    color: #2a2318;
    border-color: rgba(120,95,55,0.6);
    background: rgba(120,95,55,0.06);
  }

  .panel-header { margin-bottom: 2rem; }

  .panel-sense {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    font-family: 'DM Sans', sans-serif;
    font-size: 0.7rem;
    font-weight: 500;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    margin-bottom: 0.5rem;
  }

  .panel-thumb {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    background-size: cover;
    background-position: center;
    border: 1px solid rgba(120,95,55,0.2);
    flex-shrink: 0;
  }

  .panel-title {
    font-family: 'DM Sans', sans-serif;
    font-size: clamp(1.4rem, 3.5vw, 2rem);
    font-weight: 500;
    margin: 0 0 0.5rem;
    line-height: 1.15;
    color: #2a2318;
    letter-spacing: -0.01em;
  }

  .panel-summary {
    font-family: 'DM Sans', sans-serif;
    font-style: italic;
    font-size: 0.92rem;
    line-height: 1.7;
    color: rgba(80,60,30,0.65);
    font-weight: 300;
  }

  .panel-body :global(h3) {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.95rem;
    font-weight: 500;
    letter-spacing: 0.04em;
    text-transform: uppercase;
    margin: 2rem 0 0.5rem;
    color: #2a2318;
    border-left: 2px solid var(--accent);
    padding-left: 0.75rem;
  }

  .panel-body :global(p) {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.9rem;
    line-height: 1.85;
    color: rgba(50,38,20,0.78);
    font-weight: 300;
    margin: 0 0 1rem;
  }

  .panel-body :global(em) { color: var(--accent); font-style: italic; }

  .panel-body :global(.sources) {
    margin-top: 2rem;
    padding: 0.9rem 1rem;
    border: 1px solid rgba(120,95,55,0.18);
    border-left: 3px solid var(--accent);
    border-radius: 4px;
    font-size: 0.79rem !important;
    color: rgba(90,70,35,0.65) !important;
    line-height: 1.65 !important;
    background: rgba(120,95,55,0.04);
    font-family: 'DM Sans', sans-serif !important;
    font-weight: 300;
  }

  .panel-body :global(.sources strong) {
    color: rgba(90,70,35,0.7);
    display: block;
    margin-bottom: 0.2rem;
    font-weight: 500;
  }

  .panel-body :global(.tbd) {
    font-family: 'DM Sans', sans-serif;
    font-size: 0.85rem;
    font-weight: 300;
    color: rgba(100,80,40,0.4);
    letter-spacing: 0.04em;
    margin-top: 1rem;
  }

  @media (max-width: 480px) {
    .ball-container { width: 300px; height: 300px; }
    .panel { padding: 1.5rem 1.2rem 3rem; }
    .step-text { font-size: 1rem; }
  }
</style>

<!-- Paper texture -->
<div class="paper-base"></div>
<div class="paper-grain"></div>
<div class="paper-lines"></div>
<div class="paper-vignette"></div>

<!-- Fixed header bar — slides in after hero scrolls away -->
<div class="fixed-header {headerFixed ? 'visible' : 'hidden'}">
  <span class="fixed-title">Systems for the Senses</span>
  <span class="fixed-divider">·</span>
  <span class="fixed-sub">The Youth Soccer Field</span>
  <span class="fixed-meta">RR Sigel — 2026</span>
</div>

<div class="page">

  <!-- ── HERO: full-screen title ── -->
  <section class="hero" id="hero-title">
    <div class="meta-line">RR Sigel — 2026</div>
    <div class="meta-sub">Breaking the Algorithm in 2026</div>
    <h1>Systems for the Senses</h1>
    <div class="title-sub">The Youth Soccer Field</div>
    <div class="scroll-cue">scroll</div>
  </section>

  <!-- ── SCROLL STEPS: three paragraphs ── -->
  <div class="scroll-steps">
    {#each paragraphs as para, i}
      <div
        class="scroll-step {currentStep > i ? 'active' : ''}"
        data-step={i + 1}
      >
        <p class="step-text">{para}</p>
      </div>
    {/each}
  </div>

  <!-- ── HUB SECTION ── -->
  <section
    class="hub-section {hubVisible ? 'visible' : ''}"
    id="hub-section"
  >
    <div class="hub-instruction">↓ hover to preview · click to explore</div>

    <div class="ball-container">

      {#each senses as sense}
        <div class="spoke" style="transform: rotate({sense.angle}deg) translateY(-50%);"></div>
      {/each}

      <div class="ball-svg-wrap">
        <img
          src="https://i.ibb.co/ksy2CKwT/Firefly-Gemini-Flash-Please-generate-a-drawing-of-a-soccer-ball-that-looks-like-it-was-drawn-in-bl.png"
          alt="soccer ball"
        />
      </div>

      {#each senses as sense}
        {@const rad = (sense.angle * Math.PI) / 180}
        {@const cx = 180 + Math.cos(rad) * 136}
        {@const cy = 180 + Math.sin(rad) * 136}
        {@const onLeft = cx < 180}

        <div
          class="sense-node"
          style="left: {cx}px; top: {cy}px; transform: translate(-50%, -50%);"
          on:mouseenter={() => setHover(sense.id)}
          on:mouseleave={clearHover}
          on:click={() => openSection(sense.id)}
          role="button"
          tabindex="0"
          on:keypress={(e) => e.key === 'Enter' && openSection(sense.id)}
          aria-label="Explore {sense.label}: {sense.subtitle}"
        >
          <div
            class="node-circle"
            style="background: {sense.image
              ? `url('${sense.image}') center/cover`
              : sense.color + 'cc'};"
          >
            <div class="node-overlay"></div>
          </div>

          <div class="node-content">
            <div class="sense-label">{sense.label}</div>
          </div>

          {#if hoveredNode === sense.id}
            <div
              class="hover-subtitle"
              style="{onLeft ? 'left: calc(100% + 10px);' : 'right: calc(100% + 10px);'}"
            >
              <div class="hover-subtitle-text">{sense.subtitle}</div>
            </div>
          {/if}
        </div>
      {/each}

    </div>
  </section>

  <!-- Attribution + footer -->
  <div class="below-hub {hubVisible ? 'visible' : ''}">
    <p class="attribution">
      This prototype was created using AI assistance to generate code and images with directed prompts.
    </p>
  </div>

  <footer>
    <div class="footer-text">
      A multimedia interactive project by RR Sigel &nbsp;·&nbsp; 2026<br>
      Prototype outline for editorial development.<br>
      Research frameworks and source suggestions are indicated within each thread.
    </div>
  </footer>

</div>

<!-- Slide-up content panel -->
{#if activeSection}
  {@const sense = getActiveSense()}
  <div
    class="panel-overlay"
    on:click|self={closeSection}
    role="dialog"
    aria-modal="true"
  >
    <div class="panel" style="--accent: {sense.accent};">
      <button class="panel-close" on:click={closeSection}>✕ close</button>
      <div class="panel-header">
        <div class="panel-sense" style="color: {sense.accent}">
          <div class="panel-thumb" style="background-image: url('{sense.image}');"></div>
          {sense.label}
        </div>
        <h2 class="panel-title">{sense.subtitle}</h2>
        <p class="panel-summary">{sense.summary}</p>
      </div>
      <div class="panel-body">
        {@html sense.content}
      </div>
    </div>
  </div>
{/if}
