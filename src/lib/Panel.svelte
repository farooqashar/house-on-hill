<!-- Panel.svelte -->
<script>
    import { createEventDispatcher } from 'svelte';
  
    export let selectedGroups;
    export let groupColors;
  
    const dispatch = createEventDispatcher();
  
    function toggle(group) {
      const updated = { ...selectedGroups, [group]: !selectedGroups[group] };
      dispatch('changeGroups', updated);
    }
  </script>
  
  <div class="panel">
    <h2>The demographics of evicted Boston residents</h2>
    <p>Each dot corresponds to an evicted resident corresponding to the demographic below.</p>
    {#each Object.entries(selectedGroups) as [group, isChecked]}
      <label style="color: {groupColors[group]};">
        <input type="checkbox" checked={selectedGroups[group]} on:change={() => toggle(group)} />
        {group}
      </label>
    {/each}
    <p>A resident who lived in a household earning less than $50,000 is marked with a circle; those who lived in a household earning at least $50,000 are marked with a square.</p>
  </div>
  
  <style>
    .panel {
      /* position: absolute;
      top: 1rem;
      left: 1rem;
      background: white;
      padding: 1rem;
      border-radius: 8px;
      font-family: sans-serif; */
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

    .panel h2 {
    margin: 0 0 0.5rem 0;
    font-size: 1.5rem;
    border-bottom: 1px solid #555;
    padding-bottom: 0.3rem;
  }

  .panel p {
    font-style: italic;
    margin: 0.5rem 0 1rem;
    font-size: 1rem;
    line-height: 1.4;
  }

  .panel label {
  display: block;
  margin-bottom: 0.5rem;
}
  </style>