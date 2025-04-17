<script>
  import * as d3 from "d3";
  import { afterUpdate, onMount } from "svelte";
  import Panel from "./Panel.svelte";
  export let selectedGroups;
  export let groupColors;
  let canvas;

  // in this component, i want to take in width, height, top left x, top left y, color, population and just plot the dots.
  // in the big scatter component, i want to take in the population breakdowns that i'm plotting, and output the appropriate width,
  //    height, top left x, top left y, color, and population for each population (to go into subcomponent)
  // the panel component will contain the checkboxes with the population groups we want to consider and will send the desired populations.
  // main app has population numbers for each subgroup.


  const width = window.innerWidth;
  const height = window.innerHeight;
  const popData =
    {
      low_inc: 7423,
      high_inc: 4558,
      white: 3747,
      black: 4189,
      asian: 939,
      latino: 2525,
      native: 32,
      islander: 3,
      other: 546,
      white_low: 1664,
      white_high: 2083,
      black_low: 2992,
      black_high: 1197,
      asian_low: 605,
      asian_high: 334,
      latino_low: 1784,
      latino_high: 741,
      other_low: 354,
      other_high: 192,
      native_low: 22,
      native_high: 10,
      islander_low: 2,
      islander_high: 1
    };
  // const popData = 
  // {
  //   white_prop: 3747,
  //   black_prop: 4189,
  //   asian_prop: 939,
  //   hispanic_prop: 2525,
  //   pacific_islander_prop: 3,
  //   native_american_prop: 32,
  //   other_prop: 546,
  //   total_pop: 643565,
  //   income_less_50k: 7423,
  //   income_over_50k: 4558
  // }

  // const tractsData = Array.from(
  //   {length: 100}, (_, i) =>
  //   ({
  //       white_prop: random_prop_gen(),
  //       asian_prop: random_prop_gen(),
  //       hispanic_prop: random_prop_gen(),
  //       pacific_islander_prop: random_prop_gen(),
  //       native_american_prop: random_prop_gen(),
  //       black_prop: random_prop_gen(),
  //       total_pop: d3.randomInt(100, 1000000)(),
  //       income_less_50k: random_prop_gen(),
  //       tractId: i
  //   })
  // );

  // const rows = Array.from({length: 10}, (_, i) => i);
  // const cols = Array.from({length: 10}, (_, i) => i);

  const plotDots = (ctx, data, color, startCount, isLowInc) => {
    console.log("isLowInc: " + isLowInc);
    let count = 0
    let startRow = startCount/90;
    let startCol = startCount % 90;
    for(let row = startRow; row <144; row++)
    {
      for(let col = 0; col < 90; col++)
      {
        if(row == startRow && col < startCol) continue;
        if(count < data)
        {
          ctx.beginPath();
          if(isLowInc) ctx.arc(3.5*width*row/(5*144) + 20, height*col/(90)+20, 2.5, 0, 2 * Math.PI);
          else ctx.fillRect(3.5*width*row/(5*144) - 2.5 + 20, height*col/(90)+20 - 2.5, 5, 5);
          // console.log("this is: ", width*row/1067 + " " + height*col/603);
          ctx.fillStyle = color;
          ctx.fill();
          count += 1;
        }
        else break;
      }
    }
  };

  onMount(()=> {
    canvas.width = width;
    canvas.height = height;
    const ctx = canvas.getContext("2d");
  // const svgEl = d3
  //     .select(svg)
  //     .html("")
  //     .attr("width", width)
  //     .attr("height", height);

    // console.log("this is point number:" + totalPop);

    ctx.clearRect(0, 0, width, height);
    let cumCount = 0;
    // if (selectedGroups.white && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.white_low, groupColors.white, cumCount, true);
    //   cumCount += popData.white_low;
    //   plotDots(ctx, popData.white_high, groupColors.white, cumCount, false);
    //   cumCount += popData.white_low;
    //   console.log("this is cumCount after white: " + cumCount);
    // }
    if(selectedGroups.white && selectedGroups.under50k)
    {
      plotDots(ctx, popData.white_low, groupColors.white, cumCount, true);
      cumCount += popData.white_low;
      console.log("this is cumCount after white: " + cumCount);
    }
    if(selectedGroups.white && selectedGroups.over50k)
    {
      plotDots(ctx, popData.white_high, groupColors.white, cumCount, false);
      cumCount += popData.white_high;
      console.log("this is cumCount after white: " + cumCount);
    }
    // if (selectedGroups.black && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.black, groupColors.black, cumCount);
    //   cumCount += popData.black;
    //   console.log("this is cumCount after black: " + cumCount);
    // }
    if (selectedGroups.black && selectedGroups.under50k) {
      plotDots(ctx, popData.black_low, groupColors.black, cumCount, true);
      cumCount += popData.black_low;
      console.log("this is cumCount after black: " + cumCount);
    }
    if (selectedGroups.black && selectedGroups.over50k) {
      plotDots(ctx, popData.black_high, groupColors.black, cumCount, false);
      cumCount += popData.black_high;
      console.log("this is cumCount after black: " + cumCount);
    }
    // if (selectedGroups.asian && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.asian, groupColors.asian, cumCount);
    //   cumCount += popData.asian;
    //   console.log("this is cumCount after asian: " + cumCount);
    // }
    if (selectedGroups.asian && selectedGroups.under50k) {
      plotDots(ctx, popData.asian_low, groupColors.asian, cumCount, true);
      cumCount += popData.asian_low;
      console.log("this is cumCount after asian: " + cumCount);
    }
    if (selectedGroups.asian && selectedGroups.over50k) {
      plotDots(ctx, popData.asian_high, groupColors.asian, cumCount, false);
      cumCount += popData.asian_high;
      console.log("this is cumCount after asian: " + cumCount);
    }
    // if (selectedGroups.latino && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.latino, groupColors.latino, cumCount);
    //   cumCount += popData.latino;
    //   console.log("this is cumCount after hispanic: " + cumCount);
    // }
    if (selectedGroups.latino && selectedGroups.under50k) {
      plotDots(ctx, popData.latino_low, groupColors.latino, cumCount, true);
      cumCount += popData.latino_low;
      console.log("this is cumCount after hispanic: " + cumCount);
    }
    if (selectedGroups.latino && selectedGroups.over50k) {
      plotDots(ctx, popData.latino_high, groupColors.latino, cumCount, false);
      cumCount += popData.latino_high;
      console.log("this is cumCount after hispanic: " + cumCount);
    }
    // if (selectedGroups.islander && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.islander, groupColors.islander, cumCount);
    //   cumCount += popData.islander;
    //   console.log("this is cumCount after pacific islander: " + cumCount);
    // }
    if (selectedGroups.islander && selectedGroups.under50k) {
      plotDots(ctx, popData.islander_low, groupColors.islander, cumCount, true);
      cumCount += popData.islander_low;
      console.log("this is cumCount after pacific islander: " + cumCount);
    }
    if (selectedGroups.islander && selectedGroups.over50k) {
      plotDots(ctx, popData.islander_high, groupColors.islander, cumCount, false);
      cumCount += popData.islander_high;
      console.log("this is cumCount after pacific islander: " + cumCount);
    }
    // if (selectedGroups.native && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.native, groupColors.native_american, cumCount);
    //   cumCount += popData.native;
    //   console.log("this is cumCount after native american: " + cumCount);
    // }
    if (selectedGroups.native && selectedGroups.under50k) {
      plotDots(ctx, popData.native_low, groupColors.native_american, cumCount, true);
      cumCount += popData.native_low;
      console.log("this is cumCount after native american: " + cumCount);
    }
    if (selectedGroups.native && selectedGroups.over50k) {
      plotDots(ctx, popData.native_high, groupColors.native_american, cumCount, false);
      cumCount += popData.native_high;
      console.log("this is cumCount after native american: " + cumCount);
    }
    // if (selectedGroups.other && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.other, groupColors.other, cumCount);
    //   cumCount += popData.other;
    //   console.log("this is cumCount after other: " + cumCount);
    // }
    if (selectedGroups.other && selectedGroups.under50k) {
      plotDots(ctx, popData.other_low, groupColors.other, cumCount, true);
      cumCount += popData.other_low;
      console.log("this is cumCount after other: " + cumCount);
    }
    if (selectedGroups.other && selectedGroups.over50k) {
      plotDots(ctx, popData.other_high, groupColors.other, cumCount, false);
      cumCount += popData.other_high;
      console.log("this is cumCount after other: " + cumCount);
    }
    console.log("this is selectedGroups:" + selectedGroups.white + "," + selectedGroups.asian + "," + selectedGroups.black + "," +
                                          selectedGroups.islander + "," + selectedGroups.latino + "," + selectedGroups.native);

        // .attr("r", 5)
        // .attr("cx", width*row*20/totalPop + 50)
        // .attr("cy", height*col/20 + 50)
        // .attr("fill", "#f30486")
        // .style("cursor", "pointer");
      // }
    // }
  // const circle = svgEl
  //       .append("circle")
  //       .attr("r", 10)
  //       .attr("cx", width/2)
  //       .attr("cy", height/2)
  //       .attr("fill", "#f30486")
  //       .style("cursor", "pointer");
  });

  afterUpdate(()=> {
    canvas.width = width;
    canvas.height = height;
    const ctx = canvas.getContext("2d");
  // const svgEl = d3
  //     .select(svg)
  //     .html("")
  //     .attr("width", width)
  //     .attr("height", height);

    // console.log("this is point number:" + totalPop);

    ctx.clearRect(0, 0, width, height);
    let cumCount = 0;
    // if (selectedGroups.white && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.white_low, groupColors.white, cumCount, true);
    //   cumCount += popData.white_low;
    //   plotDots(ctx, popData.white_high, groupColors.white, cumCount, false);
    //   cumCount += popData.white_low;
    //   console.log("this is cumCount after white: " + cumCount);
    // }
    if(selectedGroups.white && selectedGroups.under50k)
    {
      plotDots(ctx, popData.white_low, groupColors.white, cumCount, true);
      cumCount += popData.white_low;
      console.log("this is cumCount after white: " + cumCount);
    }
    if(selectedGroups.white && selectedGroups.over50k)
    {
      plotDots(ctx, popData.white_high, groupColors.white, cumCount, false);
      cumCount += popData.white_high;
      console.log("this is cumCount after white: " + cumCount);
    }
    // if (selectedGroups.black && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.black, groupColors.black, cumCount);
    //   cumCount += popData.black;
    //   console.log("this is cumCount after black: " + cumCount);
    // }
    if (selectedGroups.black && selectedGroups.under50k) {
      plotDots(ctx, popData.black_low, groupColors.black, cumCount, true);
      cumCount += popData.black_low;
      console.log("this is cumCount after black: " + cumCount);
    }
    if (selectedGroups.black && selectedGroups.over50k) {
      plotDots(ctx, popData.black_high, groupColors.black, cumCount, false);
      cumCount += popData.black_high;
      console.log("this is cumCount after black: " + cumCount);
    }
    // if (selectedGroups.asian && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.asian, groupColors.asian, cumCount);
    //   cumCount += popData.asian;
    //   console.log("this is cumCount after asian: " + cumCount);
    // }
    if (selectedGroups.asian && selectedGroups.under50k) {
      plotDots(ctx, popData.asian_low, groupColors.asian, cumCount, true);
      cumCount += popData.asian_low;
      console.log("this is cumCount after asian: " + cumCount);
    }
    if (selectedGroups.asian && selectedGroups.over50k) {
      plotDots(ctx, popData.asian_high, groupColors.asian, cumCount, false);
      cumCount += popData.asian_high;
      console.log("this is cumCount after asian: " + cumCount);
    }
    // if (selectedGroups.latino && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.latino, groupColors.latino, cumCount);
    //   cumCount += popData.latino;
    //   console.log("this is cumCount after hispanic: " + cumCount);
    // }
    if (selectedGroups.latino && selectedGroups.under50k) {
      plotDots(ctx, popData.latino_low, groupColors.latino, cumCount, true);
      cumCount += popData.latino_low;
      console.log("this is cumCount after hispanic: " + cumCount);
    }
    if (selectedGroups.latino && selectedGroups.over50k) {
      plotDots(ctx, popData.latino_high, groupColors.latino, cumCount, false);
      cumCount += popData.latino_high;
      console.log("this is cumCount after hispanic: " + cumCount);
    }
    // if (selectedGroups.islander && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.islander, groupColors.islander, cumCount);
    //   cumCount += popData.islander;
    //   console.log("this is cumCount after pacific islander: " + cumCount);
    // }
    if (selectedGroups.islander && selectedGroups.under50k) {
      plotDots(ctx, popData.islander_low, groupColors.islander, cumCount, true);
      cumCount += popData.islander_low;
      console.log("this is cumCount after pacific islander: " + cumCount);
    }
    if (selectedGroups.islander && selectedGroups.over50k) {
      plotDots(ctx, popData.islander_high, groupColors.islander, cumCount, false);
      cumCount += popData.islander_high;
      console.log("this is cumCount after pacific islander: " + cumCount);
    }
    // if (selectedGroups.native && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.native, groupColors.native_american, cumCount);
    //   cumCount += popData.native;
    //   console.log("this is cumCount after native american: " + cumCount);
    // }
    if (selectedGroups.native && selectedGroups.under50k) {
      plotDots(ctx, popData.native_low, groupColors.native_american, cumCount, true);
      cumCount += popData.native_low;
      console.log("this is cumCount after native american: " + cumCount);
    }
    if (selectedGroups.native && selectedGroups.over50k) {
      plotDots(ctx, popData.native_high, groupColors.native_american, cumCount, false);
      cumCount += popData.native_high;
      console.log("this is cumCount after native american: " + cumCount);
    }
    // if (selectedGroups.other && selectedGroups.under50k && selectedGroups.over50k) {
    //   plotDots(ctx, popData.other, groupColors.other, cumCount);
    //   cumCount += popData.other;
    //   console.log("this is cumCount after other: " + cumCount);
    // }
    if (selectedGroups.other && selectedGroups.under50k) {
      plotDots(ctx, popData.other_low, groupColors.other, cumCount, true);
      cumCount += popData.other_low;
      console.log("this is cumCount after other: " + cumCount);
    }
    if (selectedGroups.other && selectedGroups.over50k) {
      plotDots(ctx, popData.other_high, groupColors.other, cumCount, false);
      cumCount += popData.other_high;
      console.log("this is cumCount after other: " + cumCount);
    }
    console.log("this is selectedGroups:" + selectedGroups.white + "," + selectedGroups.asian + "," + selectedGroups.black + "," +
                                          selectedGroups.islander + "," + selectedGroups.latino + "," + selectedGroups.native);

        // .attr("r", 5)
        // .attr("cx", width*row*20/totalPop + 50)
        // .attr("cy", height*col/20 + 50)
        // .attr("fill", "#f30486")
        // .style("cursor", "pointer");
      // }
    // }
  // const circle = svgEl
  //       .append("circle")
  //       .attr("r", 10)
  //       .attr("cx", width/2)
  //       .attr("cy", height/2)
  //       .attr("fill", "#f30486")
  //       .style("cursor", "pointer");
  });
</script>
<style>
  .container {
    display: flex;
    flex-direction: row;
    height: 100vh;
    width: 100vw;
  }

  .panel {
    width: 25vw;
    background: #f0f0f0;
    overflow-y: auto;
    padding: 1rem;
    box-shadow: -2px 0 5px rgba(0, 0, 0, 0.1);
  }

  canvas {
    display: block;
  }
</style>

<div class="container">
  <!-- <div class="panel">
    <Panel {selectedGroups: }/>
  </div> -->
  <canvas bind:this={canvas}></canvas>
</div>
<!-- <Panel/>
<canvas bind:this={canvas} style="display: block; width: 100vw; height: 100vh;"></canvas> -->