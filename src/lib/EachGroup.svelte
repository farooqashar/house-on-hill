<script>
  let props = $props();
</script>

{#if props.dot}
  <div class="hill-info-panel">
    <h2>{props.dot.description}</h2>
    <div class="quote">“{props.dot.anectode}”</div>
    <div class="stats">
      <strong>Proportion of evictions:</strong>
      {props.dot.proportion_of_evictions}
    </div>
    <img
      src={`https://raw.githubusercontent.com/farooqashar/house-on-hill/refs/heads/main/src/assets/${props.dot.eviction_trend}`}
      alt="Eviction trend for {props.dot.description}"
    />
  </div>
{/if}

<style>
  .hill-info-panel {
    position: absolute;
    top: 1rem;
    right: 2rem;
    background: #2a2a2a;
    color: #f4f4f4;
    padding: 1.2rem;
    border: 1px solid #444;
    border-radius: 8px;
    max-width: 320px;
    font-family: "Georgia", serif;
    z-index: 10;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.4);
  }

  .hill-info-panel h2 {
    margin: 0 0 0.5rem 0;
    font-size: 1.5rem;
    border-bottom: 1px solid #555;
    padding-bottom: 0.3rem;
  }

  .hill-info-panel .quote {
    font-style: italic;
    margin: 0.5rem 0 1rem;
    font-size: 1rem;
    line-height: 1.4;
  }

  .hill-info-panel .stats {
    font-size: 0.95rem;
    margin-bottom: 0.8rem;
  }

  .hill-info-panel img {
    width: 100%;
    border: 1px solid #555;
    border-radius: 4px;
    margin-top: 0.5rem;
  }
</style>
