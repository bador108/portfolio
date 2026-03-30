<script>
  const TEMPLATES = [
    { id: 'modern',  label: 'Moderní' },
    { id: 'minimal', label: 'Minimalistická' },
    { id: 'classic', label: 'Klasická' },
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
    firstName: 'Jan',
    lastName: 'Novák',
    role: 'Frontend Developer',
    email: 'jan.novak@email.cz',
    phone: '605 123 456',
    location: 'Praha',
    website: '',
    github: 'github.com/jannovak',
    summary: 'Frontend developer s vášní pro čistý kód a skvělé uživatelské zážitky. Specializuji se na moderní JavaScript frameworky a rád pracuji na projektech, které mají smysl.',
    experience: [
      { company: 'Startup s.r.o.', role: 'Frontend Developer', from: '2023', to: 'současnost', desc: 'Vývoj uživatelského rozhraní v Reactu a TypeScriptu. Optimalizace výkonu, code review, spolupráce s designery.' },
      { company: 'Webová agentura XY', role: 'Junior Developer', from: '2021', to: '2023', desc: 'Tvorba webových stránek a e-shopů — HTML, CSS, JavaScript, WordPress.' },
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
    certs: [],
  });

  let template = $state('modern');
  let colorHex = $state('#2563eb');
  let activeSection = $state('personal');

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

  function exportPDF() { window.print(); }

  function hasVal(v) { return v && v.trim() !== ''; }
  const fullName = $derived((cv.firstName + ' ' + cv.lastName).trim());
</script>

<svelte:head>
  <title>CV Builder</title>
  <link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,400;12..96,600;12..96,700;12..96,800&family=Inter:wght@300;400;500;600&family=Lora:ital,wght@0,400;0,600;1,400&display=swap" rel="stylesheet" />
</svelte:head>

