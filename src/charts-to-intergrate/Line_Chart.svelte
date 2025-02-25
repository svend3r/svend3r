<script>
  import { curveLinear, scaleUtc, scaleLinear, line, range } from 'd3';

  export let lineData;

  const data = lineData,
    r = 3, // (fixed) radius of dots, in pixels
    curve = curveLinear, // method of interpolation between points
    marginTop = 20, // the top margin, in pixels
    marginRight = 0, // the right margin, in pixels
    marginBottom = 30, // the bottom margin, in pixels
    marginLeft = 40, // the left margin, in pixels
    inset = r * 2, // inset the default range, in pixels
    insetTop = inset, // inset the default y-range
    insetRight = inset, // inset the default x-range
    insetBottom = inset, // inset the default y-range
    insetLeft = inset, // inset the default x-range
    width = 900, // the outer width of the chart, in pixels
    height = 600, // the outer height of the chart, in pixels
    xType = scaleUtc, // type of x-scale
    xRange = [marginLeft + insetLeft, width - marginRight - insetRight], // [left, right]
    yType = scaleLinear, // type of y-scale
    yRange = [height - marginBottom - insetBottom, marginTop + insetTop], // [bottom, top]
    xLabel = '', // a label for the y-axis
    yLabel = '↑ Daily Close', // a label for the y-axis
    xFormat = '', // a format specifier string for the y-axis
    yFormat = '', // a format specifier string for the y-axis
    horizontalGrid = true, // show horizontal grid lines
    verticalGrid = false, // show vertical grid lines
    xScalefactor = width / 80, //y-axis number of values
    yScalefactor = height / 40, //y-axis number of values
    // number of colors in fill array MUST match number of subsets in data
    colors = ['blue', 'red', 'green'], // fill color for dots
    showDots = false, // whether dots should be displayed
    dotsFilled = false, // whether dots should be filled or outlined
    strokeLinecap = 'round', // stroke line cap of the line
    strokeLinejoin = 'round', // stroke line join of the line
    strokeWidth = 1, // stroke width of line, in pixels
    strokeOpacity = 0.8, // stroke opacity of line
    tooltipBackground = 'black', // background color of tooltip
    tooltipTextColor = 'white'; // text color of tooltip

  let x, y, xVals = [], yVals = [], points = [], dotInfo;
  const subsets = [], colorVals = [];

  // For a single set of data
  if (colors.length === 1) {
    x = Object.keys(data[0])[0];
    y = Object.keys(data[0])[1];
    xVals = data.map((el) => el[x]);
    yVals = data.map((el) => el[y]);
    points = data.map((el) => [el[x], el[y], 0]);
  }
  // For data with subsets (NOTE: expects 'id' and 'data' keys)
  else {
    x = Object.keys(data[0]?.data[0])[0];
    y = Object.keys(data[0]?.data[0])[1];
    data.forEach((subset, i) => {
      subset.data.forEach((coordinate) => {
        xVals.push(coordinate[x]);
        yVals.push(coordinate[y]);
        colorVals.push(i);
        points.push(
          { 
            x: coordinate[x],
            y: coordinate[y],
            color: i
          });
      });
      subsets.push(subset.id);
    });
  }

  const I = range(xVals.length);
  const gaps = (d, i) => !isNaN(xVals[i]) && !isNaN(yVals[i]);
  const cleanData = points.map(gaps);

  const xDomain = [xVals[0], xVals[xVals.length - 1]];
  const yDomain = [0, Math.max(...yVals)];
  const xScale = xType(xDomain, xRange);
  const yScale = yType(yDomain, yRange);
  const niceY = scaleLinear().domain([0, Math.max(...yVals)]).nice();

  const chartLine = line()
    .defined(i => cleanData[i])
    .curve(curve)
    .x(i => xScale(xVals[i]))
    .y(i => yScale(yVals[i])); // TODO: should this be niceY?

  const lines = [];

  colors.forEach((color, j) => {
    const filteredI = I.filter((el, i) => colorVals[i] === j);
    lines.push(chartLine(filteredI));
  });

  const xTicks = xScale.ticks(xScalefactor);
  const xTicksFormatted = xTicks.map((el, i, t) => {
    if (i === 0 || el.getFullYear() === t[i - 1].getFullYear())
      return el.toLocaleString('en-US', { month: 'long' });
    else return el.getFullYear();
  });

  const yTicks = niceY.ticks(yScalefactor);

  const hyp = (index, mouseX, mouseY) => Math.hypot(xScale(xVals[index]) - mouseX + 17, yScale(yVals[index]) - mouseY + 17);
  function mousemoved(e) {
    const { clientX, clientY } = e;
    const closest = I.sort((a, b) => hyp(a, clientX, clientY) - hyp(b, clientX, clientY))[0];
    dotInfo = 
      { 
        x: xVals[closest], 
        y: yVals[closest],
        index: colorVals[closest]
      };
  }
