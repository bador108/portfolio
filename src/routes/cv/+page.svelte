<script>
  import { onMount, tick } from 'svelte';
  const TEMPLATES = [
    { id: 'modern',    label: 'Moderní' },
    { id: 'minimal',   label: 'Minimalistická' },
    { id: 'classic',   label: 'Klasická' },
    { id: 'timeline',  label: 'Timeline' },
    { id: 'executive', label: 'Executive' },
  ];

  const COLORS = [
    { hex: '#1e293b', label: 'Slate' },
    { hex: '#dc2626', label: 'Červená' },
    { hex: '#2563eb', label: 'Modrá' },
    { hex: '#16a34a', label: 'Zelená' },
    { hex: '#7c3aed', label: 'Fialová' },
    { hex: '#ea580c', label: 'Oranžová' },
  ];

  let cv = $state({
    firstName: '', lastName: '', role: '',
    email: '', phone: '', location: '',
    website: '', github: '', summary: '',
    experience: [], education: [], skills: [],
    languages: [], certs: [],
  });

  let template = $state('modern');
  let colorHex = $state('#2563eb');
  let activeSection = $state('personal');
  let mobileTab = $state('edit'); // 'edit' | 'preview'

  const sections = [
    { id: 'personal',   label: '👤 Osobní' },
    { id: 'experience', label: '💼 Praxe' },
    { id: 'education',  label: '🎓 Vzdělání' },
    { id: 'skills',     label: '⚡ Dovednosti' },
    { id: 'languages',  label: '🌍 Jazyky' },
    { id: 'certs',      label: '🏅 Certifikáty' },
  ];

  function addExp()       { cv.experience = [...cv.experience, { company: '', role: '', from: '', to: '', desc: '' }]; }
  function removeExp(i)   { cv.experience = cv.experience.filter((_, j) => j !== i); }
  function addEdu()       { cv.education  = [...cv.education,  { school: '', field: '', from: '', to: '', note: '' }]; }
  function removeEdu(i)   { cv.education  = cv.education.filter((_, j) => j !== i); }
  function addSkill()     { cv.skills     = [...cv.skills,     { name: '', level: 3 }]; }
  function removeSkill(i) { cv.skills     = cv.skills.filter((_, j) => j !== i); }
  function addLang()      { cv.languages  = [...cv.languages,  { name: '', level: '' }]; }
  function removeLang(i)  { cv.languages  = cv.languages.filter((_, j) => j !== i); }
  function addCert()      { cv.certs      = [...cv.certs,      { name: '', issuer: '', year: '' }]; }
  function removeCert(i)  { cv.certs      = cv.certs.filter((_, j) => j !== i); }

  async function exportPDF() {
    const prev = mobileTab;
    mobileTab = 'preview';
    await tick();
    window.print();
    mobileTab = prev;
  }
  function hasVal(v) { return v && v.trim() !== ''; }

  onMount(() => {
    const setScale = () => {
      const el = document.querySelector('.preview-inner');
      if (!el) return;
      if (window.innerWidth > 900) { el.style.zoom = ''; return; }
      el.style.zoom = String(Math.min(1, window.innerWidth / 820));
    };
    setScale();
    window.addEventListener('resize', setScale);
    return () => window.removeEventListener('resize', setScale);
  });
  const fullName = $derived((cv.firstName + ' ' + cv.lastName).trim());

  // Vrátí hodnotu nebo šedý placeholder pro náhled
  function v(val, placeholder) { return hasVal(val) ? val : placeholder; }
  function c(val) { return hasVal(val) ? '#1a1a1a' : '#ccc'; }
  function ca(val, accent) { return hasVal(val) ? accent : '#ccc'; }

  const DEMO = {
    name: 'Jan Novák',
    role: 'Frontend Developer',
    email: 'jan.novak@email.cz',
    phone: '605 123 456',
    location: 'Praha',
    github: 'github.com/jannovak',
    summary: 'Frontend developer s vášní pro čistý kód a skvělé uživatelské zážitky. Specializuji se na moderní JavaScript frameworky.',
    experience: [
      { role: 'Frontend Developer', company: 'Startup s.r.o.', from: '2023', to: 'současnost', desc: 'Vývoj UI v Reactu a TypeScriptu, optimalizace výkonu, spolupráce s designery.' },
      { role: 'Junior Developer', company: 'Agentura XY', from: '2021', to: '2023', desc: 'Tvorba webových stránek — HTML, CSS, JavaScript, WordPress.' },
    ],
    education: [
      { school: 'ČVUT Praha', field: 'Softwarové inženýrství', from: '2020', to: '2024', note: 'Bc.' },
    ],
    skills: [
      { name: 'React / Next.js', level: 4 },
      { name: 'TypeScript', level: 4 },
      { name: 'CSS / Tailwind', level: 5 },
      { name: 'Node.js', level: 3 },
    ],
    languages: [
      { name: 'Čeština', level: 'Rodilý mluvčí' },
      { name: 'Angličtina', level: 'B2' },
    ],
  };

  const showExp   = $derived(cv.experience.length > 0 ? cv.experience : DEMO.experience);
  const showEdu   = $derived(cv.education.length  > 0 ? cv.education  : DEMO.education);
  const showSkills= $derived(cv.skills.filter(s=>hasVal(s.name)).length > 0 ? cv.skills.filter(s=>hasVal(s.name)) : DEMO.skills);
  const showLangs = $derived(cv.languages.filter(l=>hasVal(l.name)).length > 0 ? cv.languages.filter(l=>hasVal(l.name)) : DEMO.languages);
  const isDemoExp    = $derived(cv.experience.length === 0);
  const isDemoEdu    = $derived(cv.education.length === 0);
  const isDemoSkills = $derived(cv.skills.filter(s=>hasVal(s.name)).length === 0);
  const isDemoLangs  = $derived(cv.languages.filter(l=>hasVal(l.name)).length === 0);
</script>

<svelte:head>
  <title>CV Builder</title>
  <link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,400;12..96,600;12..96,700;12..96,800&family=Inter:wght@300;400;500;600&family=Lora:ital,wght@0,400;0,600;1,400&family=DM+Serif+Display:ital@0;1&display=swap" rel="stylesheet" />
</svelte:head>

