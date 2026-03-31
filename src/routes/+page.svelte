<script>
  import { onMount } from 'svelte';
 
  let dark = $state(false);
 
  function toggleDark() {
    dark = !dark;
    document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light');
    localStorage.setItem('theme', dark ? 'dark' : 'light');
  }
 
  const projects = [
    { num: '01', title: 'Star Wars Archive', sub: 'Interaktivní 3D archiv',
      desc: 'Clone Wars vesmír v prohlížeči. Galaktická mapa, velitelé, laboratoř světelného meče — vše animované a interaktivní.',
      tech: ['Next.js', 'React', 'Canvas API'], color: '#0071e3',
      url: 'https://clone-wars-psi.vercel.app/star-wars',
    },
    { num: '02', title: 'F1 2026 Tracker', sub: 'Živá data & 3D vozy',
      desc: 'Přehled sezóny 2026 v reálném čase. Three.js 3D modely vozů, tabulky jezdců, týmů a kalendář závodů.',
      tech: ['Next.js', 'Three.js', 'GSAP'], color: '#ff3b30',
      url: 'https://clone-wars-psi.vercel.app/f1',
    },
    { num: '03', title: 'CV Builder', sub: 'Interaktivní tvůrce CV',
      desc: '3 šablony, 6 barev, live preview a export do PDF. Vytvoř profesionální CV za pár minut.',
      tech: ['SvelteKit', 'Svelte 5', 'CSS'], color: '#34c759',
      url: '/cv',
    },
    { num: '04', title: 'Projekt Hub', sub: 'Centrální rozcestník',
      desc: 'Rozcestník pro všechny projekty s animovaným hvězdným pozadím a interaktivními kartami.',
      tech: ['Next.js', 'React', 'Canvas API'], color: '#5e5ce6',
      url: 'https://clone-wars-psi.vercel.app/',
    },
  ];
 
  const skills = [
    { name: 'HTML & CSS',       pct: 92 },
    { name: 'JavaScript',       pct: 82 },
    { name: 'React / Next.js',  pct: 80 },
    { name: 'SvelteKit',        pct: 75 },
    { name: 'Three.js / WebGL', pct: 70 },
    { name: 'GSAP',             pct: 72 },
    { name: 'Správa sítí',      pct: 85 },
    { name: 'Linux / Windows',  pct: 80 },
  ];
 
  let navScrolled = $state(false);
  let formName = $state(''), formEmail = $state(''), formMsg = $state(''), formStatus = $state('');
 
  async function handleSubmit(e) {
    e.preventDefault();
    formStatus = 'sending';
    try {
      const res = await fetch('https://formspree.io/f/mbdzwebo', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', Accept: 'application/json' },
        body: JSON.stringify({ name: formName, email: formEmail, message: formMsg }),
      });
      formStatus = res.ok ? 'success' : 'error';
    } catch { formStatus = 'error'; }
  }
 
  onMount(async () => {
    const gsap = (await import('gsap')).default;
    const ScrollTrigger = (await import('gsap/ScrollTrigger')).default;
    gsap.registerPlugin(ScrollTrigger);
 
    const saved = localStorage.getItem('theme');
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    dark = saved ? saved === 'dark' : prefersDark;
    document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light');
 
    const onScroll = () => { navScrolled = window.scrollY > 40; };
    window.addEventListener('scroll', onScroll);
 
    gsap.timeline({ delay: 0.2 })
      .from('.hero-eyebrow', { y: 20, opacity: 0, duration: .8, ease: 'power3.out' })
      .from('.hero-line span', { y: '100%', opacity: 0, duration: 1, stagger: .1, ease: 'power4.out' }, '-=.4')
      .from('.hero-sub',     { y: 24, opacity: 0, duration: .8, ease: 'power3.out' }, '-=.5')
      .from('.hero-btns a',  { y: 16, opacity: 0, duration: .6, stagger: .1, ease: 'power3.out' }, '-=.4')
      .from('.hero-scroll',  { opacity: 0, duration: 1 }, '-=.2')
      .from('.hm-item',      { y: 10, opacity: 0, duration: .5, stagger: .08 }, '-=.6');
 
    gsap.to('.scroll-prog', {
      scaleX: 1, ease: 'none',
      scrollTrigger: { scrub: .3, start: 'top top', end: 'bottom bottom' },
    });
 
    gsap.to('.hero-bg-word', {
      y: 200, opacity: 0,
      scrollTrigger: { scrub: 2, start: 'top top', end: 'bottom top' },
    });
 
    gsap.utils.toArray('.ap-reveal').forEach(el => {
      gsap.from(el, {
        scrollTrigger: { trigger: el, start: 'top 88%', toggleActions: 'play reverse play reverse' },
        y: 48, opacity: 0, duration: 1, ease: 'power3.out',
      });
    });
 
    gsap.utils.toArray('.ap-stagger').forEach(parent => {
      gsap.from(parent.querySelectorAll('.ap-si'), {
        scrollTrigger: { trigger: parent, start: 'top 85%', toggleActions: 'play reverse play reverse' },
        y: 40, opacity: 0, duration: .8, stagger: .1, ease: 'power3.out',
      });
    });
 
    gsap.utils.toArray('.skill-fill').forEach(el => {
      gsap.fromTo(el, { width: '0%' }, {
        width: el.dataset.w,
        scrollTrigger: { trigger: el, start: 'top 92%', toggleActions: 'play reverse play reverse' },
        duration: 1.4, ease: 'power3.out',
      });
    });
 
    gsap.utils.toArray('.proj-visual').forEach(el => {
      gsap.to(el, {
        y: -40,
        scrollTrigger: { trigger: el.closest('.proj-row'), scrub: 1.5, start: 'top bottom', end: 'bottom top' },
      });
    });
 
    gsap.utils.toArray('.split-reveal').forEach(el => {
      gsap.from(el, {
        scrollTrigger: { trigger: el, start: 'top 80%', toggleActions: 'play reverse play reverse' },
        y: '100%', opacity: 0, duration: 1.1, ease: 'power4.out',
      });
    });
 
    gsap.to('.mq-track', { x: '-50%', duration: 28, repeat: -1, ease: 'none' });
 
    return () => {
      window.removeEventListener('scroll', onScroll);
      ScrollTrigger.getAll().forEach(t => t.kill());
    };
  });