</script>

<svg {width} {height} viewBox="0 0 {width} {height}"
  cursor='crosshair'
  on:mousemove="{(e) => mousemoved(e)}"  
  on:mouseout="{() => dotInfo = null}"
  on:blur="{() => dotInfo = null}"
>

  <!-- Dots (if enabled) -->
  {#if showDots && !dotInfo}
    {#each I as i}
      <g class='dot' pointer-events='none'>
        <circle
          cx={xScale(xVals[i])}
          cy={yScale(yVals[i])}
          r={r}
          stroke={colors[colorVals[i]]}
          filled={dotsFilled ? colors[colorVals[i]] : 'none'}
        />
      </g>
    {/each}
  {/if}

  <!-- Chart Lines -->
  {#each lines as subsetLine, i}
    <g class='chartlines' pointer-events='none'>
      {#if dotInfo}
        <path class="line" fill='none' stroke-opacity={dotInfo.index === i ? '1' : '0.4'} stroke={colors[i]} d={subsetLine} />
        <circle cx={xScale(dotInfo.x)} cy={yScale(dotInfo.y)} r=3 stroke={colors[dotInfo.index]} fill='none' />
      {:else}
        <path class="line" fill='none' stroke={colors[i]} d={subsetLine}
          stroke-opacity={strokeOpacity} stroke-width={strokeWidth} stroke-linecap={strokeLinecap} stroke-linejoin={strokeLinejoin} />
      {/if}
    </g>
  {/each}
  
  <!-- Y-axis and horizontal grid lines -->
  <g class="y-axis" transform="translate({marginLeft}, 0)" pointer-events='none'>
    <path class="domain" stroke="black" d="M{insetLeft}, 0.5 V{height}"/>
    {#each yTicks as tick, i}
      <g class="tick" transform="translate(0, {yScale(tick)})">
        <line class="tick-start" x1={insetLeft - 6} x2={insetLeft}/>
        {#if horizontalGrid}
          <line class="tick-grid" x1={insetLeft} x2={width - marginLeft - marginRight}/>
        {/if}
        <text x={-marginLeft} y="10">{tick + yFormat}</text>
      </g>
    {/each}
    <text x="-{marginLeft}" y={marginTop}>{yLabel}</text>
  </g>

  <!-- X-axis and vertical grid lines -->
  <g class="x-axis" transform="translate(0,{height - marginBottom - insetBottom})" pointer-events='none'>
    <path class="domain" stroke="black" d="M{marginLeft},0.5 H{width}"/>
    {#each xTicks as tick, i}
      <g class="tick" transform="translate({xScale(tick)}, 0)">
        <line class="tick-start" stroke='black' y2='6' />
        {#if verticalGrid}
          <line class="tick-grid" y2={-height} />
        {/if}
        <text font-size='8px' x={-marginLeft} y="20">{xTicksFormatted[i] + xFormat}</text>
      </g>
    {/each}
    <text x={width - marginLeft - marginRight - 40} y={marginBottom}>{xLabel}</text>
  </g>
</svg>

<!-- Tooltip -->
{#if dotInfo}
  <div style='position:absolute; left:{xScale(dotInfo.x) + 12}px; top:{yScale(dotInfo.y) + 12}px; pointer-events:none; background-color:{tooltipBackground}; color:{tooltipTextColor}'>
    {subsets[dotInfo.index]} {dotInfo.x.toLocaleDateString('en-US')} ${dotInfo.y.toFixed(2)}
  </div>
{/if}

<style>
  svg {
    max-width: 100%;
    height: auto;
    height: "intrinsic";
  }


  path {
    fill: "green"
  }

  .y-axis {
    font-size: "10px";
    font-family: sans-serif;
    text-anchor: "end";
  }

  .x-axis {
    font-size: "10px";
    font-family: sans-serif;
    text-anchor: "end";
  }

  .tick {
    opacity: 1;
  }

  .tick-start {
    stroke: black;
    stroke-opacity: 1;
  }

  .tick-grid {
    stroke: black;
    stroke-opacity: 0.2;
    font-size: "11px";
    color: black;
  }

  .tick text {
    fill: black;
    text-anchor: start;
  }
</style>