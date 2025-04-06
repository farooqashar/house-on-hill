<script>
    export let hoveredDot;
  </script>

  {#if hoveredDot}
    <h2>Dot {hoveredDot.id}</h2>
    <p><strong>Speed:</strong> {hoveredDot.speed}</p>
    <p>{hoveredDot.description}</p>

    <div style="margin-top: 1rem;">
      <h3>Visualization</h3>
      <div style="width: 100%; height: 100px; background: #eee; border: 1px solid #ccc;">
        <p style="text-align: center; line-height: 100px;">[Chart here]</p>
      </div>
    </div>
  {:else}
    <p>Hover over a dot to see more info.</p>
  {/if}
