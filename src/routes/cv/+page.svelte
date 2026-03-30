<script>
  const TEMPLATES = [
    { id: 'minimal', label: 'Minimalistická' },
    { id: 'modern',  label: 'Moderní' },
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
    firstName: '',
    lastName: '',
    role: '',
    email: '',
    phone: '',
    location: '',
    website: '',
    github: '',
    summary: '',
    experience: [],
    education: [],
    skills: [],
    languages: [],
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

  function val(v, placeholder) { return v || placeholder; }
  function hasVal(v) { return v && v.trim() !== ''; }
  function col(v)       { return hasVal(v) ? '#1a1a1a' : '#d1d5db'; }
  function colAccent(v) { return hasVal(v) ? colorHex : '#d1d5db'; }

  const fullName = $derived((cv.firstName + ' ' + cv.lastName).trim());
</script>

<svelte:head>
  <title>CV Builder</title>
  <link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,400;12..96,600;12..96,700;12..96,800&family=Inter:wght@300;400;500;600&family=Lora:wght@400;600&display=swap" rel="stylesheet" />
</svelte:head>

<div class="builder">

  <!-- ══ SIDEBAR ══ -->
  <aside class="panel" id="form-panel">
    <div class="panel-head">
      <a href="/" class="back">← Portfolio</a>
      <span class="panel-title">CV Builder</span>
      <button class="pdf-btn" onclick={exportPDF}>PDF</button>
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
          <div class="f"><label>Jméno</label><input bind:value={cv.firstName} placeholder="Václav" /></div>
          <div class="f"><label>Příjmení</label><input bind:value={cv.lastName} placeholder="Urbanec" /></div>
        </div>
        <div class="f"><label>Pozice / Role</label><input bind:value={cv.role} placeholder="Frontend Developer" /></div>
        <div class="r2">
          <div class="f"><label>E-mail</label><input bind:value={cv.email} type="email" placeholder="email@example.com" /></div>
          <div class="f"><label>Telefon</label><input bind:value={cv.phone} placeholder="605 123 456" /></div>
        </div>
        <div class="r2">
          <div class="f"><label>Lokalita</label><input bind:value={cv.location} placeholder="Praha" /></div>
          <div class="f"><label>Web / Portfolio</label><input bind:value={cv.website} placeholder="mojeportfolio.cz" /></div>
        </div>
        <div class="f"><label>GitHub <span class="opt">volitelné</span></label><input bind:value={cv.github} placeholder="github.com/username" /></div>
        <div class="f"><label>O mně <span class="opt">volitelné</span></label><textarea bind:value={cv.summary} rows="4" placeholder="Pár vět o sobě, čím se zabýváš a co hledáš..."></textarea></div>
      {/if}

      {#if activeSection === 'experience'}
        <p class="section-hint">Pracovní zkušenosti jsou volitelné — zobrazí se jen pokud je přidáš.</p>
        {#each cv.experience as exp, i}
          <div class="card">
            <div class="card-head"><span>Praxe {i+1}</span><button class="rm" onclick={() => removeExp(i)}>✕</button></div>
            <div class="f"><label>Firma</label><input bind:value={exp.company} placeholder="Název firmy" /></div>
            <div class="f"><label>Pozice</label><input bind:value={exp.role} placeholder="Junior Developer" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={exp.from} placeholder="2024" /></div>
              <div class="f"><label>Do <span class="opt">volitelné</span></label><input bind:value={exp.to} placeholder="současnost" /></div>
            </div>
            <div class="f"><label>Popis <span class="opt">volitelné</span></label><textarea bind:value={exp.desc} rows="3" placeholder="Co jsi dělal, čeho dosáhl..."></textarea></div>
          </div>
        {/each}
        <button class="add" onclick={addExp}>+ Přidat pracovní zkušenost</button>
      {/if}

      {#if activeSection === 'education'}
        <p class="section-hint">Vzdělání je volitelné — zobrazí se jen pokud ho přidáš.</p>
        {#each cv.education as edu, i}
          <div class="card">
            <div class="card-head"><span>Škola {i+1}</span><button class="rm" onclick={() => removeEdu(i)}>✕</button></div>
            <div class="f"><label>Škola</label><input bind:value={edu.school} placeholder="Název školy" /></div>
            <div class="f"><label>Obor <span class="opt">volitelné</span></label><input bind:value={edu.field} placeholder="Informatika" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={edu.from} placeholder="2022" /></div>
              <div class="f"><label>Do</label><input bind:value={edu.to} placeholder="2026" /></div>
            </div>
            <div class="f"><label>Poznámka <span class="opt">volitelné</span></label><input bind:value={edu.note} placeholder="Maturitní ročník, Bc., ..." /></div>
          </div>
        {/each}
        <button class="add" onclick={addEdu}>+ Přidat vzdělání</button>
      {/if}

      {#if activeSection === 'skills'}
        <p class="section-hint">Dovednosti jsou volitelné.</p>
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
        <p class="section-hint">Jazyky jsou volitelné.</p>
        {#each cv.languages as lang, i}
          <div class="card">
            <div class="card-head"><span>Jazyk {i+1}</span><button class="rm" onclick={() => removeLang(i)}>✕</button></div>
            <div class="r2">
              <div class="f"><label>Jazyk</label><input bind:value={lang.name} placeholder="Angličtina" /></div>
              <div class="f"><label>Úroveň</label><input bind:value={lang.level} placeholder="B2 / Pokročilý" /></div>
            </div>
          </div>
        {/each}
        <button class="add" onclick={addLang}>+ Přidat jazyk</button>
      {/if}

      {#if activeSection === 'certs'}
        <p class="section-hint">Certifikáty jsou volitelné.</p>
        {#each cv.certs as cert, i}
          <div class="card">
            <div class="card-head"><span>Certifikát {i+1}</span><button class="rm" onclick={() => removeCert(i)}>✕</button></div>
            <div class="f"><label>Název</label><input bind:value={cert.name} placeholder="Cambridge FCE" /></div>
            <div class="r2">
              <div class="f"><label>Vydavatel <span class="opt">volitelné</span></label><input bind:value={cert.issuer} placeholder="Cambridge Assessment" /></div>
              <div class="f"><label>Rok <span class="opt">volitelné</span></label><input bind:value={cert.year} placeholder="2024" /></div>
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

      <!-- ── MODERN ── -->
      {#if template === 'modern'}
        <div class="cv">
          <div class="m-head" style="border-bottom:2px solid {colorHex}">
            <div>
              <div class="m-name" style="color:{col(fullName)}">
                {val(fullName, 'Tvoje celé jméno')}
              </div>
              {#if hasVal(cv.role)}
                <div class="m-role" style="color:{colorHex}">{cv.role}</div>
              {/if}
            </div>
            <div class="m-contacts">
              {#if hasVal(cv.email)}<span>✉ {cv.email}</span>{/if}
              {#if hasVal(cv.phone)}<span>📞 {cv.phone}</span>{/if}
              {#if hasVal(cv.location)}<span>📍 {cv.location}</span>{/if}
              {#if hasVal(cv.website)}<span>🌐 {cv.website}</span>{/if}
              {#if hasVal(cv.github)}<span>⌥ {cv.github}</span>{/if}
            </div>
          </div>

          <div class="m-body">
            <div class="m-left">
              {#if hasVal(cv.summary)}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">O mně</div>
                  <p class="m-text">{cv.summary}</p>
                </div>
              {/if}

              {#if cv.experience.length > 0}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">Pracovní zkušenosti</div>
                  {#each cv.experience as exp}
                    <div class="m-entry">
                      <div class="m-entry-row">
                        <strong style="color:{col(exp.role)}">{val(exp.role, 'Název pozice')}</strong>
                        {#if hasVal(exp.from)}
                          <span class="m-date">{exp.from}{hasVal(exp.to) ? ` — ${exp.to}` : ' — současnost'}</span>
                        {/if}
                      </div>
                      {#if hasVal(exp.company)}<div class="m-company">{exp.company}</div>{/if}
                      {#if hasVal(exp.desc)}<p class="m-desc">{exp.desc}</p>{/if}
                    </div>
                  {/each}
                </div>
              {/if}

              {#if cv.education.length > 0}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">Vzdělání</div>
                  {#each cv.education as edu}
                    <div class="m-entry">
                      <div class="m-entry-row">
                        <strong style="color:{col(edu.school)}">{val(edu.school, 'Název školy')}</strong>
                        {#if hasVal(edu.from)}
                          <span class="m-date">{edu.from}{hasVal(edu.to) ? ` — ${edu.to}` : ''}</span>
                        {/if}
                      </div>
                      {#if hasVal(edu.field)}<div class="m-company">{edu.field}</div>{/if}
                      {#if hasVal(edu.note)}<p class="m-desc">{edu.note}</p>{/if}
                    </div>
                  {/each}
                </div>
              {/if}
            </div>

            <div class="m-right">
              {#if cv.skills.length > 0}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">Dovednosti</div>
                  {#each cv.skills as s}
                    {#if hasVal(s.name)}
                      <div class="m-skill">
                        <span>{s.name}</span>
                        <div class="m-dots">
                          {#each [1,2,3,4,5] as l}
                            <div class="m-dot" style="background:{l <= s.level ? colorHex : '#e5e5e5'}"></div>
                          {/each}
                        </div>
                      </div>
                    {/if}
                  {/each}
                </div>
              {/if}

              {#if cv.languages.length > 0}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">Jazyky</div>
                  {#each cv.languages as l}
                    {#if hasVal(l.name)}
                      <div class="m-lang">
                        <span class="m-lang-n">{l.name}</span>
                        {#if hasVal(l.level)}<span class="m-lang-l">{l.level}</span>{/if}
                      </div>
                    {/if}
                  {/each}
                </div>
              {/if}

              {#if cv.certs.length > 0}
                <div class="m-sec">
                  <div class="m-sec-title" style="color:{colorHex}">Certifikáty</div>
                  {#each cv.certs as c}
                    {#if hasVal(c.name)}
                      <div class="m-cert">
                        <strong>{c.name}</strong>
                        {#if hasVal(c.issuer) || hasVal(c.year)}
                          <span>{c.issuer}{c.year ? ` · ${c.year}` : ''}</span>
                        {/if}
                      </div>
                    {/if}
                  {/each}
                </div>
              {/if}
            </div>
          </div>
        </div>
      {/if}

      <!-- ── MINIMAL ── -->
      {#if template === 'minimal'}
        <div class="cv">
          <div class="n-wrap">
            <div>
              <div class="n-name" style="color:{col(fullName)}">{val(fullName, 'Tvoje celé jméno')}</div>
              {#if hasVal(cv.role)}<div class="n-role">{cv.role}</div>{/if}
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

          {#if hasVal(cv.summary)}
            <p class="n-summary">{cv.summary}</p>
          {/if}

          {#if cv.experience.length > 0}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">PRACOVNÍ ZKUŠENOSTI</div>
              {#each cv.experience as exp}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong style="color:{col(exp.role)}">{val(exp.role, 'Pozice')}</strong>
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
              <div class="n-title" style="color:{colorHex}">VZDĚLÁNÍ</div>
              {#each cv.education as edu}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong style="color:{col(edu.school)}">{val(edu.school, 'Škola')}</strong>
                    {#if hasVal(edu.from)}<span>{edu.from}{hasVal(edu.to) ? ` – ${edu.to}` : ''}</span>{/if}
                  </div>
                  {#if hasVal(edu.field)}<div class="n-sub">{edu.field}{hasVal(edu.note) ? ` · ${edu.note}` : ''}</div>{/if}
                </div>
              {/each}
            </div>
          {/if}

          {#if cv.skills.length > 0 || cv.languages.length > 0 || cv.certs.length > 0}
            <div class="n-bottom">
              {#if cv.skills.length > 0}
                <div class="n-sec half">
                  <div class="n-title" style="color:{colorHex}">DOVEDNOSTI</div>
                  <div class="n-tags">
                    {#each cv.skills as s}{#if hasVal(s.name)}<span class="n-tag">{s.name}</span>{/if}{/each}
                  </div>
                </div>
              {/if}
              {#if cv.languages.length > 0}
                <div class="n-sec half">
                  <div class="n-title" style="color:{colorHex}">JAZYKY</div>
                  {#each cv.languages as l}{#if hasVal(l.name)}<div class="n-lang"><strong>{l.name}</strong>{hasVal(l.level) ? ` — ${l.level}` : ''}</div>{/if}{/each}
                </div>
              {/if}
              {#if cv.certs.length > 0}
                <div class="n-sec half">
                  <div class="n-title" style="color:{colorHex}">CERTIFIKÁTY</div>
                  {#each cv.certs as c}{#if hasVal(c.name)}<div class="n-lang"><strong>{c.name}</strong>{hasVal(c.year) ? ` (${c.year})` : ''}</div>{/if}{/each}
                </div>
              {/if}
            </div>
          {/if}
        </div>
      {/if}

      <!-- ── CLASSIC ── -->
      {#if template === 'classic'}
        <div class="cv">
          <div class="c-head" style="background:{colorHex}">
            <div class="c-name">{val(fullName, 'Tvoje celé jméno')}</div>
            {#if hasVal(cv.role)}<div class="c-role">{cv.role}</div>{/if}
            <div class="c-contacts">
              {#if hasVal(cv.email)}<span>✉ {cv.email}</span>{/if}
              {#if hasVal(cv.phone)}<span>📞 {cv.phone}</span>{/if}
              {#if hasVal(cv.location)}<span>📍 {cv.location}</span>{/if}
              {#if hasVal(cv.website)}<span>🌐 {cv.website}</span>{/if}
              {#if hasVal(cv.github)}<span>⌥ {cv.github}</span>{/if}
            </div>
          </div>
          <div class="c-body">
            <div class="c-main">
              {#if hasVal(cv.summary)}
                <div class="c-sec">
                  <div class="c-title" style="border-bottom:2px solid {colorHex}">Profil</div>
                  <p class="c-text">{cv.summary}</p>
                </div>
              {/if}

              {#if cv.experience.length > 0}
                <div class="c-sec">
                  <div class="c-title" style="border-bottom:2px solid {colorHex}">Pracovní zkušenosti</div>
                  {#each cv.experience as exp}
                    <div class="c-entry">
                      <div class="c-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="c-entry-title">
                          {#if hasVal(exp.role)}<strong>{exp.role}</strong>{/if}
                          {#if hasVal(exp.company)}{hasVal(exp.role) ? ' — ' : ''}<em>{exp.company}</em>{/if}
                        </div>
                        {#if hasVal(exp.from)}<div class="c-entry-date">{exp.from}{hasVal(exp.to) ? ` – ${exp.to}` : ''}</div>{/if}
                        {#if hasVal(exp.desc)}<p class="c-text sm">{exp.desc}</p>{/if}
                      </div>
                    </div>
                  {/each}
                </div>
              {/if}

              {#if cv.education.length > 0}
                <div class="c-sec">
                  <div class="c-title" style="border-bottom:2px solid {colorHex}">Vzdělání</div>
                  {#each cv.education as edu}
                    <div class="c-entry">
                      <div class="c-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="c-entry-title">{val(edu.school, 'Škola')}</div>
                        <div class="c-entry-date">
                          {#if hasVal(edu.field)}{edu.field}{/if}
                          {#if hasVal(edu.from)} · {edu.from}{hasVal(edu.to) ? ` – ${edu.to}` : ''}{/if}
                        </div>
                        {#if hasVal(edu.note)}<p class="c-text sm">{edu.note}</p>{/if}
                      </div>
                    </div>
                  {/each}
                </div>
              {/if}
            </div>

            {#if cv.skills.length > 0 || cv.languages.length > 0 || cv.certs.length > 0}
              <div class="c-side">
                {#if cv.skills.length > 0}
                  <div class="c-sec">
                    <div class="c-title" style="border-bottom:2px solid {colorHex}">Dovednosti</div>
                    {#each cv.skills as s}{#if hasVal(s.name)}
                      <div class="c-skill">
                        <span>{s.name}</span>
                        <div class="c-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                      </div>
                    {/if}{/each}
                  </div>
                {/if}

                {#if cv.languages.length > 0}
                  <div class="c-sec">
                    <div class="c-title" style="border-bottom:2px solid {colorHex}">Jazyky</div>
                    {#each cv.languages as l}{#if hasVal(l.name)}
                      <div class="c-lang"><strong>{l.name}</strong>{#if hasVal(l.level)}<span>{l.level}</span>{/if}</div>
                    {/if}{/each}
                  </div>
                {/if}

                {#if cv.certs.length > 0}
                  <div class="c-sec">
                    <div class="c-title" style="border-bottom:2px solid {colorHex}">Certifikáty</div>
                    {#each cv.certs as c}{#if hasVal(c.name)}
                      <div class="c-cert">
                        <strong>{c.name}</strong>
                        {#if hasVal(c.issuer) || hasVal(c.year)}<span>{c.issuer}{c.year ? ` · ${c.year}` : ''}</span>{/if}
                      </div>
                    {/if}{/each}
                  </div>
                {/if}
              </div>
            {:else}
              <div class="c-side c-side-empty"></div>
            {/if}
          </div>
        </div>
      {/if}

    </div>
  </main>
</div>

<style>
  :global(*, *::before, *::after) { margin:0; padding:0; box-sizing:border-box; }
  :global(html,body) { height:100%; background:#0f0f0f; font-family:'Inter',sans-serif; overflow-x:hidden; }

  .builder { display:grid; grid-template-columns:360px 1fr; height:100vh; overflow:hidden; }

  /* ── PANEL ── */
  .panel { background:#141414; border-right:1px solid rgba(255,255,255,.07); overflow-y:auto; display:flex; flex-direction:column; }
  .panel::-webkit-scrollbar { width:3px; }
  .panel::-webkit-scrollbar-thumb { background:rgba(255,255,255,.12); border-radius:2px; }

  .panel-head { padding:16px 20px; border-bottom:1px solid rgba(255,255,255,.07); display:flex; align-items:center; gap:10px; position:sticky; top:0; background:#141414; z-index:10; }
  .back { font-size:11px; font-weight:600; color:rgba(255,255,255,.3); text-decoration:none; transition:color .2s; white-space:nowrap; }
  .back:hover { color:rgba(255,255,255,.7); }
  .panel-title { font-size:15px; font-weight:800; color:#fff; flex:1; font-family:'Bricolage Grotesque',sans-serif; letter-spacing:-.3px; }
  .pdf-btn { font-size:11px; font-weight:700; padding:6px 13px; background:#fff; color:#111; border:none; cursor:pointer; border-radius:3px; letter-spacing:.5px; transition:opacity .15s; }
  .pdf-btn:hover { opacity:.85; }

  .controls { border-bottom:1px solid rgba(255,255,255,.07); padding:12px 20px; display:flex; flex-direction:column; gap:10px; }
  .ctrl-group { display:flex; align-items:center; gap:12px; }
  .ctrl-lbl { font-size:9px; font-weight:700; letter-spacing:2px; text-transform:uppercase; color:rgba(255,255,255,.28); min-width:48px; }
  .ctrl-row { display:flex; gap:5px; align-items:center; flex-wrap:wrap; }

  .tpl { font-size:11px; font-weight:600; padding:5px 11px; background:rgba(255,255,255,.05); color:rgba(255,255,255,.38); border:1px solid rgba(255,255,255,.09); cursor:pointer; transition:all .15s; border-radius:3px; }
  .tpl.on { background:#fff; color:#111; border-color:#fff; }
  .tpl:hover:not(.on) { background:rgba(255,255,255,.09); color:rgba(255,255,255,.7); }

  .clr { width:20px; height:20px; border-radius:50%; border:2px solid transparent; cursor:pointer; transition:transform .15s; outline:2px solid transparent; }
  .clr.on { outline:2px solid rgba(255,255,255,.6); outline-offset:2px; }
  .clr:hover { transform:scale(1.15); }

  .tabs { display:flex; flex-wrap:wrap; gap:3px; padding:10px 20px; border-bottom:1px solid rgba(255,255,255,.07); }
  .tab { font-size:11px; font-weight:600; padding:5px 9px; background:transparent; color:rgba(255,255,255,.32); border:1px solid transparent; border-radius:4px; cursor:pointer; transition:all .15s; }
  .tab.on { background:rgba(255,255,255,.09); color:#fff; border-color:rgba(255,255,255,.13); }
  .tab:hover:not(.on) { color:rgba(255,255,255,.6); }

  .form-body { padding:16px 20px; display:flex; flex-direction:column; gap:11px; }
  .section-hint { font-size:10px; color:rgba(255,255,255,.28); font-style:italic; line-height:1.5; }
  .r2 { display:grid; grid-template-columns:1fr 1fr; gap:9px; }
  .f { display:flex; flex-direction:column; gap:4px; }
  .f label { font-size:9px; font-weight:700; letter-spacing:1.5px; text-transform:uppercase; color:rgba(255,255,255,.28); display:flex; align-items:center; gap:5px; }
  .opt { font-size:8px; font-weight:500; letter-spacing:.5px; color:rgba(255,255,255,.18); text-transform:none; border:1px solid rgba(255,255,255,.1); padding:1px 5px; border-radius:2px; }
  .f input, .f textarea { background:rgba(255,255,255,.04); border:1px solid rgba(255,255,255,.09); color:#fff; font-family:'Inter',sans-serif; font-size:13px; padding:8px 10px; outline:none; resize:vertical; border-radius:4px; transition:border-color .2s, background .2s; }
  .f input:focus, .f textarea:focus { border-color:rgba(255,255,255,.35); background:rgba(255,255,255,.06); }
  .f input::placeholder, .f textarea::placeholder { color:rgba(255,255,255,.18); }

  .card { background:rgba(255,255,255,.025); border:1px solid rgba(255,255,255,.06); padding:13px; border-radius:6px; display:flex; flex-direction:column; gap:9px; }
  .card-head { display:flex; justify-content:space-between; align-items:center; }
  .card-head span { font-size:9px; font-weight:700; letter-spacing:1.5px; color:rgba(255,255,255,.3); text-transform:uppercase; }
  .rm { font-size:10px; background:rgba(255,80,80,.12); color:rgba(255,100,100,.7); border:none; width:22px; height:22px; border-radius:50%; cursor:pointer; transition:background .15s; display:flex; align-items:center; justify-content:center; }
  .rm:hover { background:rgba(255,80,80,.25); color:#ff6464; }
  .rm.sm { width:18px; height:18px; font-size:9px; }
  .add { font-size:12px; font-weight:600; padding:9px; background:transparent; color:rgba(255,255,255,.32); border:1px dashed rgba(255,255,255,.12); border-radius:4px; cursor:pointer; transition:all .2s; }
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

  /* ── CV BASE ── */
  .cv { background:#fff; color:#1a1a1a; font-family:'Inter',sans-serif; font-size:10pt; line-height:1.5; box-shadow:0 12px 48px rgba(0,0,0,.55); min-height:297mm; }

  /* MODERN */
  .m-head { padding:30px 34px 22px; display:flex; justify-content:space-between; align-items:flex-start; gap:20px; }
  .m-name { font-family:'Bricolage Grotesque',sans-serif; font-size:25pt; font-weight:800; letter-spacing:-1px; line-height:1; word-break:break-word; }
  .m-role { font-size:10.5pt; font-weight:500; margin-top:5px; }
  .m-contacts { display:flex; flex-direction:column; gap:4px; text-align:right; font-size:8pt; color:#444; max-width:220px; word-break:break-all; }
  .m-body { display:grid; grid-template-columns:1fr 190px; padding:22px 34px; gap:26px; }
  .m-sec { margin-bottom:18px; }
  .m-sec-title { font-family:'Bricolage Grotesque',sans-serif; font-size:7.5pt; font-weight:800; letter-spacing:2px; text-transform:uppercase; margin-bottom:9px; padding-bottom:5px; border-bottom:1px solid #eee; }
  .m-text { font-size:9pt; line-height:1.7; color:#333; }
  .m-entry { margin-bottom:12px; }
  .m-entry-row { display:flex; justify-content:space-between; align-items:baseline; }
  .m-entry-row strong { font-size:9.5pt; font-weight:700; }
  .m-date { font-size:8pt; color:#999; }
  .m-company { font-size:8.5pt; color:#666; margin:2px 0 4px; }
  .m-desc { font-size:8.5pt; color:#555; line-height:1.6; }
  .m-skill { display:flex; align-items:center; justify-content:space-between; margin-bottom:8px; font-size:9pt; }
  .m-dots { display:flex; gap:4px; }
  .m-dot { width:8px; height:8px; border-radius:50%; }
  .m-lang { display:flex; justify-content:space-between; margin-bottom:7px; padding-bottom:7px; border-bottom:1px solid #f0f0f0; }
  .m-lang-n { font-size:9pt; font-weight:600; }
  .m-lang-l { font-size:8pt; color:#777; }
  .m-cert { margin-bottom:9px; }
  .m-cert strong { display:block; font-size:9pt; }
  .m-cert span { font-size:8pt; color:#777; }

  /* MINIMAL */
  .n-wrap { display:flex; justify-content:space-between; align-items:flex-start; padding:38px 46px 16px; }
  .n-name { font-family:'Bricolage Grotesque',sans-serif; font-size:27pt; font-weight:800; letter-spacing:-1px; word-break:break-word; }
  .n-role { font-size:10.5pt; color:#555; margin-top:4px; }
  .n-contacts { display:flex; flex-direction:column; gap:3px; text-align:right; font-size:8pt; color:#666; max-width:200px; word-break:break-all; }
  .n-line { height:2px; margin:0 46px 16px; }
  .n-summary { font-size:9.5pt; line-height:1.7; margin:0 46px 22px; color:#333; }
  .n-sec { margin:0 46px 18px; }
  .n-sec.half { flex:1; min-width:150px; margin:0; }
  .n-title { font-size:7.5pt; font-weight:700; letter-spacing:3px; text-transform:uppercase; margin-bottom:10px; color:#888; }
  .n-entry { margin-bottom:12px; padding-bottom:12px; border-bottom:1px solid #f0f0f0; }
  .n-entry:last-child { border-bottom:none; }
  .n-entry-row { display:flex; justify-content:space-between; align-items:baseline; }
  .n-entry-row strong { font-size:9.5pt; font-weight:700; }
  .n-entry-row span { font-size:8pt; color:#999; }
  .n-sub { font-size:8.5pt; color:#777; margin-top:2px; }
  .n-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .n-bottom { display:flex; gap:26px; flex-wrap:wrap; margin:0 46px; padding-top:16px; border-top:1px solid #eee; }
  .n-tags { display:flex; flex-wrap:wrap; gap:5px; }
  .n-tag { font-size:8pt; padding:2px 8px; border:1px solid #ddd; color:#444; border-radius:2px; }
  .n-lang { font-size:9pt; color:#444; margin-bottom:5px; }

  /* CLASSIC */
  .c-head { padding:28px 34px; color:#fff; }
  .c-name { font-family:'Lora',serif; font-size:23pt; font-weight:600; word-break:break-word; }
  .c-role { font-size:10.5pt; opacity:.85; margin:4px 0 12px; }
  .c-contacts { display:flex; gap:16px; flex-wrap:wrap; font-size:8pt; opacity:.88; }
  .c-body { display:grid; grid-template-columns:1fr 180px; }
  .c-main { padding:22px 26px 22px 34px; border-right:1px solid #eee; }
  .c-side { padding:22px 18px; background:#f8f8f8; }
  .c-side-empty { background:#f8f8f8; }
  .c-sec { margin-bottom:18px; }
  .c-title { font-size:8pt; font-weight:700; letter-spacing:2px; text-transform:uppercase; margin-bottom:10px; padding-bottom:5px; color:#333; }
  .c-text { font-size:9.5pt; color:#444; line-height:1.7; }
  .c-text.sm { font-size:9pt; margin-top:4px; }
  .c-entry { display:flex; gap:10px; margin-bottom:12px; align-items:flex-start; }
  .c-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; margin-top:5px; }
  .c-entry-title { font-size:9.5pt; font-weight:700; }
  .c-entry-title em { font-style:normal; font-weight:400; color:#666; }
  .c-entry-date { font-size:8pt; color:#999; margin:2px 0; }
  .c-skill { margin-bottom:9px; font-size:9pt; }
  .c-skill span { display:block; margin-bottom:3px; font-weight:500; color:#333; }
  .c-bar { height:3px; background:#e0e0e0; border-radius:2px; overflow:hidden; }
  .c-bar div { height:100%; border-radius:2px; }
  .c-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:7px; align-items:baseline; }
  .c-lang strong { color:#222; }
  .c-lang span { color:#777; font-size:8pt; }
  .c-cert { margin-bottom:9px; }
  .c-cert strong { display:block; font-size:9pt; color:#222; }
  .c-cert span { font-size:8pt; color:#777; }

  /* PRINT */
  @media print {
    :global(body) { background:white !important; }
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
