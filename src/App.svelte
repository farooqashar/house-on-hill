<script>
  import Hill from './lib/Hill.svelte';

  let hoveredDot = null;

  const dots = [
    { id: 'A', speed: 0.05, color: '#1f77b4', description: 'Whites' },
    { id: 'B', speed: 0.08, color: '#ff7f0e', description: 'Blacks' },
    { id: 'C', speed: 0.11, color: '#2ca02c', description: 'Latinos' },
    { id: 'D', speed: 0.14, color: '#d62728', description: 'Asians' }
  ];

  const handleHoverDot = (dot) => {
    hoveredDot = dot;
  };
</script>

<style>
  .page {
    display: flex;
    flex-direction: column;
    width: 100vw;
    height: 100vh;
    overflow-y: scroll;
    scroll-snap-type: y mandatory;
  }

  .section {
    height: 100vh;
    scroll-snap-align: start;
    position: relative;
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
  }
</style>

<div class="page">
  <!-- Scene 0: All dots rolling with hover showing dot name -->
  <div class="section">
    <Hill dots={dots} freezeAfterDone={false} showInfoFor={null} onHoverDot={handleHoverDot} />
    {#if hoveredDot}
      <div class="info-label">
         {hoveredDot.description}
      </div>
    {/if}
  </div>

  <!-- Scene 1–4: Each ball rolls down alone with info -->
  {#each dots as dot, i}
    <div class="section">
      <Hill dots={[dot]} freezeAfterDone={false} showInfoFor={dot.id} onHoverDot={() => {}} />
      <div class="info-label">
        <h2>{dot.description}</h2>
        <p>CHART HERE OR INFO</p>
      </div>
    </div>
  {/each}
</div>
