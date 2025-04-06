<script>
  import Hill from './lib/Hill.svelte';
  import { onMount } from 'svelte';

  const allDots = [
    { id: 'A', speed: 0.05, color: '#1f77b4', description: 'Whites' },
    { id: 'B', speed: 0.08, color: '#ff7f0e', description: 'Blacks' },
    { id: 'C', speed: 0.11, color: '#2ca02c', description: 'Asians' },
    { id: 'D', speed: 0.14, color: '#d62728', description: 'Latinos' }
  ];

  let currentSection = 0;

  onMount(() => {
    // Figuring out where in the web page you are
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

  // changes dynamically
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
  font-family: 'Georgia', 'Times New Roman', serif;
  background-color: #fdb7b7;
  color: #f4f4f4;
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

<!-- Single persistent hill visual -->
<div class="page">
  <Hill {activeDots} {showInfoFor} {freezeAfterDone} />

  <!-- Invisible scroll triggers -->
  <div class="scroll-overlay">
    <div class="trigger" data-section="0"></div>
    <div class="trigger" data-section="1"></div>
    <div class="trigger" data-section="2"></div>
    <div class="trigger" data-section="3"></div>
    <div class="trigger" data-section="4"></div>
  </div>
</div>
