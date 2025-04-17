<script>
  import * as d3 from "d3";
  import { onMount } from "svelte";
  import EachGroup from "./EachGroup.svelte";

  const {
    activeDots = [],
    showInfoFor = null,
    freezeAfterDone = false,
    onHoverDot = () => {},
  } = $props();

  // svelte-ignore non_reactive_update
  let textboxText =
    "This hill represents the journey toward housing stability. Each dot is a demographic group, and their speed shows how quickly they're displaced—faster movement means a higher risk of eviction.";

  setTimeout(() => {
    textboxText =
      "Groups that move faster down the hill are more vulnerable to eviction. Slower dots represent more stability.";
  }, 2000);

  let svg;
  const width = 1000;
  const height = window.innerHeight;

  const hillData = Array.from({ length: 100 }, (_, i) => {
    const t = i / 99;
    const x = t * width;
    const y = 210 + 700 * Math.pow(t, 3);
    return { x, y };
  });

  let dotStates = [];

  // Rebuild the hill and dots when props change
  $effect(() => {
    if (!svg || !activeDots.length) return;

    const svgEl = d3
      .select(svg)
      .html("")
      .attr("width", width)
      .attr("height", height);

    // Hill path
    svgEl
      .append("path")
      .datum([...hillData, { x: width, y: height }, { x: 0, y: height }])
      .attr(
        "d",
        d3
          .line()
          .x((d) => d.x)
          .y((d) => d.y)
          .curve(d3.curveBasis)
      )
      .attr("fill", "#a0522d");

    // House base
    const houseX = 30;
    const houseY = hillData[0].y;

    svgEl
      .append("rect")
      .attr("x", houseX)
      .attr("y", houseY - 40)
      .attr("width", 50)
      .attr("height", 40)
      .attr("fill", "#E2CD85")
      .attr("stroke", "#bfa76f")
      .attr("stroke-width", 1);

    // Roof
    svgEl
      .append("polygon")
      .attr(
        "points",
        `
    ${houseX - 10},${houseY - 40}
    ${houseX + 25},${houseY - 70}
    ${houseX + 60},${houseY - 40}
  `
      )
      .attr("fill", "#8B0000");

    // Chimney
    svgEl
      .append("rect")
      .attr("x", houseX + 35)
      .attr("y", houseY - 65)
      .attr("width", 8)
      .attr("height", 20)
      .attr("fill", "#5a1a00");

    // Left window
    svgEl
      .append("rect")
      .attr("x", houseX + 6)
      .attr("y", houseY - 30)
      .attr("width", 10)
      .attr("height", 10)
      .attr("fill", "#87CEEB")
      .attr("stroke", "#444")
      .attr("stroke-width", 0.5);

    // Right window
    svgEl
      .append("rect")
      .attr("x", houseX + 34)
      .attr("y", houseY - 30)
      .attr("width", 10)
      .attr("height", 10)
      .attr("fill", "#87CEEB")
      .attr("stroke", "#444")
      .attr("stroke-width", 0.5);

    // Door
    svgEl
      .append("rect")
      .attr("x", houseX + 20)
      .attr("y", houseY - 20)
      .attr("width", 10)
      .attr("height", 20)
      .attr("fill", "#7B3F00")
      .attr("stroke", "#4a2c00")
      .attr("stroke-width", 0.5);

    // Dot setup
    dotStates = activeDots.map((dot) => {
      const circle = svgEl
        .append("circle")
        .attr("r", 28)
        .attr("fill", dot.color)
        .style("cursor", "pointer")
        .on("mouseover", () => onHoverDot(dot))
        .on("mouseout", () => onHoverDot(null));

      const label =
        showInfoFor === null
          ? svgEl
              .append("text")
              .text(dot.description)
              .attr("font-size", "12px")
              .attr("fill", "#000")
              .attr("text-anchor", "middle")
          : null;

      return {
        ...dot,
        index: 0,
        done: false,
        circle,
        label,
      };
    });
  });

  // Animate dots
  onMount(() => {
    d3.timer(() => {
      if (!svg || !dotStates.length) return;

      dotStates.forEach((dot) => {
        if (!freezeAfterDone || !dot.done) {
          if (dot.index < hillData.length - 1) {
            dot.index += dot.speed * 0.8;
          } else {
            dot.done = true;
          }
        }

        const i = Math.floor(dot.index);
        const t = dot.index - i;
        const p1 = hillData[i];
        const p2 = hillData[Math.min(i + 1, hillData.length - 1)];

        // interpolate for smooth movement
        const x = d3.interpolateNumber(p1.x, p2.x)(t);
        const y = d3.interpolateNumber(p1.y, p2.y)(t);

        dot.circle.attr("cx", x).attr("cy", y - 5);

        if (dot.label) {
          dot.label.attr("x", x).attr("y", y - 30);
        }
      });
    });
  });
</script>

<svg bind:this={svg} style="display: block; width={width} height={height}"
></svg>

{#if showInfoFor}
  {#each activeDots as dot}
    {#if dot.id === showInfoFor}
      <EachGroup {dot} />
    {/if}
  {/each}
{:else if activeDots.length > 1}
  <div class="textbox">{textboxText}</div>
{/if}

<style>
  .textbox {
    position: absolute;
    top: 8rem;
    right: 2rem;
    max-width: 320px;
    background: rgba(255, 255, 255, 0.95);
    color: #111;
    padding: 1rem 1.2rem;
    border-radius: 10px;
    font-size: 0.95rem;
    font-family: Georgia, serif;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.2);
    z-index: 15;
    text-align: left;
  }
</style>
