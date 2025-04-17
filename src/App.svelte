<script>
  import Hill from "./lib/Hill.svelte";
  import BigScatter from "./lib/BigScatter.svelte";
  import EvictionIntro from "./lib/EvictionIntro.svelte";
  import { onMount } from "svelte";
  import { allDots, groupColors, populationData } from "./utils/util";
  import Panel from "./lib/Panel.svelte";

  let selectedGroups = {
    white: true,
    black: true,
    asian: true,
    latino: true,
    native: true,
    islander: true,
    other: true,
    under50k: true,
    over50k: true,
  };

  function updateGroups(newSelection) {
    selectedGroups = { ...newSelection };
  }

  let currentSection = 0;
  let triggerElements = [];

  onMount(() => {
    triggerElements = Array.from(document.querySelectorAll(".trigger"));

    // Figuring out where in the web page you are
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          // console.log("going to next entry!");
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
  currentSection === 1
    ? allDots
    : currentSection > 1 && currentSection < 8
      ? [allDots[currentSection - 2]]
      : [];

$: showInfoFor =
  currentSection === 1 || currentSection === 8 || activeDots.length === 0
    ? null
    : activeDots[0].id;

$: freezeAfterDone = currentSection === 1;
</script>

<!-- Single persistent hill visual -->
<div class="page">
  <!-- console.log("this is currentSection: {currentSection}"); -->
  {#if currentSection < 8}
    <!-- console.log("this is currentSection: {currentSection}"); -->
    <Hill {activeDots} {showInfoFor} {freezeAfterDone} />
  {:else if currentSection === 8}
    <!-- console.log("made it this far"); -->
    <!-- <BigScatter/> -->
    <Panel
      {selectedGroups}
      {groupColors}
      on:changeGroups={(e) => updateGroups(e.detail)}
    />
    <!-- console.log("this is selectedGroups: {selectedGroups.white}, {selectedGroups.asian}, {selectedGroups.black},
                                          {selectedGroups.islander}, {selectedGroups.latino}, {selectedGroups.native}"); -->
    <BigScatter {selectedGroups} {groupColors} />
  {/if}
  <div class="arrow-controls">
    <button
      on:click={() => scrollToSection(currentSection - 1)}
      disabled={currentSection === 0}>↑</button
    >
    <button
      on:click={() => scrollToSection(currentSection + 1)}
      disabled={currentSection === 8}>↓</button
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
    <div class="trigger" data-section="5"></div>
    <div class="trigger" data-section="6"></div>
    <div class="trigger" data-section="7"></div>
    <div class="trigger" data-section="8"></div>
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
