<script>
  import driversData from '$lib/data/drivers.json';

  // ── Typ-etiketter på svenska ──────────────────────────
  const typeLabels = {
    subwoofer:   'Subbas',
    'mid-bass':  'Mid-bas',
    midrange:    'Mellanregister',
    tweeter:     'Diskant',
    compression: 'Compression-driver',

  };

  // ── State ─────────────────────────────────────────────
  let search       = $state('');
  let activeType   = $state('all');

  const types = ['all', ...Object.keys(typeLabels)];

  // ── Filtrering ────────────────────────────────────────
  let filtered = $derived(
    driversData.filter((d) => {
      const matchType   = activeType === 'all' || d.type === activeType;
      const matchSearch = d.name.toLowerCase().includes(search.toLowerCase())
                       || d.brand.toLowerCase().includes(search.toLowerCase());
      return matchType && matchSearch;
    })
  );
</script>

<!-- ── Rubrik ─────────────────────────────────────── -->
<section class="hero">
  <h1>Högtalar<span class="accent">guide</span></h1>
  <p>Galen hemsida för element gemförelse, Lisa ge mig A</p>
</section>

<!-- ── Filter ─────────────────────────────────────── -->
<div class="controls">
  <input
    type="search"
    placeholder="Sök här"
    bind:value={search}
  />

  <div class="type-filters">
    {#each types as t}
      <button
        class:active={activeType === t}
        onclick={() => activeType = t}
      >
        {t === 'all' ? 'Alla' : typeLabels[t]}
      </button>
    {/each}
  </div>
</div>

<!-- ── Resultaträknare ─────────────────────────────── -->
<p class="count">{filtered.length} drivers</p>

<!-- ── Grid ───────────────────────────────────────── -->
<div class="grid">
  {#each filtered as driver (driver.id)}
    <a href="/drivers/{driver.id}" class="card">
      <div class="card-header">
        <span class="brand">{driver.brand}</span>
        <span class="type-badge">{typeLabels[driver.type] ?? driver.type}</span>
      </div>

      <h2 class="model">{driver.name}</h2>

      <div class="specs">
        <div class="spec">
          <span class="spec-label">Ø</span>
          <span class="spec-value">{driver.diameter}"</span>
        </div>
        <div class="spec">
          <span class="spec-label">Z</span>
          <span class="spec-value">{driver.impedance} Ω</span>
        </div>
        <div class="spec">
          <span class="spec-label">SPL</span>
          <span class="spec-value">{driver.sensitivity} dB</span>
        </div>
        {#if driver.fs}
        <div class="spec">
          <span class="spec-label">fS</span>
          <span class="spec-value">{driver.fs} Hz</span>
        </div>
        {/if}
        {#if driver.xmax}
        <div class="spec">
          <span class="spec-label">Xmax</span>
          <span class="spec-value">{driver.xmax} mm</span>
        </div>
        {/if}
        <div class="spec">
          <span class="spec-label">P</span>
          <span class="spec-value">{driver.power} W</span>
        </div>
      </div>

      <p class="desc">{driver.description}</p>
    </a>
  {/each}
</div>

{#if filtered.length === 0}
  <p class="empty">Inga drivers matchar sökningen.</p>
{/if}

<style>
  /* ── Hero ────────────────────────────────────── */
  .hero {
    margin-bottom: 2.5rem;
  }

  h1 {
    font-size: clamp(2rem, 5vw, 3.5rem);
    font-weight: bold;
    letter-spacing: -0.02em;
    line-height: 1;
    margin-bottom: 0.5rem;
  }

  .accent {
    color: #c8a96e;
  }

  .hero p {
    color: #888;
    font-size: 0.95rem;
    letter-spacing: 0.03em;
  }

  /* ── Controls ────────────────────────────────── */
  .controls {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    margin-bottom: 1.5rem;
  }

  input[type="search"] {
    background: #1a1a1a;
    border: 1px solid #2a2a2a;
    color: #e8e4dc;
    padding: 0.7rem 1rem;
    font-family: inherit;
    font-size: 0.9rem;
    width: 100%;
    max-width: 400px;
    outline: none;
    transition: border-color 0.15s;
  }

  input[type="search"]:focus {
    border-color: #c8a96e;
  }

  .type-filters {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  button {
    background: none;
    border: 1px solid #2a2a2a;
    color: #888;
    padding: 0.35rem 0.85rem;
    font-family: inherit;
    font-size: 0.75rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.15s;
  }

  button:hover {
    border-color: #666;
    color: #e8e4dc;
  }

  button.active {
    background: #c8a96e;
    border-color: #c8a96e;
    color: #0e0e0e;
  }

  /* ── Count ───────────────────────────────────── */
  .count {
    font-size: 0.75rem;
    color: #444;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 1.5rem;
  }

  /* ── Grid ────────────────────────────────────── */
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1px;
    background: #1e1e1e;
    border: 1px solid #1e1e1e;
  }

  /* ── Card ────────────────────────────────────── */
  .card {
    background: #0e0e0e;
    padding: 1.5rem;
    display: flex;
    flex-direction: column;
    gap: 0.8rem;
    transition: background 0.15s;
  }

  .card:hover {
    background: #141414;
  }

  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .brand {
    font-size: 0.7rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: #555;
  }

  .type-badge {
    font-size: 0.65rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: #c8a96e;
    border: 1px solid #3a3020;
    padding: 0.15rem 0.5rem;
  }

  .model {
    font-size: 1.15rem;
    font-weight: bold;
    letter-spacing: 0.02em;
    color: #e8e4dc;
  }

  /* ── Specs ───────────────────────────────────── */
  .specs {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0.5rem 0;
    border-top: 1px solid #1e1e1e;
    border-bottom: 1px solid #1e1e1e;
    padding: 0.8rem 0;
  }

  .spec {
    display: flex;
    flex-direction: column;
    gap: 0.1rem;
  }

  .spec-label {
    font-size: 0.6rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: #444;
  }

  .spec-value {
    font-size: 0.9rem;
    color: #c8c4bc;
  }

  /* ── Description ─────────────────────────────── */
  .desc {
    font-size: 0.78rem;
    color: #666;
    line-height: 1.5;
  }

  /* ── Empty state ─────────────────────────────── */
  .empty {
    text-align: center;
    color: #444;
    padding: 3rem;
    font-size: 0.9rem;
    letter-spacing: 0.05em;
  }
</style>  