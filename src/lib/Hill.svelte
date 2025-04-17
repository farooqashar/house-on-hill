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

  let svg;
  const width = 1000;
  const height = window.innerHeight;

  const hillData = Array.from({ length: 50 }, (_, i) => {
    const t = i / 49;
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

    // House (rectangle and polygon)
    const houseX = 30;
    const houseY = hillData[0].y;

    svgEl
      .append("rect")
      .attr("x", houseX)
      .attr("y", houseY - 40)
      .attr("width", 50)
      .attr("height", 40)
      .attr("fill", "##E2CD85");

    svgEl
      .append("polygon")
      .attr(
        "points",
        `${houseX - 10},${houseY - 40} ${houseX + 25},${houseY - 70} ${houseX + 60},${houseY - 40}`
      )
      .attr("fill", "#8B0000");

      
    dotStates = activeDots.map((dot) => {
      const circle = svgEl
        .append("circle")
        .attr("r", 20)
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
            dot.index += dot.speed;
          } else {
            dot.done = true;
          }
        }

        const point =
          hillData[Math.min(Math.floor(dot.index), hillData.length - 1)];

        dot.circle.attr("cx", point.x).attr("cy", point.y - 5);

        if (dot.label) {
          dot.label.attr("x", point.x).attr("y", point.y - 30);
        }
      });
    });
  });
</script>

<svg bind:this={svg} style="display: block; width={width} height={height}"></svg>

{#if showInfoFor}
  {#each activeDots as dot}
    {#if dot.id === showInfoFor}
      <EachGroup {dot} />
    {/if}
  {/each}
{/if}

<style>
</style>