</script>
 




<div class="scroll-prog"></div>

<!-- ══ NAV ══ -->
<nav class:scrolled={navScrolled}>
  <div class="nav-inner">
    <a href="/" class="nav-logo">VU</a>
    <div class="nav-links">
      <a href="#work">Projekty</a>
      <a href="#about">O mně</a>
      <a href="#skills">Dovednosti</a>
      <a href="#contact">Kontakt</a>
    </div>
    <div class="nav-actions">
      <a href="/cv" class="nav-cv">CV Builder ↗</a>
      <!-- DARK MODE TOGGLE -->
      <button class="theme-toggle" onclick={toggleDark} aria-label="Přepnout téma">
        {#if dark}
          <!-- Sun icon -->
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="5"/>
            <line x1="12" y1="1" x2="12" y2="3"/>
            <line x1="12" y1="21" x2="12" y2="23"/>
            <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/>
            <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/>
            <line x1="1" y1="12" x2="3" y2="12"/>
            <line x1="21" y1="12" x2="23" y2="12"/>
            <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/>
            <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/>
          </svg>
        {:else}
          <!-- Moon icon -->
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/>
          </svg>
        {/if}
      </button>
    </div>
  </div>
</nav>

<!-- ══ HERO ══ -->
<section class="hero" id="hero">
  <div class="hero-bg-word">VÁCLAV</div>
  <div class="hero-content">
    <p class="hero-eyebrow">Frontend Developer & IT Specialista</p>
    <h1 class="hero-h1">
      <div class="hero-line"><span>Tvořím věci,</span></div>
      <div class="hero-line"><span>které <em>baví</em></span></div>
      <div class="hero-line"><span>používat.</span></div>
    </h1>
    <p class="hero-sub">
      Student SPŠE Plzeň. IT technik v praxi u CBC net.<br />
      Specializuji se na moderní web, 3D vizualizace a správu sítí.
    </p>
    <div class="hero-btns">
      <a href="#work" class="btn-primary">Moje projekty</a>
      <a href="#contact" class="btn-ghost">Kontaktovat →</a>
    </div>
  </div>
  <div class="hero-scroll">
    <div class="hs-line"></div>
    <span>Scroll</span>
  </div>
  <div class="hero-meta">
    {#each [['4','Projekty'],['B2','Angličtina'],['2026','Maturita']] as [v,l]}
      <div class="hm-item">
        <span class="hm-val">{v}</span>
        <span class="hm-lbl">{l}</span>
      </div>
    {/each}
  </div>
</section>

<!-- ══ MARQUEE ══ -->
<div class="marquee">
  <div class="mq-track">
    {#each [1,2] as _}
      {#each ['SvelteKit','React','Next.js','Three.js','GSAP','TypeScript','CSS','Vite','WebGL','Linux','Kybernetická bezpečnost','Správa sítí','JavaScript'] as item}
        <span>{item}<em> ·</em></span>
      {/each}
    {/each}
  </div>
</div>

<!-- ══ PROJECTS ══ -->
<section class="sec-dark" id="work">
  <div class="container">
    <div class="sec-intro ap-reveal">
      <p class="eyebrow-light">Vybrané projekty</p>
      <h2 class="h2-light">Moje práce.</h2>
    </div>
    {#each projects as proj, i}
      <div class="proj-row" class:alt={i % 2 !== 0}>
        <div class="proj-info">
          <div class="proj-num ap-reveal">{proj.num}</div>
          <h3 class="proj-title ap-reveal">{proj.title}</h3>
          <p class="proj-sub ap-reveal" style="color:{proj.color}">{proj.sub}</p>
          <p class="proj-desc ap-reveal">{proj.desc}</p>
          <div class="proj-tech ap-reveal">{#each proj.tech as t}<span>{t}</span>{/each}</div>
          <a href={proj.url} target={proj.url.startsWith('http')?'_blank':'_self'} class="proj-link ap-reveal" style="color:{proj.color}">
            Zobrazit projekt →
          </a>
        </div>
        <div class="proj-visual-wrap">
          <div class="proj-visual">
            <div class="pv-inner">
              <div class="pv-dot" style="background:{proj.color}"></div>
              <div class="pv-title" style="color:{proj.color}">{proj.title}</div>
              <div class="pv-tech">{#each proj.tech as t}<span>{t}</span>{/each}</div>
            </div>
          </div>
        </div>
      </div>
    {/each}
  </div>
</section>

<!-- ══ ABOUT ══ -->
<section class="sec-light" id="about">
  <div class="container">
    <div class="about-grid">
      <div class="about-left">
        <p class="eyebrow-dark ap-reveal">O mně</p>
        <h2 class="h2-dark ap-reveal">Václav<br />Urbanec.</h2>
        <div class="avail-badge ap-reveal">
          <span class="badge-dot"></span>
          Dostupný pro práci
        </div>
      </div>
      <div class="about-right">
        <p class="about-p ap-reveal">
          Jsem student čtvrtého ročníku na <strong>SPŠE Plzeň</strong>, obor Informační technologie — Správa sítí a bezpečnost. Paralelně se věnuji frontend vývoji a moderním webovým technologiím.
        </p>
        <p class="about-p ap-reveal">
          Praxí u <strong>CBC net</strong> jsem získal zkušenosti se správou síťové infrastruktury a konfigurací zařízení. Web mě baví — rád tvořím věci, které jsou funkční i krásné.
        </p>
        <div class="about-cards ap-stagger">
          {#each [
            ['📍','Lokalita','Rakovník / Plzeň'],
            ['🎓','Škola','SPŠE Plzeň, 2022–2026'],
            ['💼','Praxe','CBC net — IT technik'],
            ['🌍','Jazyky','CZ native · EN B2'],
            ['📧','E-mail','vaclav.urbanec2@gmail.com'],
            ['📞','Telefon','605 568 856'],
          ] as [icon,label,val]}
            <div class="about-card ap-si">
              <span class="ac-icon">{icon}</span>
              <div>
                <div class="ac-label">{label}</div>
                <div class="ac-val">{val}</div>
              </div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ══ QUOTE ══ -->
<section class="sec-dark quote-sec">
  <div class="container">
    <div style="overflow:hidden"><p class="quote-text split-reveal">"Každý projekt je šance</p></div>
    <div style="overflow:hidden"><p class="quote-text split-reveal">naučit se něco nového."</p></div>
  </div>
</section>

<!-- ══ SKILLS ══ -->
<section class="sec-light" id="skills">
  <div class="container">
    <p class="eyebrow-dark ap-reveal">Dovednosti</p>
    <h2 class="h2-dark ap-reveal">Co umím.</h2>
    <div class="skills-grid ap-stagger">
      {#each skills as s}
        <div class="skill-item ap-si">
          <div class="skill-top">
            <span class="skill-name">{s.name}</span>
            <span class="skill-pct">{s.pct}%</span>
          </div>
          <div class="skill-track">
            <div class="skill-fill" data-w="{s.pct}%" style="width:{s.pct}%"></div>
          </div>
        </div>
      {/each}
    </div>
  </div>
</section>

<!-- ══ CONTACT ══ -->
<section class="sec-dark" id="contact">
  <div class="container">
    <div class="contact-grid">
      <div class="contact-left">
        <p class="eyebrow-light ap-reveal">Kontakt</p>
        <h2 class="h2-light ap-reveal">Pojďme<br /><em>spolupracovat.</em></h2>
        <p class="contact-desc ap-reveal">Hledáš junior frontend developera<br />nebo IT specialistu? Ozvi se.</p>
        <div class="clinks ap-reveal">
          <a href="mailto:vaclav.urbanec2@gmail.com" class="clink">vaclav.urbanec2@gmail.com ↗</a>
          <a href="tel:+420605568856" class="clink">605 568 856 ↗</a>
          <a href="https://github.com/bador108" target="_blank" class="clink">github.com/bador108 ↗</a>
        </div>
      </div>
      <div class="contact-right ap-reveal">
        {#if formStatus === 'success'}
          <div class="form-ok">
            <div class="form-ok-icon">✓</div>
            <h3>Odesláno!</h3>
            <p>Ozvu se co nejdříve. Díky!</p>
          </div>
        {:else}
          <form onsubmit={handleSubmit} class="form">
            <div class="field"><label>Jméno</label><input bind:value={formName} placeholder="Jan Novák" required /></div>
            <div class="field"><label>E-mail</label><input bind:value={formEmail} type="email" placeholder="jan@example.com" required /></div>
            <div class="field"><label>Zpráva</label><textarea bind:value={formMsg} rows="5" placeholder="Ahoj Václave..." required></textarea></div>
            <button type="submit" class="form-btn" disabled={formStatus === 'sending'}>
              {formStatus === 'sending' ? 'Odesílám...' : 'Odeslat zprávu'}
            </button>
            {#if formStatus === 'error'}<p class="form-err">Chyba. Napiš přímo na email.</p>{/if}
          </form>
        {/if}
      </div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="container footer-inner">
    <span class="footer-logo">VU.</span>
    <span class="footer-copy">© 2026 Václav Urbanec · Rakovník / Plzeň</span>
    <div class="footer-links">
      <a href="/cv">CV Builder</a>
      <a href="https://clone-wars-psi.vercel.app/" target="_blank">Hub</a>
      <a href="https://github.com/bador108" target="_blank">GitHub</a>
    </div>
  </div>
</footer>

<style>
  /* ── CSS VARIABLES — light/dark ── */
  :global(:root), :global([data-theme='light']) {
    --bg:        #ffffff;
    --bg2:       #f5f5f7;
    --fg:        #1d1d1f;
    --fg2:       rgba(0,0,0,.55);
    --fg3:       rgba(0,0,0,.35);
    --border:    rgba(0,0,0,.08);
    --card-bg:   #f5f5f7;
    --card-hover:#ebebeb;
    --nav-bg:    rgba(255,255,255,.82);
    --track-bg:  rgba(0,0,0,.08);
    --mq-bg:     #f5f5f7;
    --mq-color:  rgba(0,0,0,.35);
    --input-bg:  rgba(0,0,0,.04);
    --input-border: rgba(0,0,0,.12);
    --input-focus:  rgba(0,0,0,.3);
    --input-color: #1d1d1f;
    --input-ph:  rgba(0,0,0,.25);
    --sd-bg:     #f0f0f2;
    --sd-fg:     #1d1d1f;
    --sd-fg2:    rgba(0,0,0,.55);
    --sd-fg3:    rgba(0,0,0,.35);
    --sd-border: rgba(0,0,0,.08);
    --sd-card:   rgba(0,0,0,.05);
    --sd-card-text: rgba(0,0,0,.5);
    --sd-subtle: rgba(0,0,0,.15);
    --sd-visual: #e0e0e5;
  }

  :global([data-theme='dark']) {
    --bg:        #000000;
    --bg2:       #1c1c1e;
    --fg:        #f5f5f7;
    --fg2:       rgba(255,255,255,.55);
    --fg3:       rgba(255,255,255,.35);
    --border:    rgba(255,255,255,.08);
    --card-bg:   #2c2c2e;
    --card-hover:#3a3a3c;
    --nav-bg:    rgba(0,0,0,.75);
    --track-bg:  rgba(255,255,255,.12);
    --mq-bg:     #1c1c1e;
    --mq-color:  rgba(255,255,255,.3);
    --input-bg:  rgba(255,255,255,.06);
    --input-border: rgba(255,255,255,.12);
    --input-focus:  rgba(255,255,255,.4);
    --input-color: #f5f5f7;
    --input-ph:  rgba(255,255,255,.2);
    --sd-bg:     #000000;
    --sd-fg:     #ffffff;
    --sd-fg2:    rgba(255,255,255,.5);
    --sd-fg3:    rgba(255,255,255,.25);
    --sd-border: rgba(255,255,255,.08);
    --sd-card:   rgba(255,255,255,.1);
    --sd-card-text: rgba(255,255,255,.6);
    --sd-subtle: rgba(255,255,255,.15);
    --sd-visual: #111111;
  }

  /* ── RESET ── */
  :global(*, *::before, *::after) { margin:0; padding:0; box-sizing:border-box; }
  :global(html) { scroll-behavior:smooth; }
  :global(body) {
    font-family: -apple-system,'SF Pro Display','SF Pro Text','Inter',sans-serif;
    background: var(--bg); color: var(--fg);
    overflow-x: hidden; -webkit-font-smoothing: antialiased;
    transition: background .3s, color .3s;
  }
  :global(::-webkit-scrollbar) { width:0; }
  :global(a) { text-decoration:none; }

  /* ── SCROLL PROGRESS ── */
  .scroll-prog {
    position:fixed; top:0; left:0; right:0; height:2px;
    background:#0071e3; z-index:1000;
    transform:scaleX(0); transform-origin:left;
  }

  /* ── NAV ── */
  nav {
    position:fixed; top:0; left:0; right:0; z-index:500;
    border-bottom:1px solid transparent;
    transition:background .4s, backdrop-filter .4s, border-color .4s;
  }
  nav.scrolled {
    background: var(--nav-bg);
    backdrop-filter:blur(20px) saturate(180%);
    -webkit-backdrop-filter:blur(20px) saturate(180%);
    border-color: var(--border);
  }
  .nav-inner {
    max-width:1200px; margin:0 auto;
    padding:0 48px; height:52px;
    display:flex; align-items:center; justify-content:space-between;
  }
  .nav-logo { font-size:17px; font-weight:700; letter-spacing:-.5px; color:var(--fg); }
  .nav-links { display:flex; gap:32px; }
  .nav-links a { font-size:13px; color:var(--fg2); transition:color .2s; }
  .nav-links a:hover { color:var(--fg); }
  .nav-actions { display:flex; align-items:center; gap:16px; }
  .nav-cv { font-size:13px; font-weight:500; color:#0071e3; transition:opacity .2s; }
  .nav-cv:hover { opacity:.75; }

  /* THEME TOGGLE */
  .theme-toggle {
    width:32px; height:32px; border-radius:50%;
    background:var(--card-bg); border:1px solid var(--border);
    color:var(--fg2); display:flex; align-items:center; justify-content:center;
    transition:background .2s, color .2s; cursor:pointer;
  }
  .theme-toggle:hover { background:var(--card-hover); color:var(--fg); }

  /* ── HERO ── */
  .hero {
    min-height:100vh; display:flex; align-items:center; justify-content:center;
    position:relative; overflow:hidden;
    background:var(--bg); padding:80px 48px 60px;
    transition:background .3s;
  }
  .hero-bg-word {
    position:absolute; left:50%; top:50%; transform:translate(-50%,-50%);
    font-size:clamp(120px,18vw,280px); font-weight:800; letter-spacing:-8px;
    color:var(--border); white-space:nowrap; pointer-events:none; user-select:none; line-height:1;
  }
  .hero-content { position:relative; z-index:2; text-align:center; max-width:900px; }
  .hero-eyebrow { font-size:14px; color:var(--fg3); margin-bottom:28px; letter-spacing:.3px; }
  .hero-h1 {
    font-size:clamp(56px,9vw,120px); font-weight:700; letter-spacing:-4px;
    line-height:.92; color:var(--fg); margin-bottom:28px;
  }
  .hero-line { overflow:hidden; display:block; }
  .hero-line span { display:block; }
  .hero-h1 em {
    font-style:normal;
    background:linear-gradient(135deg,#0071e3,#5e5ce6);
    -webkit-background-clip:text; -webkit-text-fill-color:transparent;
  }
  .hero-sub {
    font-size:clamp(17px,2vw,21px); font-weight:400; line-height:1.6;
    color:var(--fg2); margin-bottom:44px; letter-spacing:-.3px;
  }
  .hero-btns { display:flex; gap:14px; justify-content:center; }
  .btn-primary {
    font-size:15px; font-weight:500; padding:14px 28px;
    background:#0071e3; color:#fff; border-radius:980px; transition:background .2s,transform .2s;
  }
  .btn-primary:hover { background:#0077ed; transform:scale(1.02); }
  .btn-ghost {
    font-size:15px; font-weight:500; padding:14px 28px; color:#0071e3;
    border:1.5px solid rgba(0,113,227,.3); border-radius:980px; transition:border-color .2s,transform .2s;
  }
  .btn-ghost:hover { border-color:#0071e3; transform:scale(1.02); }
  .hero-scroll {
    position:absolute; bottom:36px; left:50%; transform:translateX(-50%);
    display:flex; flex-direction:column; align-items:center; gap:10px;
    font-size:10px; letter-spacing:2px; color:var(--fg3); text-transform:uppercase;
  }
  .hs-line {
    width:1px; height:44px;
    background:linear-gradient(to bottom,var(--fg3),transparent);
    animation:spulse 2s ease-in-out infinite;
  }
  @keyframes spulse { 0%,100%{opacity:.3;transform:scaleY(.5)} 50%{opacity:1;transform:scaleY(1)} }
  .hero-meta {
    position:absolute; bottom:40px; right:52px;
    display:flex; flex-direction:column; gap:20px; text-align:right;
  }
  .hm-item { display:flex; flex-direction:column; gap:2px; }
  .hm-val { font-size:24px; font-weight:700; color:var(--fg); line-height:1; letter-spacing:-1px; }
  .hm-lbl { font-size:11px; color:var(--fg3); }

  /* ── MARQUEE ── */
  .marquee {
    overflow:hidden; border-top:1px solid var(--border); border-bottom:1px solid var(--border);
    padding:14px 0; background:var(--mq-bg); transition:background .3s;
  }
  .mq-track { display:flex; white-space:nowrap; width:max-content; }
  .mq-track span { font-size:12px; font-weight:500; letter-spacing:1.5px; text-transform:uppercase; color:var(--mq-color); padding:0 28px; }
  .mq-track em { color:var(--border); font-style:normal; }

  /* ── SECTIONS ── */
  .sec-light { background:var(--bg);  padding:120px 0; transition:background .3s; }
  .sec-dark  { background:var(--sd-bg); padding:120px 0; transition:background .3s; }
  :global([data-theme='dark']) .sec-light { background:var(--bg2); }
  .container { max-width:1200px; margin:0 auto; padding:0 48px; }

  .eyebrow-dark  { font-size:12px; font-weight:600; letter-spacing:2px; text-transform:uppercase; color:var(--fg3); margin-bottom:12px; }
  .eyebrow-light { font-size:12px; font-weight:600; letter-spacing:2px; text-transform:uppercase; color:var(--sd-fg3); margin-bottom:12px; }

  .h2-dark  { font-size:clamp(40px,6vw,72px); font-weight:700; letter-spacing:-2.5px; color:var(--fg);  line-height:.95; margin-bottom:64px; }
  .h2-light { font-size:clamp(40px,6vw,72px); font-weight:700; letter-spacing:-2.5px; color:var(--sd-fg); line-height:.95; margin-bottom:64px; }
  .h2-light em { font-style:normal; color:var(--sd-fg3); }
  .sec-intro { margin-bottom:80px; }

  /* ── PROJECTS ── */
  .proj-row {
    display:grid; grid-template-columns:1fr 1fr; gap:80px; align-items:center;
    padding:80px 0; border-top:1px solid var(--sd-border);
  }
  .proj-row:first-of-type { border-top:none; }
  .proj-row.alt { direction:rtl; }
  .proj-row.alt > * { direction:ltr; }
  .proj-num  { font-size:11px; font-weight:600; letter-spacing:2px; color:var(--sd-fg3); margin-bottom:12px; text-transform:uppercase; }
  .proj-title { font-size:clamp(28px,3.5vw,44px); font-weight:700; letter-spacing:-1.5px; color:var(--sd-fg); margin-bottom:8px; line-height:1; }
  .proj-sub  { font-size:15px; font-weight:500; margin-bottom:20px; }
  .proj-desc { font-size:16px; line-height:1.65; color:var(--sd-fg2); margin-bottom:24px; letter-spacing:-.2px; }
  .proj-tech { display:flex; gap:8px; flex-wrap:wrap; margin-bottom:28px; }
  .proj-tech span { font-size:11px; font-weight:600; letter-spacing:1px; text-transform:uppercase; color:var(--sd-fg3); border:1px solid var(--sd-subtle); padding:4px 12px; border-radius:980px; }
  .proj-link { font-size:15px; font-weight:500; transition:opacity .2s; }
  .proj-link:hover { opacity:.75; }
  .proj-visual-wrap { position:relative; }
  .proj-visual {
    aspect-ratio:4/3; border-radius:18px; overflow:hidden; background:var(--sd-visual);
    display:flex; align-items:center; justify-content:center;
    border:1px solid var(--sd-border);
  }
  .pv-inner { text-align:center; padding:40px; }
  .pv-dot { width:52px; height:52px; border-radius:50%; margin:0 auto 20px; opacity:.8; }
  .pv-title { font-size:22px; font-weight:700; letter-spacing:-.5px; margin-bottom:16px; color:var(--sd-fg); }
  .pv-tech { display:flex; gap:8px; justify-content:center; flex-wrap:wrap; }
  .pv-tech span { font-size:11px; padding:3px 10px; border-radius:980px; background:var(--sd-card); color:var(--sd-card-text); }

  /* ── ABOUT ── */
  .about-grid { display:grid; grid-template-columns:1fr 1.4fr; gap:80px; align-items:start; }
  .avail-badge {
    display:inline-flex; align-items:center; gap:8px;
    font-size:13px; font-weight:500; color:var(--fg);
    background:var(--card-bg); padding:8px 16px; border-radius:980px; margin-top:24px;
    transition:background .3s;
  }
  .badge-dot { width:8px; height:8px; border-radius:50%; background:#34c759; animation:blink 1.6s ease infinite; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:.2} }
  .about-p { font-size:17px; line-height:1.7; color:var(--fg2); margin-bottom:20px; letter-spacing:-.2px; }
  .about-p strong { color:var(--fg); font-weight:600; }
  .about-cards { display:grid; grid-template-columns:1fr 1fr; gap:12px; margin-top:36px; }
  .about-card { display:flex; align-items:center; gap:12px; background:var(--card-bg); border-radius:12px; padding:14px 16px; transition:background .2s; }
  .about-card:hover { background:var(--card-hover); }
  .ac-icon { font-size:20px; flex-shrink:0; }
  .ac-label { font-size:11px; color:var(--fg3); font-weight:500; letter-spacing:.3px; margin-bottom:2px; }
  .ac-val { font-size:13px; font-weight:500; color:var(--fg); }

  /* ── QUOTE ── */
  .quote-sec { padding:100px 0; }
  .quote-text { font-size:clamp(36px,5.5vw,68px); font-weight:700; letter-spacing:-2px; color:var(--sd-fg); line-height:1.05; display:block; }

  /* ── SKILLS ── */
  .skills-grid { display:grid; grid-template-columns:1fr 1fr; gap:32px 64px; }
  .skill-top { display:flex; justify-content:space-between; align-items:baseline; margin-bottom:10px; }
  .skill-name { font-size:15px; font-weight:500; color:var(--fg); letter-spacing:-.2px; }
  .skill-pct  { font-size:13px; color:var(--fg3); }
  .skill-track { height:3px; background:var(--track-bg); border-radius:2px; position:relative; overflow:hidden; }
  .skill-fill  { position:absolute; top:0; left:0; height:100%; background:#0071e3; border-radius:2px; }

  /* ── CONTACT ── */
  .contact-grid { display:grid; grid-template-columns:1fr 1.2fr; gap:80px; align-items:start; }
  .contact-desc { font-size:17px; line-height:1.65; color:var(--sd-fg2); margin:20px 0 36px; letter-spacing:-.2px; }
  .clinks { display:flex; flex-direction:column; gap:16px; }
  .clink { font-size:15px; font-weight:500; color:var(--sd-card-text); transition:color .2s; border-bottom:1px solid var(--sd-border); padding-bottom:16px; }
  .clink:hover { color:var(--sd-fg); }
  .form { display:flex; flex-direction:column; gap:16px; }
  .field { display:flex; flex-direction:column; gap:6px; }
  .field label { font-size:11px; font-weight:600; letter-spacing:1.5px; text-transform:uppercase; color:var(--sd-fg3); }
  .field input, .field textarea {
    background:var(--input-bg); border:1px solid var(--input-border);
    color:var(--input-color); font-family:inherit; font-size:15px;
    padding:12px 16px; outline:none; resize:vertical; border-radius:10px; transition:border-color .2s;
  }
  .field input:focus, .field textarea:focus { border-color:var(--input-focus); }
  .field input::placeholder, .field textarea::placeholder { color:var(--input-ph); }
  .form-btn {
    font-family:inherit; font-size:15px; font-weight:500;
    padding:13px 28px; background:#0071e3; color:#fff;
    border:none; border-radius:980px; transition:background .2s,transform .2s; align-self:flex-start;
  }
  .form-btn:hover:not(:disabled) { background:#0077ed; transform:scale(1.02); }
  .form-btn:disabled { opacity:.5; }
  .form-err { font-size:13px; color:#ff453a; }
  .form-ok { display:flex; flex-direction:column; gap:12px; padding:40px 0; }
  .form-ok-icon { width:48px; height:48px; border-radius:50%; background:#34c759; color:#fff; display:flex; align-items:center; justify-content:center; font-size:22px; font-weight:700; }
  .form-ok h3 { font-size:22px; font-weight:700; color:var(--sd-fg); letter-spacing:-.5px; }
  .form-ok p  { font-size:15px; color:var(--sd-fg2); }

  /* ── FOOTER ── */
  footer { background:var(--sd-bg); border-top:1px solid var(--sd-border); padding:28px 0; transition:background .3s; }
  .footer-inner { display:flex; align-items:center; justify-content:space-between; }
  .footer-logo { font-size:16px; font-weight:700; color:var(--sd-fg3); }
  .footer-copy { font-size:12px; color:var(--sd-fg3); }
  .footer-links { display:flex; gap:24px; }
  .footer-links a { font-size:12px; color:var(--sd-fg3); transition:color .2s; }
  .footer-links a:hover { color:var(--sd-fg); }

  /* ── RESPONSIVE ── */
  @media (max-width:900px) {
    .nav-links { display:none; }
    .nav-inner { padding:0 20px; }
    .hero { padding:72px 20px 48px; }
    .hero-meta { display:none; }
    .hero-h1 { letter-spacing:-2px; }
    .hero-btns { flex-direction:column; align-items:center; gap:10px; }
    .btn-primary, .btn-ghost { width:100%; max-width:280px; text-align:center; }
    .container { padding:0 20px; }
    .sec-light, .sec-dark { padding:72px 0; }
    .h2-dark, .h2-light { margin-bottom:40px; letter-spacing:-1.5px; }
    .sec-intro { margin-bottom:48px; }
    .proj-row { grid-template-columns:1fr; gap:32px; direction:ltr !important; padding:48px 0; }
    .about-grid,.contact-grid,.skills-grid { grid-template-columns:1fr; gap:40px; }
    .about-cards { grid-template-columns:1fr; }
    .footer-inner { flex-direction:column; gap:12px; text-align:center; }
    .quote-sec { padding:64px 0; }
    .quote-text { letter-spacing:-1px; }
  }
</style>
