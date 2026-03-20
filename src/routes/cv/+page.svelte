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

  // Helper — zobrazí hodnotu nebo placeholder šedě
  function val(v, placeholder) { return v || placeholder; }
  function hasVal(v) { return v && v.trim() !== ''; }
  function col(v) { return hasVal(v) ? '#1a1a1a' : '#d1d5db'; }
  function colAccent(v) { return hasVal(v) ? colorHex : '#d1d5db'; }
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
      <button class="pdf-btn" onclick={exportPDF}>⬇ PDF</button>
    </div>

    <div class="pick-row">
      <span class="pick-lbl">Šablona</span>
      <div class="pick-btns">
        {#each TEMPLATES as t}
          <button class="tpl" class:on={template === t.id} onclick={() => template = t.id}>{t.label}</button>
        {/each}
      </div>
    </div>

    <div class="pick-row">
      <span class="pick-lbl">Barva</span>
      <div class="pick-btns">
        {#each COLORS as c}
          <button class="clr" class:on={colorHex === c.hex} style="background:{c.hex}" title={c.label} onclick={() => colorHex = c.hex}></button>
        {/each}
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
        <div class="f"><label>GitHub</label><input bind:value={cv.github} placeholder="github.com/username" /></div>
        <div class="f"><label>Krátký popis</label><textarea bind:value={cv.summary} rows="4" placeholder="Pár vět o sobě, čím se zabýváš a co hledáš..."></textarea></div>
      {/if}

      {#if activeSection === 'experience'}
        {#each cv.experience as exp, i}
          <div class="card">
            <div class="card-head"><span>Praxe {i+1}</span><button class="rm" onclick={() => removeExp(i)}>✕</button></div>
            <div class="f"><label>Firma</label><input bind:value={exp.company} placeholder="Název firmy" /></div>
            <div class="f"><label>Pozice</label><input bind:value={exp.role} placeholder="Junior Developer" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={exp.from} placeholder="2024" /></div>
              <div class="f"><label>Do</label><input bind:value={exp.to} placeholder="současnost" /></div>
            </div>
            <div class="f"><label>Popis</label><textarea bind:value={exp.desc} rows="3" placeholder="Co jsi dělal, čeho dosáhl..."></textarea></div>
          </div>
        {/each}
        <button class="add" onclick={addExp}>+ Přidat pracovní zkušenost</button>
      {/if}

      {#if activeSection === 'education'}
        {#each cv.education as edu, i}
          <div class="card">
            <div class="card-head"><span>Škola {i+1}</span><button class="rm" onclick={() => removeEdu(i)}>✕</button></div>
            <div class="f"><label>Škola</label><input bind:value={edu.school} placeholder="Název školy" /></div>
            <div class="f"><label>Obor</label><input bind:value={edu.field} placeholder="Informatika" /></div>
            <div class="r2">
              <div class="f"><label>Od</label><input bind:value={edu.from} placeholder="2022" /></div>
              <div class="f"><label>Do</label><input bind:value={edu.to} placeholder="2026" /></div>
            </div>
            <div class="f"><label>Poznámka</label><input bind:value={edu.note} placeholder="Maturitní ročník, Bc., ..." /></div>
          </div>
        {/each}
        <button class="add" onclick={addEdu}>+ Přidat vzdělání</button>
      {/if}

      {#if activeSection === 'skills'}
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
        {#each cv.certs as cert, i}
          <div class="card">
            <div class="card-head"><span>Certifikát {i+1}</span><button class="rm" onclick={() => removeCert(i)}>✕</button></div>
            <div class="f"><label>Název</label><input bind:value={cert.name} placeholder="Cambridge FCE" /></div>
            <div class="r2">
              <div class="f"><label>Vydavatel</label><input bind:value={cert.issuer} placeholder="Cambridge Assessment" /></div>
              <div class="f"><label>Rok</label><input bind:value={cert.year} placeholder="2024" /></div>
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
              <div class="m-name" style="color:{col(cv.firstName + cv.lastName)}">
                {val(cv.firstName || cv.lastName ? `${cv.firstName} ${cv.lastName}`.trim() : '', 'Tvoje celé jméno')}
              </div>
              <div class="m-role" style="color:{colAccent(cv.role)}">
                {val(cv.role, 'Pozice / Role')}
              </div>
            </div>
            <div class="m-contacts">
              <span style="color:{col(cv.email)}">✉ {val(cv.email, 'email@example.com')}</span>
              <span style="color:{col(cv.phone)}">📞 {val(cv.phone, '605 123 456')}</span>
              <span style="color:{col(cv.location)}">📍 {val(cv.location, 'Město / Region')}</span>
              {#if hasVal(cv.website)}<span>🌐 {cv.website}</span>{/if}
              {#if hasVal(cv.github)}<span>⌥ {cv.github}</span>{/if}
            </div>
          </div>

          <div class="m-body">
            <div class="m-left">
              <!-- Summary -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">O mně</div>
                <p class="m-text" style="color:{col(cv.summary)}">
                  {val(cv.summary, 'Napiš pár vět o sobě — čím se zabýváš, co tě baví a co hledáš. Tento text se zobrazí jako úvod tvého CV.')}
                </p>
              </div>

              <!-- Experience -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">Pracovní zkušenosti</div>
                {#if cv.experience.length === 0}
                  <p class="m-text ph">Přidej pracovní zkušenosti vlevo v sekci "Praxe".</p>
                {:else}
                  {#each cv.experience as exp}
                    <div class="m-entry">
                      <div class="m-entry-row">
                        <strong style="color:{col(exp.role)}">{val(exp.role, 'Název pozice')}</strong>
                        <span class="m-date">{val(exp.from, '2024')}{exp.to ? ` — ${exp.to}` : ' — současnost'}</span>
                      </div>
                      <div class="m-company" style="color:{col(exp.company)}">{val(exp.company, 'Název firmy')}</div>
                      <p class="m-desc" style="color:{col(exp.desc)}">{val(exp.desc, 'Popis pracovní náplně a dosažených výsledků.')}</p>
                    </div>
                  {/each}
                {/if}
              </div>

              <!-- Education -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">Vzdělání</div>
                {#if cv.education.length === 0}
                  <p class="m-text ph">Přidej vzdělání vlevo v sekci "Vzdělání".</p>
                {:else}
                  {#each cv.education as edu}
                    <div class="m-entry">
                      <div class="m-entry-row">
                        <strong style="color:{col(edu.school)}">{val(edu.school, 'Název školy')}</strong>
                        <span class="m-date">{val(edu.from, '2022')}{edu.to ? ` — ${edu.to}` : ''}</span>
                      </div>
                      <div class="m-company" style="color:{col(edu.field)}">{val(edu.field, 'Obor studia')}</div>
                      {#if hasVal(edu.note)}<p class="m-desc">{edu.note}</p>{/if}
                    </div>
                  {/each}
                {/if}
              </div>
            </div>

            <div class="m-right">
              <!-- Skills -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">Dovednosti</div>
                {#if cv.skills.length === 0}
                  {#each ['Dovednost 1','Dovednost 2','Dovednost 3'] as ph}
                    <div class="m-skill">
                      <span style="color:#d1d5db">{ph}</span>
                      <div class="m-dots">
                        {#each [1,2,3,4,5] as l}
                          <div class="m-dot" style="background:#e5e5e5"></div>
                        {/each}
                      </div>
                    </div>
                  {/each}
                {:else}
                  {#each cv.skills as s}
                    <div class="m-skill">
                      <span style="color:{col(s.name)}">{val(s.name, 'Dovednost')}</span>
                      <div class="m-dots">
                        {#each [1,2,3,4,5] as l}
                          <div class="m-dot" style="background:{l <= s.level ? colorHex : '#e5e5e5'}"></div>
                        {/each}
                      </div>
                    </div>
                  {/each}
                {/if}
              </div>

              <!-- Languages -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">Jazyky</div>
                {#if cv.languages.length === 0}
                  {#each [['Čeština','Rodilý mluvčí'],['Angličtina','B2']] as [n,l]}
                    <div class="m-lang">
                      <span class="m-lang-n" style="color:#d1d5db">{n}</span>
                      <span class="m-lang-l" style="color:#d1d5db">{l}</span>
                    </div>
                  {/each}
                {:else}
                  {#each cv.languages as l}
                    <div class="m-lang">
                      <span class="m-lang-n" style="color:{col(l.name)}">{val(l.name, 'Jazyk')}</span>
                      <span class="m-lang-l" style="color:{col(l.level)}">{val(l.level, 'Úroveň')}</span>
                    </div>
                  {/each}
                {/if}
              </div>

              <!-- Certs -->
              <div class="m-sec">
                <div class="m-sec-title" style="color:{colorHex}">Certifikáty</div>
                {#if cv.certs.length === 0}
                  <p class="m-text ph">Přidej certifikáty v sekci "Certifikáty".</p>
                {:else}
                  {#each cv.certs as c}
                    <div class="m-cert">
                      <strong style="color:{col(c.name)}">{val(c.name, 'Název certifikátu')}</strong>
                      <span>{val(c.issuer, 'Vydavatel')}{c.year ? ` · ${c.year}` : ''}</span>
                    </div>
                  {/each}
                {/if}
              </div>
            </div>
          </div>
        </div>
      {/if}

      <!-- ── MINIMAL ── -->
      {#if template === 'minimal'}
        <div class="cv">
          <div class="n-wrap">
            <div>
              <div class="n-name" style="color:{col(cv.firstName + cv.lastName)}">
                {val(cv.firstName || cv.lastName ? `${cv.firstName} ${cv.lastName}`.trim() : '', 'Tvoje celé jméno')}
              </div>
              <div class="n-role" style="color:{col(cv.role)}">{val(cv.role, 'Pozice / Role')}</div>
            </div>
            <div class="n-contacts">
              <span style="color:{col(cv.email)}">{val(cv.email, 'email@example.com')}</span>
              <span style="color:{col(cv.phone)}">{val(cv.phone, '605 123 456')}</span>
              <span style="color:{col(cv.location)}">{val(cv.location, 'Město')}</span>
              {#if hasVal(cv.website)}<span>{cv.website}</span>{/if}
            </div>
          </div>
          <div class="n-line" style="background:{colorHex}"></div>
          <p class="n-summary" style="color:{col(cv.summary)}">
            {val(cv.summary, 'Krátký popis — napiš pár vět o sobě, svých zkušenostech a cílech. Tento text se zobrazí jako úvod.')}
          </p>

          {#if cv.experience.length > 0}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">PRACOVNÍ ZKUŠENOSTI</div>
              {#each cv.experience as exp}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong style="color:{col(exp.role)}">{val(exp.role, 'Pozice')}</strong>
                    <span>{exp.from}{exp.to ? ` – ${exp.to}` : ''}</span>
                  </div>
                  <div class="n-sub" style="color:{col(exp.company)}">{val(exp.company, 'Firma')}</div>
                  {#if hasVal(exp.desc)}<p class="n-desc">{exp.desc}</p>{/if}
                </div>
              {/each}
            </div>
          {:else}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">PRACOVNÍ ZKUŠENOSTI</div>
              <p class="n-desc ph">Přidej pracovní zkušenosti vlevo.</p>
            </div>
          {/if}

          {#if cv.education.length > 0}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">VZDĚLÁNÍ</div>
              {#each cv.education as edu}
                <div class="n-entry">
                  <div class="n-entry-row">
                    <strong style="color:{col(edu.school)}">{val(edu.school, 'Škola')}</strong>
                    <span>{edu.from}{edu.to ? ` – ${edu.to}` : ''}</span>
                  </div>
                  <div class="n-sub">{edu.field}{edu.note ? ` · ${edu.note}` : ''}</div>
                </div>
              {/each}
            </div>
          {:else}
            <div class="n-sec">
              <div class="n-title" style="color:{colorHex}">VZDĚLÁNÍ</div>
              <p class="n-desc ph">Přidej vzdělání vlevo.</p>
            </div>
          {/if}

          <div class="n-bottom">
            <div class="n-sec half">
              <div class="n-title" style="color:{colorHex}">DOVEDNOSTI</div>
              <div class="n-tags">
                {#if cv.skills.length === 0}
                  {#each ['Dovednost 1','Dovednost 2','Dovednost 3'] as ph}
                    <span class="n-tag" style="color:#d1d5db;border-color:#e5e7eb">{ph}</span>
                  {/each}
                {:else}
                  {#each cv.skills as s}{#if s.name}<span class="n-tag">{s.name}</span>{/if}{/each}
                {/if}
              </div>
            </div>
            <div class="n-sec half">
              <div class="n-title" style="color:{colorHex}">JAZYKY</div>
              {#if cv.languages.length === 0}
                {#each [['Čeština','Rodilý mluvčí'],['Angličtina','B2']] as [n,l]}
                  <div class="n-lang" style="color:#d1d5db"><strong style="color:#d1d5db">{n}</strong> — {l}</div>
                {/each}
              {:else}
                {#each cv.languages as l}{#if l.name}<div class="n-lang"><strong>{l.name}</strong> — {l.level}</div>{/if}{/each}
              {/if}
            </div>
            {#if cv.certs.length > 0}
              <div class="n-sec half">
                <div class="n-title" style="color:{colorHex}">CERTIFIKÁTY</div>
                {#each cv.certs as c}{#if c.name}<div class="n-lang"><strong>{c.name}</strong>{c.year ? ` (${c.year})` : ''}</div>{/if}{/each}
              </div>
            {/if}
          </div>
        </div>
      {/if}

      <!-- ── CLASSIC ── -->
      {#if template === 'classic'}
        <div class="cv">
          <div class="c-head" style="background:{colorHex}">
            <div class="c-name">{val(cv.firstName || cv.lastName ? `${cv.firstName} ${cv.lastName}`.trim() : '', 'Tvoje celé jméno')}</div>
            <div class="c-role">{val(cv.role, 'Pozice / Role')}</div>
            <div class="c-contacts">
              <span>✉ {val(cv.email, 'email@example.com')}</span>
              <span>📞 {val(cv.phone, '605 123 456')}</span>
              <span>📍 {val(cv.location, 'Město')}</span>
              {#if hasVal(cv.website)}<span>🌐 {cv.website}</span>{/if}
            </div>
          </div>
          <div class="c-body">
            <div class="c-main">
              <div class="c-sec">
                <div class="c-title" style="border-bottom:2px solid {colorHex}">Profil</div>
                <p class="c-text" style="color:{col(cv.summary)}">
                  {val(cv.summary, 'Krátký popis — napiš pár vět o sobě, svých zkušenostech a cílech.')}
                </p>
              </div>
              <div class="c-sec">
                <div class="c-title" style="border-bottom:2px solid {colorHex}">Pracovní zkušenosti</div>
                {#if cv.experience.length === 0}
                  <p class="c-text" style="color:#d1d5db">Přidej pracovní zkušenosti vlevo v sekci "Praxe".</p>
                {:else}
                  {#each cv.experience as exp}
                    <div class="c-entry">
                      <div class="c-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="c-entry-title" style="color:{col(exp.role)}">{val(exp.role, 'Pozice')} — <em style="color:{col(exp.company)}">{val(exp.company, 'Firma')}</em></div>
                        <div class="c-entry-date">{exp.from}{exp.to ? ` – ${exp.to}` : ''}</div>
                        {#if hasVal(exp.desc)}<p class="c-text sm">{exp.desc}</p>{/if}
                      </div>
                    </div>
                  {/each}
                {/if}
              </div>
              <div class="c-sec">
                <div class="c-title" style="border-bottom:2px solid {colorHex}">Vzdělání</div>
                {#if cv.education.length === 0}
                  <p class="c-text" style="color:#d1d5db">Přidej vzdělání vlevo v sekci "Vzdělání".</p>
                {:else}
                  {#each cv.education as edu}
                    <div class="c-entry">
                      <div class="c-dot" style="background:{colorHex}"></div>
                      <div>
                        <div class="c-entry-title" style="color:{col(edu.school)}">{val(edu.school, 'Škola')}</div>
                        <div class="c-entry-date">{edu.field}{edu.from ? ` · ${edu.from}` : ''}{edu.to ? ` – ${edu.to}` : ''}</div>
                        {#if hasVal(edu.note)}<p class="c-text sm">{edu.note}</p>{/if}
                      </div>
                    </div>
                  {/each}
                {/if}
              </div>
            </div>
            <div class="c-side">
              <div class="c-sec">
                <div class="c-title" style="border-bottom:2px solid {colorHex}">Dovednosti</div>
                {#if cv.skills.length === 0}
                  {#each ['Dovednost 1','Dovednost 2','Dovednost 3'] as ph}
                    <div class="c-skill">
                      <span style="color:#d1d5db">{ph}</span>
                      <div class="c-bar"><div style="width:60%;background:#e5e7eb"></div></div>
                    </div>
                  {/each}
                {:else}
                  {#each cv.skills as s}{#if s.name}
                    <div class="c-skill">
                      <span style="color:{col(s.name)}">{val(s.name,'Dovednost')}</span>
                      <div class="c-bar"><div style="width:{s.level*20}%;background:{colorHex}"></div></div>
                    </div>
                  {/if}{/each}
                {/if}
              </div>
              <div class="c-sec">
                <div class="c-title" style="border-bottom:2px solid {colorHex}">Jazyky</div>
                {#if cv.languages.length === 0}
                  {#each [['Čeština','Rodilý mluvčí'],['Angličtina','B2']] as [n,l]}
                    <div class="c-lang"><strong style="color:#d1d5db">{n}</strong><span style="color:#d1d5db">{l}</span></div>
                  {/each}
                {:else}
                  {#each cv.languages as l}{#if l.name}
                    <div class="c-lang"><strong>{l.name}</strong><span>{l.level}</span></div>
                  {/if}{/each}
                {/if}
              </div>
              {#if cv.certs.length > 0}
                <div class="c-sec">
                  <div class="c-title" style="border-bottom:2px solid {colorHex}">Certifikáty</div>
                  {#each cv.certs as c}{#if c.name}
                    <div class="c-cert"><strong>{c.name}</strong><span>{c.issuer}{c.year ? ` · ${c.year}` : ''}</span></div>
                  {/if}{/each}
                </div>
              {/if}
            </div>
          </div>
        </div>
      {/if}

    </div>
  </main>
</div>

<style>
  :global(*, *::before, *::after) { margin:0; padding:0; box-sizing:border-box; }
  :global(html,body) { height:100%; background:#111; font-family:'Inter',sans-serif; }

  .builder { display:grid; grid-template-columns:370px 1fr; height:100vh; overflow:hidden; }

  .panel { background:#161616; border-right:1px solid rgba(255,255,255,.08); overflow-y:auto; display:flex; flex-direction:column; }
  .panel::-webkit-scrollbar { width:3px; }
  .panel::-webkit-scrollbar-thumb { background:rgba(255,255,255,.15); }

  .panel-head { padding:18px 22px; border-bottom:1px solid rgba(255,255,255,.07); display:flex; align-items:center; gap:10px; position:sticky; top:0; background:#161616; z-index:10; }
  .back { font-size:11px; font-weight:600; color:rgba(255,255,255,.35); text-decoration:none; transition:color .2s; }
  .back:hover { color:#fff; }
  .panel-title { font-size:16px; font-weight:800; color:#fff; flex:1; font-family:'Bricolage Grotesque',sans-serif; }
  .pdf-btn { font-size:11px; font-weight:700; padding:7px 14px; background:#fff; color:#111; border:none; cursor:pointer; }
  .pdf-btn:hover { opacity:.85; }

  .pick-row { display:flex; align-items:center; gap:10px; padding:10px 22px; border-bottom:1px solid rgba(255,255,255,.05); }
  .pick-lbl { font-size:9px; font-weight:700; letter-spacing:2px; text-transform:uppercase; color:rgba(255,255,255,.3); min-width:52px; }
  .pick-btns { display:flex; gap:6px; align-items:center; }

  .tpl { font-size:11px; font-weight:600; padding:5px 11px; background:rgba(255,255,255,.06); color:rgba(255,255,255,.4); border:1px solid rgba(255,255,255,.1); cursor:pointer; transition:all .15s; }
  .tpl.on { background:#fff; color:#111; border-color:#fff; }

  .clr { width:22px; height:22px; border-radius:50%; border:3px solid transparent; cursor:pointer; transition:border-color .15s; }
  .clr.on { border-color:#fff; }

  .tabs { display:flex; flex-wrap:wrap; gap:4px; padding:10px 22px; border-bottom:1px solid rgba(255,255,255,.07); }
  .tab { font-size:11px; font-weight:600; padding:5px 9px; background:transparent; color:rgba(255,255,255,.35); border:1px solid transparent; border-radius:4px; cursor:pointer; transition:all .15s; }
  .tab.on { background:rgba(255,255,255,.1); color:#fff; border-color:rgba(255,255,255,.15); }
  .tab:hover:not(.on) { color:rgba(255,255,255,.65); }

  .form-body { padding:18px 22px; display:flex; flex-direction:column; gap:12px; }
  .r2 { display:grid; grid-template-columns:1fr 1fr; gap:10px; }
  .f { display:flex; flex-direction:column; gap:4px; }
  .f label { font-size:9px; font-weight:700; letter-spacing:1.5px; text-transform:uppercase; color:rgba(255,255,255,.3); }
  .f input, .f textarea { background:rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1); color:#fff; font-family:'Inter',sans-serif; font-size:13px; padding:8px 11px; outline:none; resize:vertical; border-radius:4px; transition:border-color .2s; }
  .f input:focus, .f textarea:focus { border-color:rgba(255,255,255,.4); }
  .f input::placeholder, .f textarea::placeholder { color:rgba(255,255,255,.2); }

  .card { background:rgba(255,255,255,.03); border:1px solid rgba(255,255,255,.07); padding:13px; border-radius:6px; display:flex; flex-direction:column; gap:9px; }
  .card-head { display:flex; justify-content:space-between; align-items:center; }
  .card-head span { font-size:10px; font-weight:700; letter-spacing:1px; color:rgba(255,255,255,.35); text-transform:uppercase; }
  .rm { font-size:11px; background:rgba(255,80,80,.15); color:rgba(255,100,100,.8); border:none; width:22px; height:22px; border-radius:50%; cursor:pointer; }
  .rm.sm { width:18px; height:18px; font-size:9px; }
  .add { font-size:12px; font-weight:700; padding:9px; background:rgba(255,255,255,.05); color:rgba(255,255,255,.4); border:1px dashed rgba(255,255,255,.15); border-radius:4px; cursor:pointer; transition:all .2s; }
  .add:hover { background:rgba(255,255,255,.1); color:#fff; }

  .skill-row { display:flex; align-items:center; gap:8px; }
  .skill-inp { flex:1; background:rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1); color:#fff; font-size:13px; padding:8px 11px; outline:none; border-radius:4px; }
  .dots { display:flex; gap:4px; }
  .dot-btn { width:13px; height:13px; border-radius:50%; background:rgba(255,255,255,.12); border:none; cursor:pointer; transition:background .15s; }
  .dot-btn.on { background:#fff; }

  .preview { background:#1a1a1a; overflow-y:auto; display:flex; justify-content:center; padding:28px; }
  .preview::-webkit-scrollbar { width:4px; }
  .preview::-webkit-scrollbar-thumb { background:rgba(255,255,255,.1); }
  .preview-inner { width:100%; max-width:794px; }

  /* ── CV BASE ── */
  .cv { background:#fff; color:#1a1a1a; font-family:'Inter',sans-serif; font-size:10pt; line-height:1.5; box-shadow:0 8px 40px rgba(0,0,0,.5); min-height:297mm; }
  .ph { color:#d1d5db !important; font-style:italic; }

  /* MODERN */
  .m-head { padding:32px 36px 24px; display:flex; justify-content:space-between; align-items:flex-start; gap:20px; }
  .m-name { font-family:'Bricolage Grotesque',sans-serif; font-size:26pt; font-weight:800; letter-spacing:-1px; line-height:1; }
  .m-role { font-size:11pt; font-weight:500; margin-top:5px; }
  .m-contacts { display:flex; flex-direction:column; gap:4px; text-align:right; font-size:8pt; }
  .m-body { display:grid; grid-template-columns:1fr 200px; padding:24px 36px; gap:28px; }
  .m-sec { margin-bottom:20px; }
  .m-sec-title { font-family:'Bricolage Grotesque',sans-serif; font-size:8pt; font-weight:800; letter-spacing:2px; text-transform:uppercase; margin-bottom:10px; padding-bottom:5px; border-bottom:1px solid #eee; }
  .m-text { font-size:9pt; line-height:1.7; }
  .m-entry { margin-bottom:12px; }
  .m-entry-row { display:flex; justify-content:space-between; align-items:baseline; }
  .m-entry-row strong { font-size:10pt; font-weight:700; }
  .m-date { font-size:8pt; color:#888; }
  .m-company { font-size:8.5pt; color:#666; margin:2px 0 4px; }
  .m-desc { font-size:8.5pt; color:#555; line-height:1.6; }
  .m-skill { display:flex; align-items:center; justify-content:space-between; margin-bottom:9px; font-size:9pt; }
  .m-dots { display:flex; gap:4px; }
  .m-dot { width:9px; height:9px; border-radius:50%; }
  .m-lang { display:flex; justify-content:space-between; margin-bottom:7px; padding-bottom:7px; border-bottom:1px solid #f0f0f0; }
  .m-lang-n { font-size:9pt; font-weight:600; }
  .m-lang-l { font-size:8pt; }
  .m-cert { margin-bottom:9px; }
  .m-cert strong { display:block; font-size:9pt; }
  .m-cert span { font-size:8pt; color:#777; }

  /* MINIMAL */
  .n-wrap { display:flex; justify-content:space-between; align-items:flex-start; padding:40px 48px 18px; }
  .n-name { font-family:'Bricolage Grotesque',sans-serif; font-size:28pt; font-weight:800; letter-spacing:-1px; }
  .n-role { font-size:11pt; margin-top:4px; }
  .n-contacts { display:flex; flex-direction:column; gap:3px; text-align:right; font-size:8pt; }
  .n-line { height:2px; margin:0 48px 18px; }
  .n-summary { font-size:9.5pt; line-height:1.7; margin:0 48px 24px; }
  .n-sec { margin:0 48px 20px; }
  .n-sec.half { flex:1; min-width:160px; margin:0; }
  .n-title { font-size:7.5pt; font-weight:700; letter-spacing:3px; text-transform:uppercase; margin-bottom:10px; }
  .n-entry { margin-bottom:13px; padding-bottom:13px; border-bottom:1px solid #f0f0f0; }
  .n-entry-row { display:flex; justify-content:space-between; align-items:baseline; }
  .n-entry-row strong { font-size:10pt; font-weight:700; }
  .n-entry-row span { font-size:8pt; color:#999; }
  .n-sub { font-size:8.5pt; color:#777; margin-top:2px; }
  .n-desc { font-size:8.5pt; color:#555; line-height:1.6; margin-top:4px; }
  .n-bottom { display:flex; gap:28px; flex-wrap:wrap; margin:0 48px; padding-top:18px; border-top:1px solid #eee; }
  .n-tags { display:flex; flex-wrap:wrap; gap:5px; }
  .n-tag { font-size:8pt; padding:2px 9px; border:1px solid #ddd; color:#444; border-radius:2px; }
  .n-lang { font-size:9pt; color:#444; margin-bottom:5px; }

  /* CLASSIC */
  .c-head { padding:32px 36px; color:#fff; }
  .c-name { font-family:'Lora',serif; font-size:24pt; font-weight:600; }
  .c-role { font-size:11pt; opacity:.85; margin:5px 0 14px; }
  .c-contacts { display:flex; gap:18px; flex-wrap:wrap; font-size:8.5pt; opacity:.9; }
  .c-body { display:grid; grid-template-columns:1fr 190px; }
  .c-main { padding:24px 28px 24px 36px; border-right:1px solid #eee; }
  .c-side { padding:24px 20px; background:#f9f9f9; }
  .c-sec { margin-bottom:20px; }
  .c-title { font-size:8.5pt; font-weight:700; letter-spacing:2px; text-transform:uppercase; margin-bottom:10px; padding-bottom:5px; }
  .c-text { font-size:9.5pt; color:#444; line-height:1.7; }
  .c-text.sm { font-size:9pt; margin-top:4px; }
  .c-entry { display:flex; gap:10px; margin-bottom:12px; align-items:flex-start; }
  .c-dot { width:8px; height:8px; border-radius:50%; flex-shrink:0; margin-top:5px; }
  .c-entry-title { font-size:10pt; font-weight:700; }
  .c-entry-title em { font-style:normal; font-weight:400; color:#555; }
  .c-entry-date { font-size:8pt; color:#999; margin:2px 0; }
  .c-skill { margin-bottom:9px; font-size:9pt; }
  .c-skill span { display:block; margin-bottom:3px; font-weight:500; }
  .c-bar { height:4px; background:#e5e5e5; border-radius:2px; overflow:hidden; }
  .c-bar div { height:100%; border-radius:2px; }
  .c-lang { display:flex; justify-content:space-between; font-size:9pt; margin-bottom:7px; }
  .c-lang strong { color:#111; }
  .c-lang span { color:#777; font-size:8pt; }
  .c-cert { margin-bottom:9px; }
  .c-cert strong { display:block; font-size:9pt; color:#111; }
  .c-cert span { font-size:8pt; color:#777; }

  /* PRINT */
  @media print {
    :global(body) { background:white !important; }
    :global(#form-panel) { display:none !important; }
    :global(.builder) { display:block !important; height:auto !important; }
    :global(.preview) { display:block !important; padding:0 !important; overflow:visible !important; background:white !important; height:auto !important; }
    :global(.preview-inner) { max-width:100% !important; }
    :global(.cv) { box-shadow:none !important; min-height:auto !important; }
    :global(.ph) { display:none !important; }
  }

  @media (max-width:900px) {
    .builder { grid-template-columns:1fr; height:auto; }
    .preview { padding:16px; }
  }
</style>