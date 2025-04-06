<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  const { dots = [], showInfoFor = null, freezeAfterDone = false, onHoverDot = () => {} } = $props();

  let svg;
  const width = 800;
  const height = 600;

  const hillData = Array.from({ length: 50 }, (_, i) => {
    const t = i / 49;
    const x = t * width;
    const y = 100 + 500 * Math.pow(t, 3);
    return { x, y };
  });

  onMount(() => {
    const dotStates = dots.map(dot => ({ ...dot, index: 0, done: false }));

    // Making hill
    const svgEl = d3.select(svg)
      .attr('width', width)
      .attr('height', height)
      .html('');

    svgEl.append('path')
      .datum([...hillData, { x: width, y: height }, { x: 0, y: height }])
      .attr('d', d3.line().x(d => d.x).y(d => d.y).curve(d3.curveBasis))
      .attr('fill', '#a0522d');

    // Making a house (rectangle and polygon)
    const houseX = 30;
    const houseY = hillData[0].y;

    svgEl.append('rect')
      .attr('x', houseX)
      .attr('y', houseY - 40)
      .attr('width', 50)
      .attr('height', 40)
      .attr('fill', '#B22222');

    svgEl.append('polygon')
      .attr('points', `${houseX - 10},${houseY - 40} ${houseX + 25},${houseY - 70} ${houseX + 60},${houseY - 40}`)
      .attr('fill', '#8B0000');

    // Making dots logic

    dotStates.forEach(dot => {
      dot.circle = svgEl.append('circle')
        .attr('r', 20)
        .attr('fill', dot.color)
        .style('cursor', 'pointer')
        .on('mouseover', () => onHoverDot(dot))
        .on('mouseout', () => onHoverDot(null));
    });

    d3.timer(() => {
      let allDone = true;

      dotStates.forEach(dot => {
        if (!freezeAfterDone || !dot.done) {
          if (dot.index < hillData.length - 1) {
            dot.index += dot.speed;
            allDone = false;
          } else {
            dot.done = true;
          }
        }

        const point = hillData[Math.min(Math.floor(dot.index), hillData.length - 1)];
        dot.circle
          .attr('cx', point.x)
          .attr('cy', point.y - 5);
      });
    });
  });
</script>

<svg bind:this={svg}></svg>
