<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  let svg;
  const width = 800;
  const height = 600;

  onMount(() => {
    const svgEl = d3.select(svg)
      .attr('width', width)
      .attr('height', height);

    // Hill data: starts high left, goes low right
    const hillData = Array.from({ length: 50 }, (_, i) => {
      const t = i / 49;
      const x = t * width;

      const y = 150 + 300 * Math.pow(t, 1.5);
      return { x, y };
    });

    const hill = d3.line()
      .x(d => d.x)
      .y(d => d.y)
      .curve(d3.curveBasis);

    svgEl.append('path')
      .datum(hillData)
      .attr('d', hill)
      .attr('fill', 'none')
      .attr('stroke', '#654321')
      .attr('stroke-width', 3);

    // House at top-left (consists of a rectangle and a polygon triangle thing)
    const houseX = 30;
    const houseY = hillData[0].y - 10;

    svgEl.append('rect')
      .attr('x', houseX)
      .attr('y', houseY - 40)
      .attr('width', 50)
      .attr('height', 40)
      .attr('fill', '#B22222');

    svgEl.append('polygon')
      .attr('points', `${houseX - 10},${houseY - 40} ${houseX + 25},${houseY - 70} ${houseX + 60},${houseY - 40}`)
      .attr('fill', '#8B0000');

      svgEl.append('path')
        .datum([...hillData, { x: width, y: height }, { x: 0, y: height }])
        .attr('d', d3.line().x(d => d.x).y(d => d.y).curve(d3.curveBasis))
        .attr('fill', '#c2b280') // ground color
        .attr('stroke', 'none');

    // Dots on the hill
    const speeds = [0.1, 0.2, 0.3, 0.4];
    const dots = speeds.map((speed, i) => ({
      index: 0,
      speed,
      circle: svgEl.append('circle')
        .attr('r', 6)
        .attr('fill', d3.schemeCategory10[i % 10])
    }));

    d3.timer(() => {
      dots.forEach(dot => {
        dot.index = (dot.index + dot.speed) % hillData.length;
        const point = hillData[Math.floor(dot.index)];
        dot.circle
          .attr('cx', point.x)
          .attr('cy', point.y - 5);
      });
    });
  });
</script>

<style>
  svg {
    display: block;
    margin: 0 auto;
  }

  body {
    height: 200vh;
    background: linear-gradient(#87CEEB, #ffffff);
    font-family: sans-serif;
  }

  main {
    text-align: center;
    padding-top: 2rem;
  }
</style>

<main>
  <h1>Scroll to see the house on the hill</h1>
  <svg bind:this={svg}></svg>
</main>
