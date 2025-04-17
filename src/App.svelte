<script>
  import Hill from "./lib/Hill.svelte";
  import EvictionIntro from "./lib/EvictionIntro.svelte";
  import { onMount } from "svelte";
  import { allDots } from "./utils/util";

  let currentSection = 0;
  let triggerElements = [];

  onMount(() => {
    triggerElements = Array.from(document.querySelectorAll(".trigger"));

    // Figuring out where in the web page you are
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            const section = /** @type {HTMLElement} */ (entry.target);
            currentSection = parseInt(section.dataset.section || "0");
          }
        });
      },
      { threshold: 0.6 }
    );

    triggerElements.forEach((el) => observer.observe(el));

    window.addEventListener("keydown", handleKey);
  });

  const scrollToSection = (index) => {
    if (triggerElements[index]) {
      triggerElements[index].scrollIntoView({ behavior: "smooth" });
    }
  };

  const handleKey = (event) => {
    if (event.key === "ArrowDown") {
      event.preventDefault();
      scrollToSection(Math.min(currentSection + 1, triggerElements.length - 1));
    } else if (event.key === "ArrowUp") {
      event.preventDefault();
      scrollToSection(Math.max(currentSection - 1, 0));
    }
  };
  // changes dynamically
  $: activeDots =
    currentSection === 0 ? allDots : [allDots[currentSection - 1]];
  $: showInfoFor = currentSection === 0 ? null : activeDots[0].id;
  $: freezeAfterDone = currentSection === 0;
</script>

<!-- Single persistent hill visual -->
<div class="page">
  <Hill {activeDots} {showInfoFor} {freezeAfterDone} />

  <div class="arrow-controls">
    <button
      on:click={() => scrollToSection(currentSection - 1)}
      disabled={currentSection === 0}>↑</button
    >
    <button
      on:click={() => scrollToSection(currentSection + 1)}
      disabled={currentSection === 4}>↓</button
    >
  </div>

  <!-- Invisible scroll triggers -->
  <div class="scroll-overlay">
    <div class="trigger" data-section="0">
      <EvictionIntro />
    </div>
    <div class="trigger" data-section="1"></div>
    <div class="trigger" data-section="2"></div>
    <div class="trigger" data-section="3"></div>
    <div class="trigger" data-section="4"></div>
  </div>
</div>

<style>
  .page {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    font-family: "Georgia", "Times New Roman", serif;
    background-color: #949494;
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

  .arrow-controls {
    position: absolute;
    bottom: 2rem;
    right: 2rem;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    z-index: 10;
  }

  .arrow-controls button {
    background: #2a2a2a;
    color: #f4f4f4;
    border: 1px solid #444;
    border-radius: 4px;
    padding: 0.6rem 1rem;
    font-family: "Georgia", serif;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .arrow-controls button:disabled {
    opacity: 0.3;
    cursor: not-allowed;
  }
</style>
