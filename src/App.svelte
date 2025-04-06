<script>
  import Hill from './lib/Hill.svelte';
  import { onMount } from 'svelte';

  const allDots = [
    { id: 'A', speed: 0.05, color: '#1f77b4', description: 'Whites' },
    { id: 'B', speed: 0.08, color: '#ff7f0e', description: 'Black' },
    { id: 'C', speed: 0.11, color: '#2ca02c', description: 'Latinos' },
    { id: 'D', speed: 0.14, color: '#d62728', description: 'Asians' }
  ];

  let currentSection = 0;

  onMount(() => {
    // Figure out what section of the page you are on
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            const section = /** @type {HTMLElement} */ (entry.target);
            currentSection = parseInt(section.dataset.section || '0');
          }
        });
      },
      { threshold: 0.6 }
    );

    document.querySelectorAll('.trigger').forEach(el => observer.observe(el));
  });

  $: activeDots = currentSection === 0 ? allDots : [allDots[currentSection - 1]];
  $: showInfoFor = currentSection === 0 ? null : activeDots[0].id;
  $: freezeAfterDone = currentSection === 0;
</script>

<style>
  .page {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
  }

  .info-label {
    position: absolute;
    top: 1rem;
    right: 2rem;
    background: rgba(255, 255, 255, 0.9);
    padding: 1rem;
    border-radius: 8px;
    font-weight: bold;
    max-width: 300px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    pointer-events: none;
    z-index: 10;
  }

  .scroll-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    overflow-y: scroll;
    scroll-snap-type: y mandatory;
    z-index: 5;
  }

  .trigger {
    height: 100vh;
    scroll-snap-align: start;
    pointer-events: none;
  }
</style>

<div class="page">
  <Hill dots={activeDots} freezeAfterDone={freezeAfterDone} />

  {#if showInfoFor}
    <div class="info-label">
      <h2>Dot {showInfoFor}</h2>
      <p>{activeDots[0].description}</p>
    </div>
  {/if}

  <div class="scroll-overlay">
    <div class="trigger" data-section="0"></div>
    <div class="trigger" data-section="1"></div>
    <div class="trigger" data-section="2"></div>
    <div class="trigger" data-section="3"></div>
    <div class="trigger" data-section="4"></div>
  </div>
</div>
