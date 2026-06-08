<script>
  import driversData from '$lib/data/drivers.json';

  // ── Vilka specs visas i jämförelsetabellen ────────────
  const specCols = [
    { key: 'diameter',    label: 'Ø (tum)'  },
    { key: 'impedance',   label: 'Z (Ω)'    },
    { key: 'sensitivity', label: 'SPL (dB)' },
    { key: 'fs',          label: 'fS (Hz)'  },
    { key: 'qts',         label: 'Qts'      },
    { key: 'xmax',        label: 'Xmax (mm)'},
    { key: 'power',       label: 'P (W)'    },
  ];

  const typeLabels = {
    subwoofer:   'Subbas',
    'mid-bass':  'Mid-bas',
    midrange:    'Mellanregister',
    tweeter:     'Diskant',
    compression: 'Compression',
  };

  // ── State ─────────────────────────────────────────────
  let selected = $state([]);   // max 4 driver-ids
  let search   = $state('');

  let filtered = $derived(
    driversData.filter(d =>
      d.name.toLowerCase().includes(search.toLowerCase()) ||
      d.brand.toLowerCase().includes(search.toLowerCase())
    )
  );

  let compared = $derived(
    driversData.filter(d => selected.includes(d.id))
  );

  function toggle(id) {
    if (selected.includes(id)) {
      selected = selected.filter(s => s !== id);
    } else if (selected.length < 4) {
      selected = [...selected, id];
    }
  }

  // ── Highlight best value per column ───────────────────
  function bestVal(key) {
    const vals = compared.map(d => d[key]).filter(v => v != null);
    if (!vals.length) return null;
    // Higher is better for sensitivity & power; lower is better for fs
    if (key === 'fs') return Math.min(...vals);
    return Math.max(...vals);
  }
</script>

<!-- ── Hero ───────────────────────────────────────── -->
<section class="hero">
  <h1>Jäm<span class="accent">för</span></h1>
  <p>Välj upp till 4 drivers och jämför specs sida vid sida.</p>
</section>

