<script>
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { ScrollTrigger } from 'gsap/ScrollTrigger';

  gsap.registerPlugin(ScrollTrigger);

  const projects = [
    {
      num: '01',
      title: 'Star Wars Archive',
      sub: 'Interaktivní 3D archiv',
      desc: 'Clone Wars vesmír v prohlížeči. Galaktická mapa, velitelé, laboratoř světelného meče — vše animované a interaktivní.',
      tech: ['Next.js', 'React', 'Canvas API'],
      color: '#0071e3',
      bg: '#000',
      url: 'https://clone-wars-psi.vercel.app/star-wars',
    },
    {
      num: '02',
      title: 'F1 2026 Tracker',
      sub: 'Živá data & 3D vozy',
      desc: 'Přehled sezóny 2026 v reálném čase. Three.js 3D modely vozů, tabulky jezdců, týmů a kalendář závodů.',
      tech: ['Next.js', 'Three.js', 'GSAP'],
      color: '#ff3b30',
      bg: '#1c1c1e',
      url: 'https://clone-wars-psi.vercel.app/f1',
    },
    {
      num: '03',
      title: 'CV Builder',
      sub: 'Interaktivní tvůrce CV',
      desc: '3 šablony, 6 barev, live preview a export do PDF. Vytvoř profesionální CV za pár minut.',
      tech: ['SvelteKit', 'Svelte 5', 'CSS'],
      color: '#34c759',
      bg: '#f5f5f7',
      url: '/cv',
    },
    {
      num: '04',
      title: 'Projekt Hub',
      sub: 'Centrální rozcestník',
      desc: 'Rozcestník pro všechny projekty s animovaným hvězdným pozadím a interaktivními kartami.',
      tech: ['Next.js', 'React', 'Canvas API'],
      color: '#5e5ce6',
      bg: '#000',
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

  let navScrolled = false;
  let formName = '', formEmail = '', formMsg = '', formStatus = '';

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

  onMount(() => {
    // Nav scroll state
    const onScroll = () => { navScrolled = window.scrollY > 40; };
    window.addEventListener('scroll', onScroll);

    // Hero animation
    gsap.timeline({ delay: 0.2 })
      .from('.hero-eyebrow', { y: 20, opacity: 0, duration: .8, ease: 'power3.out' })
      .from('.hero-h1 span', { y: '100%', opacity: 0, duration: 1, stagger: .08, ease: 'power4.out' }, '-=.4')
      .from('.hero-sub',     { y: 24, opacity: 0, duration: .8, ease: 'power3.out' }, '-=.5')
      .from('.hero-btns a',  { y: 16, opacity: 0, duration: .6, stagger: .1, ease: 'power3.out' }, '-=.4')
      .from('.hero-scroll',  { opacity: 0, duration: 1 }, '-=.2');

    // Scroll progress
    gsap.to('.scroll-prog', {
      scaleX: 1, ease: 'none',
      scrollTrigger: { scrub: .3, start: 'top top', end: 'bottom bottom' },
    });

    // Hero parallax
    gsap.to('.hero-bg-word', {
      y: 200, opacity: 0,
      scrollTrigger: { scrub: 2, start: 'top top', end: 'bottom top' },
    });

    // Section reveals — Apple style (fade up)
    gsap.utils.toArray('.ap-reveal').forEach((el, i) => {
      gsap.from(el, {
        scrollTrigger: { trigger: el, start: 'top 88%', toggleActions: 'play none none none' },
        y: 48, opacity: 0, duration: 1, ease: 'power3.out',
      });
    });

    // Stagger reveals
    gsap.utils.toArray('.ap-stagger').forEach(parent => {
      const children = parent.querySelectorAll('.ap-stagger-item');
      gsap.from(children, {
        scrollTrigger: { trigger: parent, start: 'top 85%', toggleActions: 'play none none none' },
        y: 40, opacity: 0, duration: .8, stagger: .12, ease: 'power3.out',
      });
    });

    // Skill bars
    gsap.utils.toArray('.skill-fill').forEach(el => {
      gsap.fromTo(el, { width: '0%' }, {
        width: el.dataset.w,
        scrollTrigger: { trigger: el, start: 'top 92%', toggleActions: 'play none none none' },
        duration: 1.4, ease: 'power3.out',
      });
    });

    // Project cards parallax
    gsap.utils.toArray('.proj-visual').forEach(el => {
      gsap.to(el, {
        y: -40,
        scrollTrigger: { trigger: el.closest('.proj-row'), scrub: 1.5, start: 'top bottom', end: 'bottom top' },
      });
    });

    // Big text split reveal
    gsap.utils.toArray('.split-reveal').forEach(el => {
      gsap.from(el, {
        scrollTrigger: { trigger: el, start: 'top 80%', toggleActions: 'play none none none' },
        y: '100%', opacity: 0, duration: 1.1, ease: 'power4.out',
      });
    });

    // Marquee
    gsap.to('.mq-track', { x: '-50%', duration: 28, repeat: -1, ease: 'none' });

    return () => {
      window.removeEventListener('scroll', onScroll);
      ScrollTrigger.getAll().forEach(t => t.kill());
    };
  });
</script>

<!-- SCROLL PROGRESS -->
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
    <div class="hm-item">
      <span class="hm-val">4</span>
      <span class="hm-lbl">Projekty</span>
    </div>
    <div class="hm-item">
      <span class="hm-val">B2</span>
      <span class="hm-lbl">Angličtina</span>
    </div>
    <div class="hm-item">
      <span class="hm-val">2026</span>
      <span class="hm-lbl">Maturita</span>
    </div>
  </div>
</section>

<!-- ══ MARQUEE ══ -->
<div class="marquee">
  <div class="mq-track">
    {#each [1,2] as _}
      {#each ['SvelteKit','React','Next.js','Three.js','GSAP','TypeScript','CSS','Vite','WebGL','Linux','Kybernetická bezpečnost','Správa sítí','JavaScript','Node.js'] as item}
        <span>{item}<em> ·</em></span>
      {/each}
    {/each}
  </div>
</div>

<!-- ══ PROJECTS ══ -->
<section class="section-dark" id="work">
  <div class="container">
    <div class="sec-intro ap-reveal">
      <p class="eyebrow-light">Vybrané projekty</p>
      <h2 class="sec-h2-light">Moje práce.</h2>
    </div>

    {#each projects as proj, i}
      <div class="proj-row" class:proj-row-alt={i % 2 !== 0}>
        <div class="proj-info">
          <div class="proj-num ap-reveal">{proj.num}</div>
          <h3 class="proj-title ap-reveal">{proj.title}</h3>
          <p class="proj-sub ap-reveal" style="color:{proj.color}">{proj.sub}</p>
          <p class="proj-desc ap-reveal">{proj.desc}</p>
          <div class="proj-tech ap-reveal">
            {#each proj.tech as t}<span>{t}</span>{/each}
          </div>
          <a href={proj.url} target={proj.url.startsWith('http') ? '_blank' : '_self'} class="proj-link ap-reveal" style="color:{proj.color}">
            Zobrazit projekt →
          </a>
        </div>

        <div class="proj-visual-wrap">
          <div class="proj-visual" style="background:{proj.bg}">
            <div class="proj-visual-inner">
              <div class="pv-dot" style="background:{proj.color}"></div>
              <div class="pv-title" style="color:{proj.color}">{proj.title}</div>
              <div class="pv-tech">
                {#each proj.tech as t}<span>{t}</span>{/each}
              </div>
            </div>
          </div>
        </div>
      </div>
    {/each}
  </div>
</section>

<!-- ══ ABOUT ══ -->
<section class="section-light" id="about">
  <div class="container">
    <div class="about-grid">
      <div class="about-left">
        <p class="eyebrow-dark ap-reveal">O mně</p>
        <h2 class="sec-h2-dark ap-reveal">
          Václav<br />Urbanec.
        </h2>
        <div class="about-badge ap-reveal">
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
            { icon: '📍', label: 'Lokalita', val: 'Rakovník / Plzeň' },
            { icon: '🎓', label: 'Škola', val: 'SPŠE Plzeň, 2022–2026' },
            { icon: '💼', label: 'Praxe', val: 'CBC net — IT technik' },
            { icon: '🌍', label: 'Jazyky', val: 'CZ native · EN B2' },
            { icon: '📧', label: 'E-mail', val: 'vaclav.urbanec2@gmail.com' },
            { icon: '📞', label: 'Telefon', val: '605 568 856' },
          ] as item}
            <div class="about-card ap-stagger-item">
              <span class="ac-icon">{item.icon}</span>
              <div>
                <div class="ac-label">{item.label}</div>
                <div class="ac-val">{item.val}</div>
              </div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ══ FULLWIDTH QUOTE ══ -->
<section class="section-dark quote-section">
  <div class="container">
    <div class="quote-wrap">
      <div style="overflow:hidden"><p class="quote-text split-reveal">"Každý projekt je šance</p></div>
      <div style="overflow:hidden"><p class="quote-text split-reveal">naučit se něco nového."</p></div>
    </div>
  </div>
</section>

<!-- ══ SKILLS ══ -->
<section class="section-light" id="skills">
  <div class="container">
    <p class="eyebrow-dark ap-reveal">Dovednosti</p>
    <h2 class="sec-h2-dark ap-reveal">Co umím.</h2>

    <div class="skills-grid ap-stagger">
      {#each skills as s}
        <div class="skill-item ap-stagger-item">
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
<section class="section-dark" id="contact">
  <div class="container">
    <div class="contact-grid">
      <div class="contact-left">
        <p class="eyebrow-light ap-reveal">Kontakt</p>
        <h2 class="sec-h2-light ap-reveal">
          Pojďme<br />
          <em>spolupracovat.</em>
        </h2>
        <p class="contact-desc ap-reveal">
          Hledáš junior frontend developera<br />nebo IT specialistu? Ozvi se.
        </p>
        <div class="contact-links ap-reveal">
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
            <div class="field">
              <label>Jméno</label>
              <input bind:value={formName} placeholder="Jan Novák" required />
            </div>
            <div class="field">
              <label>E-mail</label>
              <input bind:value={formEmail} type="email" placeholder="jan@example.com" required />
            </div>
            <div class="field">
              <label>Zpráva</label>
              <textarea bind:value={formMsg} rows="5" placeholder="Ahoj Václave..." required></textarea>
            </div>
            <button type="submit" class="form-btn" disabled={formStatus === 'sending'}>
              {formStatus === 'sending' ? 'Odesílám...' : 'Odeslat zprávu'}
            </button>
            {#if formStatus === 'error'}
              <p class="form-err">Chyba. Napiš přímo na email.</p>
            {/if}
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
    <span>© 2026 Václav Urbanec · Rakovník / Plzeň</span>
    <div class="footer-links">
      <a href="/cv">CV Builder</a>
      <a href="https://clone-wars-psi.vercel.app/" target="_blank">Hub</a>
      <a href="https://github.com/bador108" target="_blank">GitHub</a>
    </div>
  </div>
</footer>

<style>
  /* ── FONTS & RESET ── */
  @import url('https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap');

  :global(*, *::before, *::after) { margin: 0; padding: 0; box-sizing: border-box; }
  :global(html) { scroll-behavior: smooth; font-size: 16px; }
  :global(body) {
    font-family: -apple-system, 'SF Pro Display', 'SF Pro Text', 'Inter', sans-serif;
    background: #fff;
    color: #1d1d1f;
    overflow-x: hidden;
    -webkit-font-smoothing: antialiased;
  }
  :global(::-webkit-scrollbar) { width: 0; }
  :global(a) { text-decoration: none; }

  /* ── SCROLL PROGRESS ── */
  .scroll-prog {
    position: fixed; top: 0; left: 0; right: 0; height: 2px;
    background: #0071e3; z-index: 1000;
    transform: scaleX(0); transform-origin: left;
  }

  /* ── NAV ── */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 500;
    transition: background .4s, backdrop-filter .4s, border-color .4s;
    border-bottom: 1px solid transparent;
  }
  nav.scrolled {
    background: rgba(255,255,255,.82);
    backdrop-filter: blur(20px) saturate(180%);
    -webkit-backdrop-filter: blur(20px) saturate(180%);
    border-color: rgba(0,0,0,.08);
  }
  .nav-inner {
    max-width: 1200px; margin: 0 auto;
    padding: 0 48px; height: 52px;
    display: flex; align-items: center; justify-content: space-between;
  }
  .nav-logo {
    font-size: 17px; font-weight: 700; letter-spacing: -.5px;
    color: #1d1d1f;
  }
  .nav-links { display: flex; gap: 32px; }
  .nav-links a {
    font-size: 13px; font-weight: 400; color: rgba(0,0,0,.55);
    transition: color .2s; letter-spacing: -.1px;
  }
  .nav-links a:hover { color: #1d1d1f; }
  .nav-cv {
    font-size: 13px; font-weight: 500; color: #0071e3;
    transition: opacity .2s;
  }
  .nav-cv:hover { opacity: .75; }

  /* ── HERO ── */
  .hero {
    min-height: 100vh; display: flex; align-items: center; justify-content: center;
    position: relative; overflow: hidden; background: #fff;
    padding: 80px 48px 60px;
  }

  .hero-bg-word {
    position: absolute; left: 50%; top: 50%;
    transform: translate(-50%, -50%);
    font-size: clamp(120px, 18vw, 280px);
    font-weight: 800; letter-spacing: -8px;
    color: rgba(0,0,0,.04);
    white-space: nowrap; pointer-events: none; user-select: none;
    line-height: 1;
  }

  .hero-content { position: relative; z-index: 2; text-align: center; max-width: 900px; }

  .hero-eyebrow {
    font-size: 14px; font-weight: 400; letter-spacing: .5px;
    color: rgba(0,0,0,.4); margin-bottom: 28px;
  }

  .hero-h1 {
    font-size: clamp(56px, 9vw, 120px);
    font-weight: 700; letter-spacing: -4px; line-height: .92;
    color: #1d1d1f; margin-bottom: 28px;
  }
  .hero-line { overflow: hidden; display: block; }
  .hero-line span { display: block; }
  .hero-h1 em {
    font-style: normal;
    background: linear-gradient(135deg, #0071e3, #5e5ce6);
    -webkit-background-clip: text; -webkit-text-fill-color: transparent;
  }

  .hero-sub {
    font-size: clamp(17px, 2vw, 21px); font-weight: 400; line-height: 1.6;
    color: rgba(0,0,0,.5); margin-bottom: 44px; letter-spacing: -.3px;
  }

  .hero-btns { display: flex; gap: 14px; justify-content: center; }
  .btn-primary {
    font-size: 15px; font-weight: 500; letter-spacing: -.2px;
    padding: 14px 28px; background: #0071e3; color: #fff;
    border-radius: 980px; transition: background .2s, transform .2s;
  }
  .btn-primary:hover { background: #0077ed; transform: scale(1.02); }
  .btn-ghost {
    font-size: 15px; font-weight: 500; letter-spacing: -.2px;
    padding: 14px 28px; color: #0071e3;
    border: 1.5px solid rgba(0,113,227,.3); border-radius: 980px;
    transition: border-color .2s, transform .2s;
  }
  .btn-ghost:hover { border-color: #0071e3; transform: scale(1.02); }

  .hero-scroll {
    position: absolute; bottom: 36px; left: 50%; transform: translateX(-50%);
    display: flex; flex-direction: column; align-items: center; gap: 10px;
    font-size: 10px; letter-spacing: 2px; color: rgba(0,0,0,.25); text-transform: uppercase;
  }
  .hs-line {
    width: 1px; height: 44px;
    background: linear-gradient(to bottom, rgba(0,0,0,.3), transparent);
    animation: spulse 2s ease-in-out infinite;
  }
  @keyframes spulse { 0%,100% { opacity: .3; transform: scaleY(.5) } 50% { opacity: 1; transform: scaleY(1) } }

  .hero-meta {
    position: absolute; bottom: 40px; right: 52px;
    display: flex; flex-direction: column; gap: 20px; text-align: right;
  }
  .hm-item { display: flex; flex-direction: column; gap: 2px; }
  .hm-val { font-size: 24px; font-weight: 700; color: #1d1d1f; line-height: 1; letter-spacing: -1px; }
  .hm-lbl { font-size: 11px; color: rgba(0,0,0,.35); letter-spacing: .3px; }

  /* ── MARQUEE ── */
  .marquee {
    overflow: hidden; border-top: 1px solid rgba(0,0,0,.08);
    border-bottom: 1px solid rgba(0,0,0,.08);
    padding: 14px 0; background: #f5f5f7;
  }
  .mq-track { display: flex; white-space: nowrap; width: max-content; }
  .mq-track span {
    font-size: 12px; font-weight: 500; letter-spacing: 1.5px; text-transform: uppercase;
    color: rgba(0,0,0,.35); padding: 0 28px;
  }
  .mq-track em { color: rgba(0,0,0,.15); font-style: normal; }

  /* ── SECTIONS ── */
  .section-light { background: #fff; padding: 120px 0; }
  .section-dark  { background: #000; padding: 120px 0; }
  .container { max-width: 1200px; margin: 0 auto; padding: 0 48px; }

  .eyebrow-dark  { font-size: 12px; font-weight: 600; letter-spacing: 2px; text-transform: uppercase; color: rgba(0,0,0,.35); margin-bottom: 12px; }
  .eyebrow-light { font-size: 12px; font-weight: 600; letter-spacing: 2px; text-transform: uppercase; color: rgba(255,255,255,.4); margin-bottom: 12px; }

  .sec-h2-dark {
    font-size: clamp(40px, 6vw, 72px); font-weight: 700;
    letter-spacing: -2.5px; color: #1d1d1f; line-height: .95; margin-bottom: 64px;
  }
  .sec-h2-light {
    font-size: clamp(40px, 6vw, 72px); font-weight: 700;
    letter-spacing: -2.5px; color: #fff; line-height: .95; margin-bottom: 64px;
  }
  .sec-h2-light em { font-style: normal; color: rgba(255,255,255,.4); }

  .sec-intro { margin-bottom: 80px; }

  /* ── PROJECTS ── */
  .proj-row {
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 80px; align-items: center; padding: 80px 0;
    border-top: 1px solid rgba(255,255,255,.08);
  }
  .proj-row:first-of-type { border-top: none; }
  .proj-row-alt { direction: rtl; }
  .proj-row-alt > * { direction: ltr; }

  .proj-num { font-size: 11px; font-weight: 600; letter-spacing: 2px; color: rgba(255,255,255,.25); margin-bottom: 12px; text-transform: uppercase; }
  .proj-title { font-size: clamp(28px, 3.5vw, 44px); font-weight: 700; letter-spacing: -1.5px; color: #fff; margin-bottom: 8px; line-height: 1; }
  .proj-sub { font-size: 15px; font-weight: 500; margin-bottom: 20px; }
  .proj-desc { font-size: 16px; font-weight: 400; line-height: 1.65; color: rgba(255,255,255,.5); margin-bottom: 24px; letter-spacing: -.2px; }
  .proj-tech { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 28px; }
  .proj-tech span {
    font-size: 11px; font-weight: 600; letter-spacing: 1px; text-transform: uppercase;
    color: rgba(255,255,255,.35); border: 1px solid rgba(255,255,255,.15);
    padding: 4px 12px; border-radius: 980px;
  }
  .proj-link { font-size: 15px; font-weight: 500; transition: opacity .2s; }
  .proj-link:hover { opacity: .75; }

  .proj-visual-wrap { position: relative; }
  .proj-visual {
    aspect-ratio: 4/3; border-radius: 18px; overflow: hidden;
    display: flex; align-items: center; justify-content: center;
    border: 1px solid rgba(255,255,255,.06);
  }
  .proj-visual-inner { text-align: center; padding: 40px; }
  .pv-dot { width: 52px; height: 52px; border-radius: 50%; margin: 0 auto 20px; opacity: .8; }
  .pv-title { font-size: 22px; font-weight: 700; letter-spacing: -.5px; margin-bottom: 16px; }
  .pv-tech { display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; }
  .pv-tech span { font-size: 11px; padding: 3px 10px; border-radius: 980px; background: rgba(255,255,255,.1); color: rgba(255,255,255,.6); }

  /* ── ABOUT ── */
  .about-grid { display: grid; grid-template-columns: 1fr 1.4fr; gap: 80px; align-items: start; }
  .about-badge {
    display: inline-flex; align-items: center; gap: 8px;
    font-size: 13px; font-weight: 500; color: #1d1d1f;
    background: #f5f5f7; padding: 8px 16px; border-radius: 980px;
    margin-top: 24px;
  }
  .badge-dot { width: 8px; height: 8px; border-radius: 50%; background: #34c759; animation: blink 1.6s ease infinite; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:.2} }
  .about-p { font-size: 17px; font-weight: 400; line-height: 1.7; color: rgba(0,0,0,.55); margin-bottom: 20px; letter-spacing: -.2px; }
  .about-p strong { color: #1d1d1f; font-weight: 600; }

  .about-cards { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-top: 36px; }
  .about-card {
    display: flex; align-items: center; gap: 12px;
    background: #f5f5f7; border-radius: 12px; padding: 14px 16px;
    transition: background .2s;
  }
  .about-card:hover { background: #ebebeb; }
  .ac-icon { font-size: 20px; flex-shrink: 0; }
  .ac-label { font-size: 11px; color: rgba(0,0,0,.35); font-weight: 500; letter-spacing: .3px; margin-bottom: 2px; }
  .ac-val { font-size: 13px; font-weight: 500; color: #1d1d1f; }

  /* ── QUOTE ── */
  .quote-section { padding: 100px 0; }
  .quote-wrap { max-width: 900px; }
  .quote-text {
    font-size: clamp(36px, 5.5vw, 68px); font-weight: 700;
    letter-spacing: -2px; color: #fff; line-height: 1.05;
    display: block;
  }

  /* ── SKILLS ── */
  .skills-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 32px 64px; }
  .skill-top { display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 10px; }
  .skill-name { font-size: 15px; font-weight: 500; color: #1d1d1f; letter-spacing: -.2px; }
  .skill-pct  { font-size: 13px; font-weight: 400; color: rgba(0,0,0,.35); }
  .skill-track { height: 3px; background: rgba(0,0,0,.08); border-radius: 2px; position: relative; overflow: hidden; }
  .skill-fill  { position: absolute; top: 0; left: 0; height: 100%; background: #0071e3; border-radius: 2px; }

  /* ── CONTACT ── */
  .contact-grid { display: grid; grid-template-columns: 1fr 1.2fr; gap: 80px; align-items: start; }
  .contact-desc { font-size: 17px; font-weight: 400; line-height: 1.65; color: rgba(255,255,255,.45); margin: 20px 0 36px; letter-spacing: -.2px; }
  .contact-links { display: flex; flex-direction: column; gap: 16px; }
  .clink { font-size: 15px; font-weight: 500; color: rgba(255,255,255,.6); transition: color .2s; border-bottom: 1px solid rgba(255,255,255,.08); padding-bottom: 16px; }
  .clink:hover { color: #fff; }

  /* FORM */
  .form { display: flex; flex-direction: column; gap: 16px; }
  .field { display: flex; flex-direction: column; gap: 6px; }
  .field label { font-size: 11px; font-weight: 600; letter-spacing: 1.5px; text-transform: uppercase; color: rgba(255,255,255,.3); }
  .field input, .field textarea {
    background: rgba(255,255,255,.06); border: 1px solid rgba(255,255,255,.12);
    color: #fff; font-family: inherit; font-size: 15px; font-weight: 400;
    padding: 12px 16px; outline: none; resize: vertical;
    border-radius: 10px; transition: border-color .2s;
  }
  .field input:focus, .field textarea:focus { border-color: rgba(255,255,255,.4); }
  .field input::placeholder, .field textarea::placeholder { color: rgba(255,255,255,.2); }
  .form-btn {
    font-family: inherit; font-size: 15px; font-weight: 500;
    padding: 13px 28px; background: #0071e3; color: #fff;
    border: none; border-radius: 980px; transition: background .2s, transform .2s; align-self: flex-start;
  }
  .form-btn:hover:not(:disabled) { background: #0077ed; transform: scale(1.02); }
  .form-btn:disabled { opacity: .5; }
  .form-err { font-size: 13px; color: #ff453a; margin-top: -4px; }

  .form-ok { display: flex; flex-direction: column; align-items: flex-start; gap: 12px; padding: 40px 0; }
  .form-ok-icon { width: 48px; height: 48px; border-radius: 50%; background: #34c759; color: #fff; display: flex; align-items: center; justify-content: center; font-size: 22px; font-weight: 700; }
  .form-ok h3 { font-size: 22px; font-weight: 700; color: #fff; letter-spacing: -.5px; }
  .form-ok p  { font-size: 15px; color: rgba(255,255,255,.45); }

  /* ── FOOTER ── */
  footer { background: #000; border-top: 1px solid rgba(255,255,255,.08); padding: 28px 0; }
  .footer-inner { display: flex; align-items: center; justify-content: space-between; }
  .footer-logo { font-size: 16px; font-weight: 700; color: rgba(255,255,255,.4); }
  footer span { font-size: 12px; color: rgba(255,255,255,.2); }
  .footer-links { display: flex; gap: 24px; }
  .footer-links a { font-size: 12px; color: rgba(255,255,255,.3); transition: color .2s; }
  .footer-links a:hover { color: #fff; }

  /* ── RESPONSIVE ── */
  @media (max-width: 900px) {
    .nav-links { display: none; }
    .nav-inner { padding: 0 24px; }
    .hero { padding: 80px 24px 60px; }
    .hero-meta { display: none; }
    .container { padding: 0 24px; }
    .proj-row { grid-template-columns: 1fr; gap: 40px; }
    .proj-row-alt { direction: ltr; }
    .about-grid, .contact-grid, .skills-grid { grid-template-columns: 1fr; gap: 40px; }
    .about-cards { grid-template-columns: 1fr; }
    .footer-inner { flex-direction: column; gap: 12px; text-align: center; }
  }
</style>