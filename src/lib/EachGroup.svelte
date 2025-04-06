<script>
      let props = $props();
</script>
<style>
  .hill-info-panel {
  position: absolute;
  top: 1rem;
  right: 2rem;
  background: hsl(0, 80%, 47%);
  color: #f4f4f4;
  padding: 1rem;
  border: 1px solid #444;
  border-radius: 8px;
  max-width: 300px;
  font-family: 'Georgia', serif;
}

.hill-info-panel h2 {
  margin: 0 0 0.5rem 0;
  font-size: 1.5rem;
}

.hill-info-panel p {
  margin: 0;
  font-size: 1rem;
}
</style>

      <div class="hill-info-panel">
        <h2>{props.dot.description}</h2>
      </div>