<div class="builder">

  <!-- ══ SIDEBAR ══ -->
  <aside class="panel" id="form-panel">
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
            <div class="f"><label>Popis <span class="opt">volitelné</span></label><textarea bind:value={exp.desc} rows="3" placeholder="Co jsi dělal, čeho dosáhl..."></textarea></div>
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
  <main class="preview">
    <div class="preview-inner">

      <!-- ── MODERN: barevný levý sidebar ── -->
      {#if template === 'modern'}
        <div class="cv mod-cv">
          <div class="mod-sidebar" style="background:{colorHex}">
            <div class="mod-name">{fullName || 'Tvoje jméno'}</div>
            {#if hasVal(cv.role)}<div class="mod-role">{cv.role}</div>{/if}

            <div class="mod-divider"></div>

            <div class="mod-sec-title">Kontakt</div>
            <div class="mod-contacts">
              {#if hasVal(cv.email)}<div class="mod-contact-item"><span class="mod-icon">✉</span>{cv.email}</div>{/if}
              {#if hasVal(cv.phone)}<div class="mod-contact-item"><span class="mod-icon">📞</span>{cv.phone}</div>{/if}
              {#if hasVal(cv.location)}<div class="mod-contact-item"><span class="mod-icon">📍</span>{cv.location}</div>{/if}
              {#if hasVal(cv.website)}<div class="mod-contact-item"><span class="mod-icon">🌐</span>{cv.website}</div>{/if}
              {#if hasVal(cv.github)}<div class="mod-contact-item"><span class="mod-icon">⌥</span>{cv.github}</div>{/if}
            </div>

            {#if cv.skills.filter(s => hasVal(s.name)).length > 0}
              <div class="mod-divider"></div>
              <div class="mod-sec-title">Dovednosti</div>
              {#each cv.skills as s}
                {#if hasVal(s.name)}
                  <div class="mod-skill">
                    <span class="mod-skill-name">{s.name}</span>
                    <div class="mod-skill-bar">
                      <div class="mod-skill-fill" style="width:{s.level * 20}%"></div>
                    </div>
                  </div>
                {/if}
              {/each}
            {/if}

            {#if cv.languages.filter(l => hasVal(l.name)).length > 0}
              <div class="mod-divider"></div>
              <div class="mod-sec-title">Jazyky</div>
              {#each cv.languages as l}
                {#if hasVal(l.name)}
                  <div class="mod-lang">
                    <span class="mod-lang-name">{l.name}</span>
                    {#if hasVal(l.level)}<span class="mod-lang-level">{l.level}</span>{/if}
                  </div>
                {/if}
              {/each}
            {/if}

            {#if cv.certs.filter(c => hasVal(c.name)).length > 0}
              <div class="mod-divider"></div>
              <div class="mod-sec-title">Certifikáty</div>
              {#each cv.certs as c}
                {#if hasVal(c.name)}
                  <div class="mod-cert">
                    <div class="mod-cert-name">{c.name}</div>
                    {#if hasVal(c.issuer) || hasVal(c.year)}<div class="mod-cert-sub">{c.issuer}{c.year ? ` · ${c.year}` : ''}</div>{/if}
                  </div>
                {/if}
              {/each}
            {/if}
          </div>

          <div class="mod-main">
            {#if hasVal(cv.summary)}
              <div class="mod-main-sec">
                <div class="mod-main-title" style="color:{colorHex}">O mně</div>
                <p class="mod-text">{cv.summary}</p>
              </div>
            {/if}

            {#if cv.experience.length > 0}
              <div class="mod-main-sec">
                <div class="mod-main-title" style="color:{colorHex}">Pracovní zkušenosti</div>
                {#each cv.experience as exp}
                  <div class="mod-entry">
                    <div class="mod-entry-dot" style="background:{colorHex}"></div>
                    <div class="mod-entry-body">
                      <div class="mod-entry-row">
                        <strong>{exp.role || 'Pozice'}</strong>
                        {#if hasVal(exp.from)}<span class="mod-date">{exp.from}{hasVal(exp.to) ? ` – ${exp.to}` : ' – současnost'}</span>{/if}
                      </div>
                      {#if hasVal(exp.company)}<div class="mod-entry-company">{exp.company}</div>{/if}
                      {#if hasVal(exp.desc)}<p class="mod-entry-desc">{exp.desc}</p>{/if}
                    </div>
                  </div>
                {/each}
              </div>
            {/if}

            {#if cv.education.length > 0}
              <div class="mod-main-sec">
                <div class="mod-main-title" style="color:{colorHex}">Vzdělání</div>
                {#each cv.education as edu}
                  <div class="mod-entry">
                    <div class="mod-entry-dot" style="background:{colorHex}"></div>
                    <div class="mod-entry-body">
                      <div class="mod-entry-row">
                        <strong>{edu.school || 'Škola'}</strong>
                        {#if hasVal(edu.from)}<span class="mod-date">{edu.from}{hasVal(edu.to) ? ` – ${edu.to}` : ''}</span>{/if}
                      </div>
                      {#if hasVal(edu.field)}<div class="mod-entry-company">{edu.field}{hasVal(edu.note) ? ` · ${edu.note}` : ''}</div>{/if}
                    </div>
                  </div>
                {/each}
              </div>
            {/if}
          </div>
        </div>
      {/if}

      <!-- ── MINIMAL ── -->
      {#if template === 'minimal'}
        <div class="cv">
          <div class="n-head">
            <div>
              <div class="n-name">{fullName || 'Tvoje jméno'}</div>
              {#if hasVal(cv.role)}<div class="n-role" style="color:{colorHex}">{cv.role}</div>{/if}
            </div>
            <div class="n-contacts">
              {#if hasVal(cv.email)}<span>{cv.email}</span>{/if}
              {#if hasVal(cv.phone)}<span>{cv.phone}</span>{/if}
              {#if hasVal(cv.location)}<span>{cv.location}</span>{/if}
              {#if hasVal(cv.website)}<span>{cv.website}</span>{/if}
              {#if hasVal(cv.github)}<span>{cv.github}</span>{/if}
            </div>
          </div>
          <div class="n-line" style="background:{colorHex}"></div>

          {#if hasVal(cv.summary)}<p class="n-summary">{cv.summary}</p>{/if}

          {#if cv.experience.length > 0}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">Pracovní zkušenosti</div>
              {#each cv.experience as exp}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong>{exp.role || 'Pozice'}</strong>
                    {#if hasVal(exp.from)}<span>{exp.from}{hasVal(exp.to) ? ` – ${exp.to}` : ''}</span>{/if}
                  </div>
                  {#if hasVal(exp.company)}<div class="n-sub">{exp.company}</div>{/if}
                  {#if hasVal(exp.desc)}<p class="n-desc">{exp.desc}</p>{/if}
                </div>
              {/each}
            </div>
          {/if}

          {#if cv.education.length > 0}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">Vzdělání</div>
              {#each cv.education as edu}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong>{edu.school || 'Škola'}</strong>
                    {#if hasVal(edu.from)}<span>{edu.from}{hasVal(edu.to) ? ` – ${edu.to}` : ''}</span>{/if}
                  </div>
                  {#if hasVal(edu.field)}<div class="n-sub">{edu.field}{hasVal(edu.note) ? ` · ${edu.note}` : ''}</div>{/if}
                </div>
              {/each}
            </div>
          {/if}

          {#if cv.skills.filter(s=>hasVal(s.name)).length > 0 || cv.languages.filter(l=>hasVal(l.name)).length > 0 || cv.certs.filter(c=>hasVal(c.name)).length > 0}
            <div class="n-bottom">
              {#if cv.skills.filter(s=>hasVal(s.name)).length > 0}
                <div class="n-bot-col">
                  <div class="n-title" style="color:{colorHex}">Dovednosti</div>
                  <div class="n-tags">
                    {#each cv.skills as s}{#if hasVal(s.name)}<span class="n-tag" style="border-color:{colorHex};color:{colorHex}">{s.name}</span>{/if}{/each}
                  </div>
                </div>
              {/if}
              {#if cv.languages.filter(l=>hasVal(l.name)).length > 0}
                <div class="n-bot-col">
                  <div class="n-title" style="color:{colorHex}">Jazyky</div>
                  {#each cv.languages as l}{#if hasVal(l.name)}<div class="n-lang-item"><strong>{l.name}</strong>{hasVal(l.level) ? ` — ${l.level}` : ''}</div>{/if}{/each}
                </div>
              {/if}
              {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
                <div class="n-bot-col">
                  <div class="n-title" style="color:{colorHex}">Certifikáty</div>
                  {#each cv.certs as c}{#if hasVal(c.name)}<div class="n-lang-item"><strong>{c.name}</strong>{hasVal(c.year) ? ` (${c.year})` : ''}</div>{/if}{/each}
                </div>
              {/if}
            </div>
          {/if}
        </div>
      {/if}

      <!-- ── CLASSIC ── -->
      {#if template === 'classic'}
        <div class="cv">
          <div class="cl-head" style="background:{colorHex}">
            <div class="cl-head-left">
              <div class="cl-name">{fullName || 'Tvoje jméno'}</div>
              {#if hasVal(cv.role)}<div class="cl-role">{cv.role}</div>{/if}
            </div>
            <div class="cl-contacts">
              {#if hasVal(cv.email)}<div class="cl-contact">✉ {cv.email}</div>{/if}
              {#if hasVal(cv.phone)}<div class="cl-contact">📞 {cv.phone}</div>{/if}
              {#if hasVal(cv.location)}<div class="cl-contact">📍 {cv.location}</div>{/if}
              {#if hasVal(cv.website)}<div class="cl-contact">🌐 {cv.website}</div>{/if}
              {#if hasVal(cv.github)}<div class="cl-contact">⌥ {cv.github}</div>{/if}
            </div>
          </div>

          <div class="cl-body">
            <div class="cl-main">
              {#if hasVal(cv.summary)}
                <div class="cl-sec">
                  <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Profil</div>
                  <p class="cl-text">{cv.summary}</p>
                </div>
              {/if}
              {#if cv.experience.length > 0}
                <div class="cl-sec">
                  <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Pracovní zkušenosti</div>
                  {#each cv.experience as exp}
                    <div class="cl-entry">
                      <div class="cl-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="cl-entry-head">
                          {#if hasVal(exp.role)}<strong>{exp.role}</strong>{/if}
                          {#if hasVal(exp.company)}<em>{hasVal(exp.role) ? ' · ' : ''}{exp.company}</em>{/if}
                        </div>
                        {#if hasVal(exp.from)}<div class="cl-date">{exp.from}{hasVal(exp.to) ? ` – ${exp.to}` : ' – současnost'}</div>{/if}
                        {#if hasVal(exp.desc)}<p class="cl-desc">{exp.desc}</p>{/if}
                      </div>
                    </div>
                  {/each}
                </div>
              {/if}
              {#if cv.education.length > 0}
                <div class="cl-sec">
                  <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Vzdělání</div>
                  {#each cv.education as edu}
                    <div class="cl-entry">
                      <div class="cl-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="cl-entry-head"><strong>{edu.school || 'Škola'}</strong></div>
                        <div class="cl-date">{#if hasVal(edu.field)}{edu.field}{/if}{#if hasVal(edu.from)} · {edu.from}{hasVal(edu.to) ? ` – ${edu.to}` : ''}{/if}</div>
                        {#if hasVal(edu.note)}<p class="cl-desc">{edu.note}</p>{/if}
                      </div>
                    </div>
                  {/each}
                </div>
              {/if}
            </div>

            {#if cv.skills.filter(s=>hasVal(s.name)).length > 0 || cv.languages.filter(l=>hasVal(l.name)).length > 0 || cv.certs.filter(c=>hasVal(c.name)).length > 0}
              <div class="cl-side">
                {#if cv.skills.filter(s=>hasVal(s.name)).length > 0}
                  <div class="cl-sec">
                    <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Dovednosti</div>
                    {#each cv.skills as s}{#if hasVal(s.name)}
                      <div class="cl-skill">
                        <span>{s.name}</span>
                        <div class="cl-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                      </div>
                    {/if}{/each}
                  </div>
                {/if}
                {#if cv.languages.filter(l=>hasVal(l.name)).length > 0}
                  <div class="cl-sec">
                    <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Jazyky</div>
                    {#each cv.languages as l}{#if hasVal(l.name)}
                      <div class="cl-lang"><strong>{l.name}</strong>{#if hasVal(l.level)}<span>{l.level}</span>{/if}</div>
                    {/if}{/each}
                  </div>
                {/if}
                {#if cv.certs.filter(c=>hasVal(c.name)).length > 0}
                  <div class="cl-sec">
                    <div class="cl-title" style="color:{colorHex};border-bottom:2px solid {colorHex}">Certifikáty</div>
                    {#each cv.certs as c}{#if hasVal(c.name)}
                      <div class="cl-cert"><strong>{c.name}</strong>{#if hasVal(c.year)}<span>{c.year}</span>{/if}</div>
                    {/if}{/each}
                  </div>
                {/if}
              </div>
            {/if}
          </div>
        </div>
      {/if}

    </div>
  </main>
</div>

<style>
  :global(*, *::before, *::after) { margin:0; padding:0; box-sizing:border-box; }
  :global(html,body) { height:100%; background:#0f0f0f; font-family:'Inter',sans-serif; overflow:hidden; }

  .builder { display:grid; grid-template-columns:460px 1fr; height:100vh; overflow:hidden; }

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

  .tpl { font-size:11px; font-weight:600; padding:5px 12px; background:rgba(255,255,255,.05); color:rgba(255,255,255,.38); border:1px solid rgba(255,255,255,.09); cursor:pointer; transition:all .15s; border-radius:3px; }
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

  /* ── MODERN ── */
  .mod-cv { display:grid; grid-template-columns:220px 1fr; }
  .mod-sidebar { padding:28px 22px; color:#fff; display:flex; flex-direction:column; gap:0; }
  .mod-name { font-family:'Bricolage Grotesque',sans-serif; font-size:17pt; font-weight:800; line-height:1.1; word-break:break-word; }
  .mod-role { font-size:8.5pt; opacity:.75; margin-top:5px; font-weight:500; }
  .mod-divider { height:1px; background:rgba(255,255,255,.2); margin:16px 0; }
  .mod-sec-title { font-size:7pt; font-weight:700; letter-spacing:2.5px; text-transform:uppercase; opacity:.6; margin-bottom:10px; }
  .mod-contacts { display:flex; flex-direction:column; gap:6px; }
  .mod-contact-item { font-size:7.5pt; display:flex; align-items:flex-start; gap:6px; word-break:break-all; line-height:1.3; opacity:.88; }
  .mod-icon { flex-shrink:0; font-size:8pt; }
  .mod-skill { margin-bottom:8px; }
  .mod-skill-name { font-size:8pt; display:block; margin-bottom:4px; opacity:.88; }
  .mod-skill-bar { height:3px; background:rgba(255,255,255,.2); border-radius:2px; overflow:hidden; }
  .mod-skill-fill { height:100%; background:#fff; border-radius:2px; }
  .mod-lang { display:flex; justify-content:space-between; margin-bottom:6px; font-size:8pt; opacity:.88; }
  .mod-lang-name { font-weight:600; }
  .mod-lang-level { opacity:.7; }
  .mod-cert { margin-bottom:8px; }
  .mod-cert-name { font-size:8pt; font-weight:600; }
  .mod-cert-sub { font-size:7.5pt; opacity:.65; margin-top:1px; }

  .mod-main { padding:28px 26px; }
  .mod-main-sec { margin-bottom:20px; }
  .mod-main-title { font-family:'Bricolage Grotesque',sans-serif; font-size:7.5pt; font-weight:800; letter-spacing:2px; text-transform:uppercase; padding-bottom:5px; border-bottom:1px solid #eee; margin-bottom:12px; }
  .mod-text { font-size:9pt; line-height:1.7; color:#444; }
  .mod-entry { display:flex; gap:10px; margin-bottom:13px; align-items:flex-start; }
  .mod-entry-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; margin-top:4px; }
  .mod-entry-body { flex:1; }
  .mod-entry-row { display:flex; justify-content:space-between; align-items:baseline; gap:8px; }
  .mod-entry-row strong { font-size:9.5pt; font-weight:700; }
  .mod-date { font-size:7.5pt; color:#999; white-space:nowrap; }
  .mod-entry-company { font-size:8.5pt; color:#666; margin:2px 0 4px; }
  .mod-entry-desc { font-size:8.5pt; color:#555; line-height:1.6; }

  /* ── MINIMAL ── */
  .n-head { display:flex; justify-content:space-between; align-items:flex-start; padding:36px 44px 14px; gap:20px; }
  .n-name { font-family:'Bricolage Grotesque',sans-serif; font-size:26pt; font-weight:800; letter-spacing:-1px; word-break:break-word; }
  .n-role { font-size:10.5pt; font-weight:500; margin-top:4px; }
  .n-contacts { display:flex; flex-direction:column; gap:3px; text-align:right; font-size:8pt; color:#666; word-break:break-all; max-width:190px; }
  .n-line { height:2px; margin:0 44px 14px; }
  .n-summary { font-size:9.5pt; line-height:1.7; margin:0 44px 20px; color:#333; }
  .n-sec { margin:0 44px 18px; }
  .n-title { font-size:7.5pt; font-weight:700; letter-spacing:3px; text-transform:uppercase; margin-bottom:10px; }
  .n-entry { margin-bottom:12px; padding-bottom:12px; border-bottom:1px solid #f0f0f0; }
  .n-entry:last-child { border-bottom:none; padding-bottom:0; }
  .n-entry-row { display:flex; justify-content:space-between; align-items:baseline; gap:8px; }
  .n-entry-row strong { font-size:9.5pt; font-weight:700; }
  .n-entry-row span { font-size:8pt; color:#999; white-space:nowrap; }
  .n-sub { font-size:8.5pt; color:#777; margin-top:2px; }
  .n-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .n-bottom { display:flex; gap:24px; flex-wrap:wrap; margin:0 44px; padding:16px 0; border-top:1px solid #eee; }
  .n-bot-col { flex:1; min-width:140px; }
  .n-tags { display:flex; flex-wrap:wrap; gap:5px; }
  .n-tag { font-size:7.5pt; padding:2px 9px; border:1px solid; border-radius:2px; font-weight:500; }
  .n-lang-item { font-size:9pt; color:#444; margin-bottom:5px; }

  /* ── CLASSIC ── */
  .cl-head { padding:26px 32px; color:#fff; display:flex; justify-content:space-between; align-items:flex-start; gap:24px; }
  .cl-head-left { flex:1; }
  .cl-name { font-family:'Lora',serif; font-size:22pt; font-weight:600; word-break:break-word; }
  .cl-role { font-size:10.5pt; opacity:.82; margin:5px 0 0; }
  .cl-contacts { display:flex; flex-direction:column; gap:5px; text-align:right; font-size:8pt; opacity:.88; word-break:break-all; max-width:200px; }
  .cl-contact { line-height:1.3; }
  .cl-body { display:grid; grid-template-columns:1fr 175px; }
  .cl-main { padding:22px 24px 22px 32px; border-right:1px solid #eee; }
  .cl-side { padding:22px 18px; background:#f8f8f8; }
  .cl-sec { margin-bottom:18px; }
  .cl-title { font-size:8pt; font-weight:700; letter-spacing:2px; text-transform:uppercase; margin-bottom:9px; padding-bottom:5px; }
  .cl-text { font-size:9.5pt; color:#444; line-height:1.7; }
  .cl-entry { display:flex; gap:10px; margin-bottom:12px; align-items:flex-start; }
  .cl-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; margin-top:5px; }
  .cl-entry-head { font-size:9.5pt; font-weight:700; }
  .cl-entry-head em { font-style:normal; font-weight:400; color:#666; }
  .cl-date { font-size:8pt; color:#999; margin:2px 0; }
  .cl-desc { font-size:9pt; color:#555; line-height:1.6; margin-top:3px; }
  .cl-skill { margin-bottom:9px; font-size:9pt; }
  .cl-skill span { display:block; font-weight:500; color:#333; margin-bottom:3px; }
  .cl-bar { height:3px; background:#e0e0e0; border-radius:2px; overflow:hidden; }
  .cl-bar div { height:100%; border-radius:2px; }
  .cl-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:7px; }
  .cl-lang strong { color:#222; }
  .cl-lang span { color:#777; font-size:8pt; }
  .cl-cert { margin-bottom:8px; }
  .cl-cert strong { display:block; font-size:9pt; color:#222; }
  .cl-cert span { font-size:8pt; color:#777; }

  /* ── PRINT ── */
  @media print {
    :global(body) { background:white !important; overflow:visible !important; }
    :global(#form-panel) { display:none !important; }
    :global(.builder) { display:block !important; height:auto !important; }
    :global(.preview) { display:block !important; padding:0 !important; overflow:visible !important; background:white !important; height:auto !important; }
    :global(.preview-inner) { max-width:100% !important; }
    :global(.cv) { box-shadow:none !important; min-height:auto !important; }
  }

  @media (max-width:900px) {
    .builder { grid-template-columns:1fr; height:auto; }
    .preview { padding:16px; }
  }
</style>
