<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    let props = $props();

    let svg;
    let isHoveringAnyDot = null;
    let hoveringSmoothFactor = 0 // 0 = full speed, 1 = full pause
    const width = 800;
    const height = 600;

    onMount(() => {
      const svgEl = d3.select(svg)
        .attr('width', width)
        .attr('height', height);

      // Steep cliff curve
      const hillData = Array.from({ length: 50 }, (_, i) => {
        const t = i / 49;
        const x = t * width;
        const y = 100 + 500 * Math.pow(t, 3);
        return { x, y };
      });

      // Fill under hill
      svgEl.append('path')
        .datum([...hillData, { x: width, y: height }, { x: 0, y: height }])
        .attr('d', d3.line().x(d => d.x).y(d => d.y).curve(d3.curveBasis))
        .attr('fill', '#a0522d');

      // House (Consists of a rectangle and a polygon)
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

      const dotInfo = [
        { id: 'A', speed: 0.05, color: '#1f77b4', description: 'Blue dot info' },
        { id: 'B', speed: 0.08, color: '#ff7f0e', description: 'Orange dot info' },
        { id: 'C', speed: 0.11, color: '#2ca02c', description: 'Green dot info' },
        { id: 'D', speed: 0.14, color: '#d62728', description: 'Red dot info' },
      ];

      const dots = dotInfo.map(dot => ({
          ...dot,
          index: 0,
          circle: svgEl.append('circle')
            .attr('r', 20)
            .attr('fill', dot.color)
            .style('cursor', 'pointer')
            .on('mouseover', () => {
              isHoveringAnyDot = true;
              props.onHoverDot(dot);
            })
            .on('mouseout', () => {
              isHoveringAnyDot = false;
              props.onHoverDot(null);
            })
        }));

      d3.timer(() => {
        const target = isHoveringAnyDot ? 1 : 0;
        hoveringSmoothFactor += (target - hoveringSmoothFactor) * 0.1;

        dots.forEach(dot => {
          const effectiveSpeed = dot.speed * (1 - hoveringSmoothFactor); // slows down smoothly
          dot.index = (dot.index + effectiveSpeed) % hillData.length;

          const point = hillData[Math.floor(dot.index)];
          dot.circle
            .attr('cx', point.x)
            .attr('cy', point.y - 5);
      });
      });
    });
  </script>

  <svg bind:this={svg}></svg>
