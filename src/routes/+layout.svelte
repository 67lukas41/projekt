<script>
  import { page } from '$app/stores';

  const navLinks = [
    { href: '/',           label: 'Alla drivers' },
    { href: '/compare',    label: 'Jämför' },
    { href: '/box-guide',  label: 'Lådguide' },
  ];
</script>

<div class="site">
  <header>
    <a href="/" class="logo">
      <span class="logo-icon">◈</span>
      <span class="logo-text">Högtalarguiden</span>
    </a>

    <nav>
      {#each navLinks as link}
        <a
          href={link.href}
          class:active={$page.url.pathname === link.href}
        >
          {link.label}
        </a>
      {/each}
    </nav>
  </header>

  <main>
    <slot />
  </main>

  <footer>
    <p>Högtalarguiden — Lär dig om drivers, jämför och bygg din egna låda.</p>
  </footer>
</div>

<style>
  :global(*, *::before, *::after) {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  :global(body) {
    background-color: #0e0e0e;
    color: #e8e4dc;
    font-family: 'Courier New', Courier, monospace;
    min-height: 100vh;
  }

  :global(a) {
    color: inherit;
    text-decoration: none;
  }

  .site {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1.2rem 2rem;
    border-bottom: 1px solid #2a2a2a;
    position: sticky;
    top: 0;
    background: #0e0e0e;
    z-index: 100;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    font-size: 1.1rem;
    font-weight: bold;
    letter-spacing: 0.05em;
  }

  .logo-icon {
    color: #c8a96e;
    font-size: 1.3rem;
  }

  nav {
    display: flex;
    gap: 2rem;
  }

  nav a {
    font-size: 0.85rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: #888;
    transition: color 0.15s;
    padding-bottom: 2px;
    border-bottom: 1px solid transparent;
  }

  nav a:hover {
    color: #e8e4dc;
  }

  nav a.active {
    color: #c8a96e;
    border-bottom-color: #c8a96e;
  }


  main {
    flex: 1;
    padding: 2.5rem 2rem;
    max-width: 1200px;
    width: 100%;
    margin: 0 auto;
  }


  footer {
    padding: 1.5rem 2rem;
    border-top: 1px solid #2a2a2a;
    text-align: center;
    font-size: 0.75rem;
    color: #444;
    letter-spacing: 0.05em;
  }
</style>