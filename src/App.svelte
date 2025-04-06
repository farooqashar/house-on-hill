<script>
  import Hill from './lib/Hill.svelte';
  import InfoPanel from './lib/InfoPanel.svelte';

  let hoveredDot = null;

  const handleHoverDot = (dot)  => {
    hoveredDot = dot;
  }
</script>

<style>
  .container {
    display: flex;
    flex-direction: row;
    height: 100vh;
    overflow: hidden;
  }

  .main {
    flex: 2;
    background: linear-gradient(#87CEEB, #ffffff);
  }

  .side {
    flex: 1;
    background: #f7f7f7;
    padding: 1rem;
    border-left: 2px solid #ddd;
    overflow-y: auto;
  }
</style>

<div class="container">
  <div class="main">
    <Hill onHoverDot={handleHoverDot} />
  </div>
  <div class="side">
    <InfoPanel {hoveredDot} />
  </div>
</div>