<!-- ── Picker ─────────────────────────────────────── -->
<div class="picker-section">
  <input
    type="search"
    placeholder="Sök driver…"
    bind:value={search}
  />

  <div class="picker-list">
    {#each filtered as d (d.id)}
      <button
        class="picker-item"
        class:picked={selected.includes(d.id)}
        class:disabled={!selected.includes(d.id) && selected.length >= 4}
        onclick={() => toggle(d.id)}
      >
        <span class="pi-brand">{d.brand}</span>
        <span class="pi-name">{d.name}</span>
        <span class="pi-badge">{typeLabels[d.type] ?? d.type}</span>
        {#if selected.includes(d.id)}
          <span class="pi-check">✓</span>
        {/if}
      </button>
    {/each}
  </div>
</div>

<!-- ── Compare table ──────────────────────────────── -->
{#if compared.length > 0}
  <div class="table-wrap">
    <table>
      <thead>
        <tr>
          <th class="row-label">Spec</th>
          {#each compared as d}
            <th>
              <button class="remove-btn" onclick={() => toggle(d.id)}>✕</button>
              <div class="th-brand">{d.brand}</div>
              <div class="th-name">{d.name}</div>
              <div class="th-type">{typeLabels[d.type] ?? d.type}</div>
            </th>
          {/each}
        </tr>
      </thead>
      <tbody>
        {#each specCols as col}
          {@const best = bestVal(col.key)}
          <tr>
            <td class="row-label">{col.label}</td>
            {#each compared as d}
              {@const val = d[col.key]}
              <td class:best={val != null && val === best}>
                {val != null ? val : '—'}
              </td>
            {/each}
          </tr>
        {/each}
        <!-- Description row -->
        <tr class="desc-row">
          <td class="row-label">Info</td>
          {#each compared as d}
            <td class="desc-cell">{d.description}</td>
          {/each}
        </tr>
      </tbody>
    </table>
  </div>
{:else}
  <p class="empty">Välj minst en driver ovan för att starta jämförelsen.</p>
{/if}

<style>
  /* ── Hero ────────────────────────────────────── */
  .hero {
    margin-bottom: 2rem;
  }

  h1 {
    font-size: clamp(2rem, 5vw, 3.5rem);
    font-weight: bold;
    letter-spacing: -0.02em;
    line-height: 1;
    margin-bottom: 0.4rem;
  }

  .accent { color: #c8a96e; }

  .hero p {
    color: #888;
    font-size: 0.9rem;
    letter-spacing: 0.03em;
  }

  /* ── Picker ──────────────────────────────────── */
  .picker-section {
    margin-bottom: 2.5rem;
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
    margin-bottom: 1rem;
    transition: border-color 0.15s;
  }

  input[type="search"]:focus { border-color: #c8a96e; }

  .picker-list {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  .picker-item {
    background: #1a1a1a;
    border: 1px solid #2a2a2a;
    color: #888;
    padding: 0.4rem 0.85rem;
    font-family: inherit;
    font-size: 0.75rem;
    letter-spacing: 0.06em;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    transition: all 0.15s;
    position: relative;
  }

  .picker-item:hover:not(.disabled) {
    border-color: #666;
    color: #e8e4dc;
  }

  .picker-item.picked {
    background: #1e1a10;
    border-color: #c8a96e;
    color: #c8a96e;
  }

  .picker-item.disabled {
    opacity: 0.3;
    cursor: not-allowed;
  }

  .pi-brand {
    font-size: 0.65rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: #555;
  }

  .picker-item.picked .pi-brand { color: #a07840; }

  .pi-name { font-weight: bold; }

  .pi-badge {
    font-size: 0.6rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    border: 1px solid #333;
    padding: 0.1rem 0.4rem;
    color: #555;
  }

  .picker-item.picked .pi-badge {
    border-color: #5a4020;
    color: #a07840;
  }

  .pi-check {
    color: #c8a96e;
    font-size: 0.7rem;
  }

  /* ── Table ───────────────────────────────────── */
  .table-wrap {
    overflow-x: auto;
    border: 1px solid #1e1e1e;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.85rem;
  }

  th, td {
    padding: 0.9rem 1.1rem;
    text-align: left;
    border-bottom: 1px solid #1a1a1a;
    vertical-align: top;
  }

  th {
    background: #111;
    color: #e8e4dc;
    font-weight: normal;
    letter-spacing: 0.04em;
    border-bottom: 2px solid #2a2a2a;
    position: relative;
  }

  td { color: #c8c4bc; }

  .row-label {
    color: #555;
    font-size: 0.7rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    white-space: nowrap;
    background: #0e0e0e;
    min-width: 110px;
  }

  td.best {
    color: #c8a96e;
    font-weight: bold;
  }

  /* header cell content */
  .th-brand {
    font-size: 0.65rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: #555;
    margin-bottom: 0.2rem;
  }

  .th-name {
    font-size: 1rem;
    font-weight: bold;
    letter-spacing: 0.02em;
    color: #e8e4dc;
  }

  .th-type {
    font-size: 0.65rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: #c8a96e;
    margin-top: 0.25rem;
  }

  .remove-btn {
    position: absolute;
    top: 0.4rem;
    right: 0.5rem;
    background: none;
    border: none;
    color: #444;
    font-size: 0.7rem;
    cursor: pointer;
    padding: 0.2rem 0.3rem;
    transition: color 0.15s;
    font-family: inherit;
  }

  .remove-btn:hover { color: #e8e4dc; }

  tr:last-child td { border-bottom: none; }

  tr:hover td { background: #111; }
  tr:hover .row-label { background: #0e0e0e; }

  .desc-row td { background: #080808; }
  .desc-row:hover td { background: #0d0d0d; }

  .desc-cell {
    font-size: 0.75rem;
    color: #555;
    line-height: 1.55;
    min-width: 180px;
    max-width: 240px;
  }

  /* ── Empty ───────────────────────────────────── */
  .empty {
    text-align: center;
    color: #444;
    padding: 3rem;
    font-size: 0.9rem;
    letter-spacing: 0.05em;
  }
</style>