<div class="builder">

  <!-- ══ SIDEBAR ══ -->
  <aside class="panel" id="form-panel" class:mob-hidden={mobileTab !== 'edit'} >
    <div class="panel-head">
      <a href="/" class="back">← Portfolio</a>
      <span class="panel-title">CV Builder</span>
      <button class="pdf-btn" onclick={exportPDF}>⬇ PDF</button>
    </div>

    <div class="controls">
      <div class="ctrl-group">
        <span class="ctrl-lbl">Šablona</span>
        <div class="ctrl-row">
          {#each TEMPLATES as t}
            <button class="tpl" class:on={template === t.id} onclick={() => template = t.id}>{t.label}</button>
          {/each}
        </div>
      </div>
      <div class="ctrl-group">
        <span class="ctrl-lbl">Barva</span>
        <div class="ctrl-row">
          {#each COLORS as c}
            <button class="clr" class:on={colorHex === c.hex} style="background:{c.hex}" title={c.label} onclick={() => colorHex = c.hex}></button>
          {/each}
        </div>
      </div>
    </div>

    <div class="tabs">
      {#each sections as s}
        <button class="tab" class:on={activeSection === s.id} onclick={() => activeSection = s.id}>{s.label}</button>
      {/each}
    </div>

    <div class="form-body">

      {#if activeSection === 'personal'}
        <div class="r2">
          <div class="f"><label>Jméno</label><input bind:value={cv.firstName} placeholder="Jan" /></div>
          <div class="f"><label>Příjmení</label><input bind:value={cv.lastName} placeholder="Novák" /></div>
        </div>
        <div class="f"><label>Pozice / Role</label><input bind:value={cv.role} placeholder="Frontend Developer" /></div>
        <div class="r2">
          <div class="f"><label>E-mail</label><input bind:value={cv.email} type="email" placeholder="jan@email.cz" /></div>
          <div class="f"><label>Telefon</label><input bind:value={cv.phone} placeholder="605 123 456" /></div>
        </div>
        <div class="r2">
          <div class="f"><label>Lokalita</label><input bind:value={cv.location} placeholder="Praha" /></div>
          <div class="f"><label>GitHub <span class="opt">volitelné</span></label><input bind:value={cv.github} placeholder="github.com/..." /></div>
        </div>
        <div class="f"><label>Web / Portfolio <span class="opt">volitelné</span></label><input bind:value={cv.website} placeholder="mojeportfolio.cz" /></div>
        <div class="f"><label>O mně <span class="opt">volitelné</span></label><textarea bind:value={cv.summary} rows="4" placeholder="Pár vět o sobě..."></textarea></div>
      {/if}

      {#if activeSection === 'experience'}
        <p class="hint">Volitelné — zobrazí se jen pokud přidáš.</p>
        {#each cv.experience as exp, i}
          <div class="card">
            <div class="card-head"><span>Praxe {i+1}</span><button class="rm" onclick={() => removeExp(i)}>✕</button></div>
            <div class="f"><label>Firma</label><input bind:value={exp.company} placeholder="Název firmy" /></div>
            <div class="f"><label>Pozice</label><input bind:value={exp.role} placeholder="Junior Developer" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={exp.from} placeholder="2024" /></div>
              <div class="f"><label>Do <span class="opt">vol.</span></label><input bind:value={exp.to} placeholder="současnost" /></div>
            </div>
            <div class="f"><label>Popis <span class="opt">volitelné</span></label><textarea bind:value={exp.desc} rows="3" placeholder="Co jsi dělal..."></textarea></div>
          </div>
        {/each}
        <button class="add" onclick={addExp}>+ Přidat pracovní zkušenost</button>
      {/if}

      {#if activeSection === 'education'}
        <p class="hint">Volitelné — zobrazí se jen pokud přidáš.</p>
        {#each cv.education as edu, i}
          <div class="card">
            <div class="card-head"><span>Škola {i+1}</span><button class="rm" onclick={() => removeEdu(i)}>✕</button></div>
            <div class="f"><label>Škola</label><input bind:value={edu.school} placeholder="Název školy" /></div>
            <div class="f"><label>Obor <span class="opt">vol.</span></label><input bind:value={edu.field} placeholder="Informatika" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={edu.from} placeholder="2022" /></div>
              <div class="f"><label>Do</label><input bind:value={edu.to} placeholder="2026" /></div>
            </div>
            <div class="f"><label>Poznámka <span class="opt">vol.</span></label><input bind:value={edu.note} placeholder="Maturita, Bc., ..." /></div>
          </div>
        {/each}
        <button class="add" onclick={addEdu}>+ Přidat vzdělání</button>
      {/if}

      {#if activeSection === 'skills'}
        <p class="hint">Volitelné — kliknutím na tečky nastav úroveň.</p>
        {#each cv.skills as skill, i}
          <div class="skill-row">
            <input bind:value={skill.name} placeholder="Dovednost" class="skill-inp" />
            <div class="dots">
              {#each [1,2,3,4,5] as l}
                <button class="dot-btn" class:on={l <= skill.level} onclick={() => skill.level = l}></button>
              {/each}
            </div>
            <button class="rm sm" onclick={() => removeSkill(i)}>✕</button>
          </div>
        {/each}
        <button class="add" onclick={addSkill}>+ Přidat dovednost</button>
      {/if}

      {#if activeSection === 'languages'}
        <p class="hint">Volitelné.</p>
        {#each cv.languages as lang, i}
          <div class="card">
            <div class="card-head"><span>Jazyk {i+1}</span><button class="rm" onclick={() => removeLang(i)}>✕</button></div>
            <div class="r2">
              <div class="f"><label>Jazyk</label><input bind:value={lang.name} placeholder="Angličtina" /></div>
              <div class="f"><label>Úroveň</label><input bind:value={lang.level} placeholder="B2" /></div>
            </div>
          </div>
        {/each}
        <button class="add" onclick={addLang}>+ Přidat jazyk</button>
      {/if}

      {#if activeSection === 'certs'}
        <p class="hint">Volitelné.</p>
        {#each cv.certs as cert, i}
          <div class="card">
            <div class="card-head"><span>Certifikát {i+1}</span><button class="rm" onclick={() => removeCert(i)}>✕</button></div>
            <div class="f"><label>Název</label><input bind:value={cert.name} placeholder="Cambridge FCE" /></div>
            <div class="r2">
              <div class="f"><label>Vydavatel <span class="opt">vol.</span></label><input bind:value={cert.issuer} placeholder="Cambridge" /></div>
              <div class="f"><label>Rok <span class="opt">vol.</span></label><input bind:value={cert.year} placeholder="2024" /></div>
            </div>
          </div>
        {/each}
        <button class="add" onclick={addCert}>+ Přidat certifikát</button>
      {/if}

    </div>
  </aside>

  <!-- ══ PREVIEW ══ -->
  <main class="preview" class:mob-hidden={mobileTab !== 'preview'} >
    <div class="preview-inner">

      <!-- ── MODERN ── -->
      {#if template === 'modern'}
        <div class="cv mod-cv">
          <div class="mod-sidebar" style="background:{colorHex}">
            <div class="mod-name" style="opacity:{hasVal(fullName)?1:.45}">{v(fullName, DEMO.name)}</div>
            <div class="mod-role" style="opacity:{hasVal(cv.role)?1:.45}">{v(cv.role, DEMO.role)}</div>
            <div class="mod-divider"></div>
            <div class="mod-sec-title">Kontakt</div>
            <div class="mod-contacts">
              <div class="mod-ci" style="opacity:{hasVal(cv.email)?1:.45}">✉ {v(cv.email, DEMO.email)}</div>
              <div class="mod-ci" style="opacity:{hasVal(cv.phone)?1:.45}">📞 {v(cv.phone, DEMO.phone)}</div>
              <div class="mod-ci" style="opacity:{hasVal(cv.location)?1:.45}">📍 {v(cv.location, DEMO.location)}</div>
              {#if hasVal(cv.website)}<div class="mod-ci">🌐 {cv.website}</div>{/if}
              <div class="mod-ci" style="opacity:{hasVal(cv.github)?1:.45}">⌥ {v(cv.github, DEMO.github)}</div>
            </div>
            <div class="mod-divider"></div>
            <div class="mod-sec-title">Dovednosti</div>
            {#each showSkills as s}
              <div class="mod-skill" style="opacity:{isDemoSkills?0.4:1}">
                <span class="mod-skill-name">{s.name}</span>
                <div class="mod-skill-bar"><div class="mod-skill-fill" style="width:{s.level*20}%"></div></div>
              </div>
            {/each}
            <div class="mod-divider"></div>
            <div class="mod-sec-title">Jazyky</div>
            {#each showLangs as l}
              <div class="mod-lang" style="opacity:{isDemoLangs?0.4:1}">
                <span class="mod-lang-n">{l.name}</span>
                <span class="mod-lang-l">{l.level}</span>
              </div>
            {/each}
            {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
              <div class="mod-divider"></div>
              <div class="mod-sec-title">Certifikáty</div>
              {#each cv.certs as cert}{#if hasVal(cert.name)}
                <div class="mod-cert-item"><div class="mod-cert-n">{cert.name}</div>{#if hasVal(cert.year)}<div class="mod-cert-s">{cert.year}</div>{/if}</div>
              {/if}{/each}
            {/if}
          </div>
          <div class="mod-main">
            <div class="mod-sec">
              <div class="mod-title" style="color:{colorHex}">O mně</div>
              <p class="mod-text" style="opacity:{hasVal(cv.summary)?1:.4}">{v(cv.summary, DEMO.summary)}</p>
            </div>
            <div class="mod-sec">
              <div class="mod-title" style="color:{colorHex}">Pracovní zkušenosti</div>
              {#each showExp as exp}
                <div class="mod-entry" style="opacity:{isDemoExp?0.4:1}">
                  <div class="mod-dot" style="background:{colorHex}"></div>
                  <div class="mod-entry-body">
                    <div class="mod-entry-row">
                      <strong>{exp.role}</strong>
                      <span class="mod-date">{exp.from}{exp.to ? ` – ${exp.to}` : ' – současnost'}</span>
                    </div>
                    <div class="mod-company">{exp.company}</div>
                    {#if exp.desc}<p class="mod-desc">{exp.desc}</p>{/if}
                  </div>
                </div>
              {/each}
            </div>
            <div class="mod-sec">
              <div class="mod-title" style="color:{colorHex}">Vzdělání</div>
              {#each showEdu as edu}
                <div class="mod-entry" style="opacity:{isDemoEdu?0.4:1}">
                  <div class="mod-dot" style="background:{colorHex}"></div>
                  <div class="mod-entry-body">
                    <div class="mod-entry-row">
                      <strong>{edu.school}</strong>
                      {#if edu.from}<span class="mod-date">{edu.from}{edu.to ? ` – ${edu.to}` : ''}</span>{/if}
                    </div>
                    <div class="mod-company">{edu.field}{edu.note ? ` · ${edu.note}` : ''}</div>
                  </div>
                </div>
              {/each}
            </div>
          </div>
        </div>
      {/if}

      <!-- ── MINIMAL ── -->
      {#if template === 'minimal'}
        <div class="cv">
          <div class="n-head">
            <div style="flex:1;min-width:0">
              <div class="n-name" style="opacity:{hasVal(fullName)?1:.4}">{v(fullName, DEMO.name)}</div>
              <div class="n-role" style="color:{colorHex};opacity:{hasVal(cv.role)?1:.4}">{v(cv.role, DEMO.role)}</div>
            </div>
            <div class="n-contacts">
              <span style="opacity:{hasVal(cv.email)?1:.4}">{v(cv.email, DEMO.email)}</span>
              <span style="opacity:{hasVal(cv.phone)?1:.4}">{v(cv.phone, DEMO.phone)}</span>
              <span style="opacity:{hasVal(cv.location)?1:.4}">{v(cv.location, DEMO.location)}</span>
              {#if hasVal(cv.website)}<span>{cv.website}</span>{/if}
              <span style="opacity:{hasVal(cv.github)?1:.4}">{v(cv.github, DEMO.github)}</span>
            </div>
          </div>
          <div class="n-line" style="background:{colorHex}"></div>
          <p class="n-summary" style="opacity:{hasVal(cv.summary)?1:.4}">{v(cv.summary, DEMO.summary)}</p>
          <div class="n-sec">
            <div class="n-title" style="color:{colorHex}">Pracovní zkušenosti</div>
            {#each showExp as exp}
              <div class="n-entry" style="opacity:{isDemoExp?0.4:1}">
                <div class="n-entry-row"><strong>{exp.role}</strong><span>{exp.from}{exp.to ? ` – ${exp.to}` : ''}</span></div>
                <div class="n-sub">{exp.company}</div>
                {#if exp.desc}<p class="n-desc">{exp.desc}</p>{/if}
              </div>
            {/each}
          </div>
          <div class="n-sec">
            <div class="n-title" style="color:{colorHex}">Vzdělání</div>
            {#each showEdu as edu}
              <div class="n-entry" style="opacity:{isDemoEdu?0.4:1}">
                <div class="n-entry-row"><strong>{edu.school}</strong>{#if edu.from}<span>{edu.from}{edu.to ? ` – ${edu.to}` : ''}</span>{/if}</div>
                <div class="n-sub">{edu.field}{edu.note ? ` · ${edu.note}` : ''}</div>
              </div>
            {/each}
          </div>
          <div class="n-bottom">
            <div class="n-bot-col">
              <div class="n-title" style="color:{colorHex}">Dovednosti</div>
              <div class="n-tags" style="opacity:{isDemoSkills?0.4:1}">
                {#each showSkills as s}<span class="n-tag" style="border-color:{colorHex};color:{colorHex}">{s.name}</span>{/each}
              </div>
            </div>
            <div class="n-bot-col">
              <div class="n-title" style="color:{colorHex}">Jazyky</div>
              {#each showLangs as l}
                <div class="n-lang-item" style="opacity:{isDemoLangs?0.4:1}"><strong>{l.name}</strong> — {l.level}</div>
              {/each}
            </div>
            {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
              <div class="n-bot-col">
                <div class="n-title" style="color:{colorHex}">Certifikáty</div>
                {#each cv.certs as cert}{#if hasVal(cert.name)}<div class="n-lang-item"><strong>{cert.name}</strong>{hasVal(cert.year) ? ` (${cert.year})` : ''}</div>{/if}{/each}
              </div>
            {/if}
          </div>
        </div>
      {/if}

      <!-- ── CLASSIC ── -->
      {#if template === 'classic'}
        <div class="cv">
          <div class="cl-head" style="background:{colorHex}">
            <div class="cl-head-left">
              <div class="cl-name" style="opacity:{hasVal(fullName)?1:.6}">{v(fullName, DEMO.name)}</div>
              <div class="cl-role" style="opacity:{hasVal(cv.role)?1:.6}">{v(cv.role, DEMO.role)}</div>
            </div>
            <div class="cl-contacts">
              <div class="cl-c" style="opacity:{hasVal(cv.email)?1:.6}">✉ {v(cv.email, DEMO.email)}</div>
              <div class="cl-c" style="opacity:{hasVal(cv.phone)?1:.6}">📞 {v(cv.phone, DEMO.phone)}</div>
              <div class="cl-c" style="opacity:{hasVal(cv.location)?1:.6}">📍 {v(cv.location, DEMO.location)}</div>
              {#if hasVal(cv.website)}<div class="cl-c">🌐 {cv.website}</div>{/if}
            </div>
          </div>
          <div class="cl-body">
            <div class="cl-main">
              <div class="cl-sec">
                <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Profil</div>
                <p class="cl-text" style="opacity:{hasVal(cv.summary)?1:.4}">{v(cv.summary, DEMO.summary)}</p>
              </div>
              <div class="cl-sec">
                <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Pracovní zkušenosti</div>
                {#each showExp as exp}
                  <div class="cl-entry" style="opacity:{isDemoExp?0.4:1}">
                    <div class="cl-dot" style="background:{colorHex}"></div>
                    <div>
                      <div class="cl-eh"><strong>{exp.role}</strong> <em>· {exp.company}</em></div>
                      <div class="cl-date">{exp.from}{exp.to ? ` – ${exp.to}` : ' – současnost'}</div>
                      {#if exp.desc}<p class="cl-desc">{exp.desc}</p>{/if}
                    </div>
                  </div>
                {/each}
              </div>
              <div class="cl-sec">
                <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Vzdělání</div>
                {#each showEdu as edu}
                  <div class="cl-entry" style="opacity:{isDemoEdu?0.4:1}">
                    <div class="cl-dot" style="background:{colorHex}"></div>
                    <div>
                      <div class="cl-eh"><strong>{edu.school}</strong></div>
                      <div class="cl-date">{edu.field}{edu.from ? ` · ${edu.from}` : ''}{edu.to ? ` – ${edu.to}` : ''}</div>
                    </div>
                  </div>
                {/each}
              </div>
            </div>
            <div class="cl-side">
              <div class="cl-sec">
                <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Dovednosti</div>
                {#each showSkills as s}
                  <div class="cl-skill" style="opacity:{isDemoSkills?0.4:1}">
                    <span>{s.name}</span>
                    <div class="cl-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                  </div>
                {/each}
              </div>
              <div class="cl-sec">
                <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Jazyky</div>
                {#each showLangs as l}
                  <div class="cl-lang" style="opacity:{isDemoLangs?0.4:1}"><strong>{l.name}</strong><span>{l.level}</span></div>
                {/each}
              </div>
              {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
                <div class="cl-sec">
                  <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Certifikáty</div>
                  {#each cv.certs as cert}{#if hasVal(cert.name)}
                    <div class="cl-cert"><strong>{cert.name}</strong>{#if hasVal(cert.year)}<span>{cert.year}</span>{/if}</div>
                  {/if}{/each}
                </div>
              {/if}
            </div>
          </div>
        </div>
      {/if}

      <!-- ── TIMELINE ── -->
      {#if template === 'timeline'}
        <div class="cv">
          <div class="tl-head">
            <div class="tl-accent" style="background:{colorHex}"></div>
            <div class="tl-head-content">
              <div class="tl-name" style="opacity:{hasVal(fullName)?1:.4}">{v(fullName, DEMO.name)}</div>
              <div class="tl-role" style="color:{colorHex};opacity:{hasVal(cv.role)?1:.4}">{v(cv.role, DEMO.role)}</div>
              <div class="tl-contacts">
                <span style="opacity:{hasVal(cv.email)?1:.4}">{v(cv.email, DEMO.email)}</span>
                <span style="opacity:{hasVal(cv.phone)?1:.4}">{v(cv.phone, DEMO.phone)}</span>
                <span style="opacity:{hasVal(cv.location)?1:.4}">{v(cv.location, DEMO.location)}</span>
                {#if hasVal(cv.website)}<span>{cv.website}</span>{/if}
                <span style="opacity:{hasVal(cv.github)?1:.4}">{v(cv.github, DEMO.github)}</span>
              </div>
            </div>
          </div>
          <div class="tl-body">
            <div class="tl-sec">
              <div class="tl-sec-title" style="color:{colorHex}">O mně</div>
              <p class="tl-text" style="opacity:{hasVal(cv.summary)?1:.4}">{v(cv.summary, DEMO.summary)}</p>
            </div>
            <div class="tl-sec">
              <div class="tl-sec-title" style="color:{colorHex}">Zkušenosti & Vzdělání</div>
              <div class="tl-track">
                {#each showExp as exp}
                  <div class="tl-item" style="opacity:{isDemoExp?0.4:1}">
                    <div class="tl-dot-wrap">
                      <div class="tl-dot" style="background:{colorHex}"></div>
                      <div class="tl-line" style="background:{colorHex}22"></div>
                    </div>
                    <div class="tl-item-body">
                      <div class="tl-date">{exp.from}{exp.to ? ` – ${exp.to}` : ' – současnost'}</div>
                      <div class="tl-item-title">{exp.role}</div>
                      <div class="tl-item-sub">{exp.company}</div>
                      {#if exp.desc}<p class="tl-desc">{exp.desc}</p>{/if}
                    </div>
                  </div>
                {/each}
                {#each showEdu as edu}
                  <div class="tl-item" style="opacity:{isDemoEdu?0.4:1}">
                    <div class="tl-dot-wrap">
                      <div class="tl-dot tl-dot-edu" style="border-color:{colorHex}"></div>
                      <div class="tl-line" style="background:{colorHex}22"></div>
                    </div>
                    <div class="tl-item-body">
                      {#if edu.from}<div class="tl-date">{edu.from}{edu.to ? ` – ${edu.to}` : ''}</div>{/if}
                      <div class="tl-item-title">{edu.school}</div>
                      <div class="tl-item-sub">{edu.field}{edu.note ? ` · ${edu.note}` : ''}</div>
                    </div>
                  </div>
                {/each}
              </div>
            </div>
            <div class="tl-bottom">
              <div class="tl-bot-col">
                <div class="tl-sec-title" style="color:{colorHex}">Dovednosti</div>
                {#each showSkills as s}
                  <div class="tl-skill" style="opacity:{isDemoSkills?0.4:1}">
                    <span>{s.name}</span>
                    <div class="tl-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                  </div>
                {/each}
              </div>
              <div class="tl-bot-col">
                <div class="tl-sec-title" style="color:{colorHex}">Jazyky</div>
                {#each showLangs as l}
                  <div class="tl-lang" style="opacity:{isDemoLangs?0.4:1}"><strong>{l.name}</strong><span>{l.level}</span></div>
                {/each}
              </div>
            </div>
          </div>
        </div>
      {/if}

      <!-- ── EXECUTIVE ── -->
      {#if template === 'executive'}
        <div class="cv">
          <div class="ex-head">
            <div class="ex-name" style="opacity:{hasVal(fullName)?1:.4}">{v(fullName, DEMO.name)}</div>
            <div class="ex-role" style="color:{colorHex};opacity:{hasVal(cv.role)?1:.4}">{v(cv.role, DEMO.role)}</div>
            <div class="ex-rule" style="background:{colorHex}"></div>
            <div class="ex-contacts">
              <span style="opacity:{hasVal(cv.email)?1:.4}">{v(cv.email, DEMO.email)}</span>
              <span class="ex-sep">·</span><span style="opacity:{hasVal(cv.phone)?1:.4}">{v(cv.phone, DEMO.phone)}</span>
              <span class="ex-sep">·</span><span style="opacity:{hasVal(cv.location)?1:.4}">{v(cv.location, DEMO.location)}</span>
              {#if hasVal(cv.website)}<span class="ex-sep">·</span><span>{cv.website}</span>{/if}
              <span class="ex-sep">·</span><span style="opacity:{hasVal(cv.github)?1:.4}">{v(cv.github, DEMO.github)}</span>
            </div>
          </div>
          <div class="ex-body">
            <div class="ex-sec">
              <div class="ex-title" style="color:{colorHex}">Profil</div>
              <p class="ex-text" style="opacity:{hasVal(cv.summary)?1:.4}">{v(cv.summary, DEMO.summary)}</p>
            </div>
            <div class="ex-cols">
              <div class="ex-main">
                <div class="ex-sec">
                  <div class="ex-title" style="color:{colorHex}">Pracovní zkušenosti</div>
                  {#each showExp as exp}
                    <div class="ex-entry" style="opacity:{isDemoExp?0.4:1}">
                      <div class="ex-entry-head">
                        <strong>{exp.role}</strong>
                        <span class="ex-date">{exp.from}{exp.to ? ` – ${exp.to}` : ' – současnost'}</span>
                      </div>
                      <div class="ex-sub">{exp.company}</div>
                      {#if exp.desc}<p class="ex-desc">{exp.desc}</p>{/if}
                    </div>
                  {/each}
                </div>
                <div class="ex-sec">
                  <div class="ex-title" style="color:{colorHex}">Vzdělání</div>
                  {#each showEdu as edu}
                    <div class="ex-entry" style="opacity:{isDemoEdu?0.4:1}">
                      <div class="ex-entry-head">
                        <strong>{edu.school}</strong>
                        {#if edu.from}<span class="ex-date">{edu.from}{edu.to ? ` – ${edu.to}` : ''}</span>{/if}
                      </div>
                      <div class="ex-sub">{edu.field}{edu.note ? ` · ${edu.note}` : ''}</div>
                    </div>
                  {/each}
                </div>
              </div>
              <div class="ex-side">
                <div class="ex-sec">
                  <div class="ex-title" style="color:{colorHex}">Dovednosti</div>
                  {#each showSkills as s}
                    <div class="ex-skill" style="opacity:{isDemoSkills?0.4:1}">
                      <span>{s.name}</span>
                      <div class="ex-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                    </div>
                  {/each}
                </div>
                <div class="ex-sec">
                  <div class="ex-title" style="color:{colorHex}">Jazyky</div>
                  {#each showLangs as l}
                    <div class="ex-lang" style="opacity:{isDemoLangs?0.4:1}"><strong>{l.name}</strong><span>{l.level}</span></div>
                  {/each}
                </div>
                {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
                  <div class="ex-sec">
                    <div class="ex-title" style="color:{colorHex}">Certifikáty</div>
                    {#each cv.certs as cert}{#if hasVal(cert.name)}
                      <div class="ex-cert"><strong>{cert.name}</strong>{#if hasVal(cert.year)}<span>{cert.year}</span>{/if}</div>
                    {/if}{/each}
                  </div>
                {/if}
              </div>
            </div>
          </div>
        </div>
      {/if}

    </div>
  </main>

  <!-- ══ MOBILE TAB BAR ══ -->
  <div class="mob-tabbar">
    <button class="mob-tab" class:on={mobileTab === 'edit'} onclick={() => mobileTab = 'edit'}>
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
      <span>Editovat</span>
    </button>
    <button class="mob-tab" class:on={mobileTab === 'preview'} onclick={() => mobileTab = 'preview'}>
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/><circle cx="12" cy="12" r="3"/></svg>
      <span>Náhled</span>
    </button>
  </div>

</div>

<style>
  :global(*, *::before, *::after) { margin:0; padding:0; box-sizing:border-box; }
  :global(html) { overflow:hidden; height:100%; background:#0f0f0f; }
  :global(body) { overflow:hidden; height:100%; font-family:'Inter',sans-serif; background:#0f0f0f; }

  .builder { display:grid; grid-template-columns:460px 1fr; height:100vh; max-width:100vw; overflow:hidden; }

  /* ── PANEL ── */
  .panel { background:#141414; border-right:1px solid rgba(255,255,255,.07); overflow-y:auto; overflow-x:hidden; display:flex; flex-direction:column; }
  .panel::-webkit-scrollbar { width:3px; }
  .panel::-webkit-scrollbar-thumb { background:rgba(255,255,255,.12); border-radius:2px; }

  .panel-head { padding:16px 22px; border-bottom:1px solid rgba(255,255,255,.07); display:flex; align-items:center; gap:10px; position:sticky; top:0; background:#141414; z-index:10; }
  .back { font-size:11px; font-weight:600; color:rgba(255,255,255,.3); text-decoration:none; transition:color .2s; }
  .back:hover { color:rgba(255,255,255,.7); }
  .panel-title { font-size:15px; font-weight:800; color:#fff; flex:1; font-family:'Bricolage Grotesque',sans-serif; }
  .pdf-btn { font-size:11px; font-weight:700; padding:6px 13px; background:#fff; color:#111; border:none; cursor:pointer; border-radius:3px; transition:opacity .15s; }
  .pdf-btn:hover { opacity:.85; }

  .controls { border-bottom:1px solid rgba(255,255,255,.07); padding:12px 22px; display:flex; flex-direction:column; gap:10px; }
  .ctrl-group { display:flex; align-items:center; gap:12px; }
  .ctrl-lbl { font-size:9px; font-weight:700; letter-spacing:2px; text-transform:uppercase; color:rgba(255,255,255,.28); min-width:50px; }
  .ctrl-row { display:flex; gap:5px; align-items:center; flex-wrap:wrap; }

  .tpl { font-size:11px; font-weight:600; padding:5px 11px; background:rgba(255,255,255,.05); color:rgba(255,255,255,.38); border:1px solid rgba(255,255,255,.09); cursor:pointer; transition:all .15s; border-radius:3px; }
  .tpl.on { background:#fff; color:#111; border-color:#fff; }
  .tpl:hover:not(.on) { color:rgba(255,255,255,.7); background:rgba(255,255,255,.09); }

  .clr { width:20px; height:20px; border-radius:50%; border:none; cursor:pointer; transition:transform .15s; outline:2px solid transparent; outline-offset:2px; }
  .clr.on { outline:2px solid rgba(255,255,255,.6); }
  .clr:hover { transform:scale(1.15); }

  .tabs { display:flex; flex-wrap:wrap; gap:3px; padding:10px 22px; border-bottom:1px solid rgba(255,255,255,.07); }
  .tab { font-size:11px; font-weight:600; padding:5px 9px; background:transparent; color:rgba(255,255,255,.32); border:1px solid transparent; border-radius:4px; cursor:pointer; transition:all .15s; }
  .tab.on { background:rgba(255,255,255,.09); color:#fff; border-color:rgba(255,255,255,.13); }
  .tab:hover:not(.on) { color:rgba(255,255,255,.6); }

  .form-body { padding:16px 22px; display:flex; flex-direction:column; gap:11px; }
  .hint { font-size:10px; color:rgba(255,255,255,.25); font-style:italic; }
  .r2 { display:grid; grid-template-columns:1fr 1fr; gap:9px; }
  .f { display:flex; flex-direction:column; gap:4px; }
  .f label { font-size:9px; font-weight:700; letter-spacing:1.5px; text-transform:uppercase; color:rgba(255,255,255,.28); display:flex; align-items:center; gap:6px; }
  .opt { font-size:8px; font-weight:400; letter-spacing:.3px; color:rgba(255,255,255,.18); text-transform:none; border:1px solid rgba(255,255,255,.1); padding:1px 5px; border-radius:2px; }
  .f input, .f textarea { background:rgba(255,255,255,.04); border:1px solid rgba(255,255,255,.09); color:#fff; font-family:'Inter',sans-serif; font-size:13px; padding:8px 10px; outline:none; resize:vertical; border-radius:4px; transition:border-color .2s; }
  .f input:focus, .f textarea:focus { border-color:rgba(255,255,255,.35); }
  .f input::placeholder, .f textarea::placeholder { color:rgba(255,255,255,.18); }

  .card { background:rgba(255,255,255,.025); border:1px solid rgba(255,255,255,.06); padding:13px; border-radius:6px; display:flex; flex-direction:column; gap:9px; }
  .card-head { display:flex; justify-content:space-between; align-items:center; }
  .card-head span { font-size:9px; font-weight:700; letter-spacing:1.5px; color:rgba(255,255,255,.3); text-transform:uppercase; }
  .rm { font-size:10px; background:rgba(255,80,80,.1); color:rgba(255,100,100,.7); border:none; width:22px; height:22px; border-radius:50%; cursor:pointer; display:flex; align-items:center; justify-content:center; transition:background .15s; }
  .rm:hover { background:rgba(255,80,80,.25); }
  .rm.sm { width:18px; height:18px; font-size:9px; }
  .add { font-size:12px; font-weight:600; padding:9px; background:transparent; color:rgba(255,255,255,.3); border:1px dashed rgba(255,255,255,.12); border-radius:4px; cursor:pointer; transition:all .2s; }
  .add:hover { background:rgba(255,255,255,.05); color:rgba(255,255,255,.7); border-color:rgba(255,255,255,.25); }

  .skill-row { display:flex; align-items:center; gap:8px; }
  .skill-inp { flex:1; background:rgba(255,255,255,.04); border:1px solid rgba(255,255,255,.09); color:#fff; font-size:13px; padding:8px 10px; outline:none; border-radius:4px; transition:border-color .2s; }
  .skill-inp:focus { border-color:rgba(255,255,255,.35); }
  .skill-inp::placeholder { color:rgba(255,255,255,.18); }
  .dots { display:flex; gap:4px; }
  .dot-btn { width:12px; height:12px; border-radius:50%; background:rgba(255,255,255,.1); border:none; cursor:pointer; transition:background .15s; }
  .dot-btn.on { background:#fff; }

  /* ── PREVIEW ── */
  .preview { background:#181818; overflow-y:auto; overflow-x:hidden; display:flex; justify-content:center; padding:28px; }
  .preview::-webkit-scrollbar { width:4px; }
  .preview::-webkit-scrollbar-thumb { background:rgba(255,255,255,.08); border-radius:2px; }
  .preview-inner { width:100%; max-width:794px; }

  .cv { background:#fff; color:#1a1a1a; font-family:'Inter',sans-serif; font-size:10pt; line-height:1.5; box-shadow:0 12px 48px rgba(0,0,0,.6); min-height:297mm; overflow:hidden; }

  /* ── SKELETON ── */
  .sk { background:#e8e8e8; border-radius:3px; display:block; flex-shrink:0; }
  .sk-block { display:flex; flex-direction:column; gap:6px; }
  .sk-name { width:70%; height:14px; }
  .sk-role { width:42%; height:9px; margin-top:6px; }
  .sk-contact { width:100%; height:8px; }
  .sk-cl-name { width:65%; height:18px; border-radius:3px; background:rgba(255,255,255,.25); }
  .sk-cl-role { width:40%; height:9px; margin-top:5px; border-radius:3px; background:rgba(255,255,255,.2); }
  .sk-cl-c { width:110px; height:8px; border-radius:2px; background:rgba(255,255,255,.2); }

  /* ── MODERN ── */
  .mod-cv { display:grid; grid-template-columns:210px 1fr; }
  .mod-sidebar { padding:26px 20px; color:#fff; display:flex; flex-direction:column; }
  .mod-name { font-family:'Bricolage Grotesque',sans-serif; font-size:16pt; font-weight:800; line-height:1.1; word-break:break-word; }
  .mod-role { font-size:8pt; opacity:.7; margin-top:5px; font-weight:500; }
  .mod-divider { height:1px; background:rgba(255,255,255,.18); margin:14px 0; }
  .mod-sec-title { font-size:6.5pt; font-weight:700; letter-spacing:2.5px; text-transform:uppercase; opacity:.55; margin-bottom:9px; }
  .mod-contacts { display:flex; flex-direction:column; gap:6px; }
  .mod-ci { font-size:7.5pt; display:flex; align-items:flex-start; gap:5px; word-break:break-all; line-height:1.35; opacity:.85; }
  .mod-skill { margin-bottom:7px; }
  .mod-skill-name { font-size:7.5pt; display:block; margin-bottom:4px; opacity:.85; }
  .mod-skill-bar { height:3px; background:rgba(255,255,255,.18); border-radius:2px; overflow:hidden; }
  .mod-skill-fill { height:100%; background:#fff; border-radius:2px; }
  .mod-lang { display:flex; justify-content:space-between; margin-bottom:5px; font-size:7.5pt; opacity:.85; }
  .mod-lang-n { font-weight:600; }
  .mod-lang-l { opacity:.7; }
  .mod-cert-item { margin-bottom:7px; }
  .mod-cert-n { font-size:7.5pt; font-weight:600; }
  .mod-cert-s { font-size:7pt; opacity:.6; margin-top:1px; }

  .mod-main { padding:26px 24px; }
  .mod-sec { margin-bottom:18px; }
  .mod-title { font-family:'Bricolage Grotesque',sans-serif; font-size:7pt; font-weight:800; letter-spacing:2px; text-transform:uppercase; padding-bottom:5px; border-bottom:1px solid #eee; margin-bottom:11px; }
  .mod-text { font-size:9pt; line-height:1.7; color:#444; }
  .mod-entry { display:flex; gap:9px; margin-bottom:12px; align-items:flex-start; }
  .mod-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; margin-top:4px; }
  .mod-entry-body { flex:1; min-width:0; }
  .mod-entry-row { display:flex; justify-content:space-between; align-items:baseline; gap:8px; }
  .mod-entry-row strong { font-size:9.5pt; font-weight:700; }
  .mod-date { font-size:7.5pt; color:#999; white-space:nowrap; }
  .mod-company { font-size:8pt; color:#666; margin:2px 0 4px; }
  .mod-desc { font-size:8.5pt; color:#555; line-height:1.6; }

  /* ── MINIMAL ── */
  .n-head { display:flex; justify-content:space-between; align-items:flex-start; padding:34px 42px 12px; gap:20px; }
  .n-name { font-family:'Bricolage Grotesque',sans-serif; font-size:25pt; font-weight:800; letter-spacing:-1px; word-break:break-word; }
  .n-role { font-size:10pt; font-weight:500; margin-top:4px; }
  .n-contacts { display:flex; flex-direction:column; gap:4px; align-items:flex-end; font-size:8pt; color:#666; word-break:break-all; max-width:180px; }
  .n-line { height:2px; margin:0 42px 12px; }
  .n-summary { font-size:9.5pt; line-height:1.7; margin:0 42px 18px; color:#333; }
  .n-sec { margin:0 42px 16px; }
  .n-title { font-size:7.5pt; font-weight:700; letter-spacing:3px; text-transform:uppercase; margin-bottom:10px; }
  .n-entry { margin-bottom:11px; padding-bottom:11px; border-bottom:1px solid #f0f0f0; }
  .n-entry:last-child { border-bottom:none; padding-bottom:0; }
  .n-entry-row { display:flex; justify-content:space-between; align-items:baseline; gap:8px; }
  .n-entry-row strong { font-size:9.5pt; font-weight:700; }
  .n-entry-row span { font-size:8pt; color:#999; white-space:nowrap; }
  .n-sub { font-size:8.5pt; color:#777; margin-top:2px; }
  .n-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .n-bottom { display:flex; gap:22px; flex-wrap:wrap; margin:0 42px; padding:14px 0; border-top:1px solid #eee; }
  .n-bot-col { flex:1; min-width:130px; }
  .n-tags { display:flex; flex-wrap:wrap; gap:5px; }
  .n-tag { font-size:7.5pt; padding:2px 8px; border:1px solid; border-radius:2px; font-weight:500; }
  .n-lang-item { font-size:9pt; color:#444; margin-bottom:5px; }

  /* ── CLASSIC ── */
  .cl-head { padding:24px 30px; color:#fff; display:flex; justify-content:space-between; align-items:flex-start; gap:20px; }
  .cl-head-left { flex:1; min-width:0; }
  .cl-name { font-family:'Lora',serif; font-size:22pt; font-weight:600; word-break:break-word; }
  .cl-role { font-size:10pt; opacity:.82; margin:4px 0 0; }
  .cl-contacts { display:flex; flex-direction:column; gap:5px; text-align:right; font-size:7.5pt; opacity:.88; word-break:break-all; max-width:185px; }
  .cl-c { line-height:1.3; }
  .cl-body { display:grid; grid-template-columns:1fr 170px; }
  .cl-main { padding:20px 22px 20px 30px; border-right:1px solid #eee; }
  .cl-side { padding:20px 16px; background:#f8f8f8; }
  .cl-sec { margin-bottom:16px; }
  .cl-title { font-size:7.5pt; font-weight:700; letter-spacing:2px; text-transform:uppercase; margin-bottom:9px; padding-bottom:5px; }
  .cl-text { font-size:9.5pt; color:#444; line-height:1.7; }
  .cl-entry { display:flex; gap:9px; margin-bottom:11px; align-items:flex-start; }
  .cl-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; margin-top:5px; }
  .cl-eh { font-size:9.5pt; font-weight:700; }
  .cl-eh em { font-style:normal; font-weight:400; color:#666; }
  .cl-date { font-size:8pt; color:#999; margin:2px 0; }
  .cl-desc { font-size:9pt; color:#555; line-height:1.6; margin-top:3px; }
  .cl-skill { margin-bottom:8px; font-size:9pt; }
  .cl-skill span { display:block; font-weight:500; color:#333; margin-bottom:3px; }
  .cl-bar { height:3px; background:#e0e0e0; border-radius:2px; overflow:hidden; }
  .cl-bar div { height:100%; border-radius:2px; }
  .cl-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:6px; align-items:baseline; }
  .cl-lang strong { color:#222; }
  .cl-lang span { color:#777; font-size:8pt; }
  .cl-cert { margin-bottom:7px; }
  .cl-cert strong { display:block; font-size:9pt; color:#222; }
  .cl-cert span { font-size:8pt; color:#777; }

  /* ── TIMELINE ── */
  .tl-head { display:flex; align-items:stretch; }
  .tl-accent { width:5px; flex-shrink:0; }
  .tl-head-content { flex:1; padding:28px 32px 20px; }
  .tl-name { font-family:'Bricolage Grotesque',sans-serif; font-size:24pt; font-weight:800; letter-spacing:-1px; word-break:break-word; }
  .tl-role { font-size:10.5pt; font-weight:500; margin-top:5px; }
  .tl-contacts { display:flex; flex-wrap:wrap; gap:10px 18px; margin-top:10px; font-size:8pt; color:#666; }
  .tl-body { padding:20px 32px; }
  .tl-sec { margin-bottom:18px; }
  .tl-sec-title { font-size:7.5pt; font-weight:700; letter-spacing:2.5px; text-transform:uppercase; margin-bottom:12px; }
  .tl-text { font-size:9.5pt; line-height:1.7; color:#333; }
  .tl-track { display:flex; flex-direction:column; }
  .tl-item { display:flex; gap:14px; }
  .tl-dot-wrap { display:flex; flex-direction:column; align-items:center; flex-shrink:0; width:16px; }
  .tl-dot { width:12px; height:12px; border-radius:50%; flex-shrink:0; margin-top:2px; }
  .tl-dot-edu { background:transparent !important; border:2px solid; }
  .tl-line { width:2px; flex:1; min-height:16px; margin:4px 0; }
  .tl-item-body { flex:1; padding-bottom:16px; min-width:0; }
  .tl-date { font-size:7.5pt; color:#999; margin-bottom:3px; }
  .tl-item-title { font-size:9.5pt; font-weight:700; }
  .tl-item-sub { font-size:8.5pt; color:#666; margin-top:2px; }
  .tl-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .tl-bottom { display:grid; grid-template-columns:1fr 1fr; gap:20px; margin-top:4px; padding-top:16px; border-top:1px solid #eee; }
  .tl-bot-col { }
  .tl-skill { margin-bottom:8px; font-size:9pt; display:flex; flex-direction:column; gap:3px; }
  .tl-skill span { font-weight:500; color:#333; }
  .tl-bar { height:3px; background:#eee; border-radius:2px; overflow:hidden; }
  .tl-bar div { height:100%; border-radius:2px; }
  .tl-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:6px; }
  .tl-lang strong { color:#222; }
  .tl-lang span { color:#777; font-size:8pt; }

  /* ── EXECUTIVE ── */
  .ex-head { text-align:center; padding:32px 40px 16px; border-bottom:1px solid #eee; }
  .ex-name { font-family:'DM Serif Display',serif; font-size:26pt; font-weight:400; letter-spacing:.5px; word-break:break-word; }
  .ex-role { font-size:10pt; font-weight:500; margin-top:5px; letter-spacing:.5px; }
  .ex-rule { height:2px; width:60px; margin:14px auto; border-radius:1px; }
  .ex-contacts { display:flex; flex-wrap:wrap; justify-content:center; align-items:center; gap:6px 0; font-size:8pt; color:#666; }
  .ex-sep { margin:0 8px; color:#ccc; }
  .ex-body { padding:20px 36px; }
  .ex-sec { margin-bottom:16px; }
  .ex-title { font-size:7.5pt; font-weight:700; letter-spacing:2.5px; text-transform:uppercase; border-bottom:1px solid #eee; padding-bottom:5px; margin-bottom:10px; }
  .ex-text { font-size:9.5pt; color:#444; line-height:1.7; }
  .ex-cols { display:grid; grid-template-columns:1fr 170px; gap:24px; }
  .ex-entry { margin-bottom:13px; }
  .ex-entry-head { display:flex; justify-content:space-between; align-items:baseline; gap:8px; }
  .ex-entry-head strong { font-size:9.5pt; font-weight:700; }
  .ex-date { font-size:8pt; color:#999; white-space:nowrap; }
  .ex-sub { font-size:8.5pt; color:#666; margin-top:2px; font-style:italic; }
  .ex-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .ex-skill { margin-bottom:8px; font-size:9pt; }
  .ex-skill span { display:block; font-weight:500; color:#333; margin-bottom:3px; }
  .ex-bar { height:3px; background:#eee; border-radius:2px; overflow:hidden; }
  .ex-bar div { height:100%; border-radius:2px; }
  .ex-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:6px; }
  .ex-lang strong { color:#222; }
  .ex-lang span { color:#777; font-size:8pt; }
  .ex-cert { margin-bottom:7px; }
  .ex-cert strong { display:block; font-size:9pt; color:#222; }
  .ex-cert span { font-size:8pt; color:#777; }
  .ex-side { border-left:1px solid #eee; padding-left:20px; }


  /* ── MOBILE TAB BAR ── */
  .mob-tabbar { display:none; }

  @media (max-width:900px) {
    :global(html) { overflow:auto !important; height:auto !important; }
    :global(body) { overflow:auto !important; height:auto !important; }

    .builder {
      grid-template-columns: 1fr;
      height: auto;
      min-height: 100dvh;
      padding-bottom: 60px;
    }

    .mob-tabbar {
      display: flex;
      position: fixed;
      bottom: 0; left: 0; right: 0;
      height: 60px;
      background: #141414;
      border-top: 1px solid rgba(255,255,255,.07);
      z-index: 200;
    }

    .mob-tab {
      flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center;
      gap: 4px; background: none; border: none; cursor: pointer;
      color: rgba(255,255,255,.35); font-family: 'Inter', sans-serif; font-size: 11px;
      font-weight: 500; letter-spacing: .3px; transition: color .2s;
    }
    .mob-tab.on { color: #fff; }
    .mob-tab svg { stroke: currentColor; }

    .panel { height: auto; min-height: calc(100dvh - 60px); overflow-y: auto; }
    .preview { padding: 16px; height: auto; min-height: calc(100dvh - 60px); overflow-y: auto; align-items: flex-start; }
    .preview-inner { width: 820px; }

    .mob-hidden { display: none !important; }

    .panel-head { position: sticky; top: 0; }
  }

  /* ── PRINT ── */
  @media print {
    /* margin:0 → no space for browser URL/title headers */
    @page { size: A4; margin: 0; }

    :global(html, body) {
      overflow: visible !important;
      background: white !important;
      -webkit-print-color-adjust: exact !important;
      print-color-adjust: exact !important;
    }

    :global(#form-panel)  { display: none !important; }
    :global(.mob-tabbar)  { display: none !important; }

    :global(.builder) {
      display: block !important;
      height: auto !important;
      overflow: visible !important;
    }

    :global(.preview) {
      display: block !important;
      padding: 0 !important;
      overflow: visible !important;
      background: white !important;
      height: auto !important;
      min-height: auto !important;
    }

    /* Viewport ~794px (A4 width) triggers max-width:900px media query
       which adds display:none!important via .mob-hidden — override with higher specificity */
    :global(.preview.mob-hidden) {
      display: block !important;
    }

    :global(.preview-inner) {
      max-width: 100% !important;
      width: 100% !important;
      margin: 0 !important;
      height: auto !important;
    }

    :global(.cv) {
      box-shadow: none !important;
      min-height: auto !important;
      height: auto !important;
      width: 100% !important;
      overflow: visible !important;
      /* Prevent spurious blank page after content */
      page-break-after: avoid;
      break-after: avoid;
    }

    /* Force background colors to print on colored sections */
    :global(.mod-sidebar),
    :global(.cl-head),
    :global(.cl-side),
    :global(.tl-accent) {
      -webkit-print-color-adjust: exact !important;
      print-color-adjust: exact !important;
    }

    :global(.sk)       { display: none !important; }
    :global(.sk-block) { display: none !important; }
  }
</style>
