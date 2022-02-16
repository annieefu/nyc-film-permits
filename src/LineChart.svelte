<script>
  // import event flag component
  import EventFlag from "./EventFlag.svelte";

  // import Tooltip
  import Tooltip from "./Tooltip.svelte";

  export let dataset;
  export let width;

  // D3 chart imports
  import { line, scaleLinear, timeParse, scaleTime, bisector } from "d3";

  const margin = { top: 30, bottom: 90, left: 30, right: 30 };

  const monthList = [
    "Jan",
    "Feb",
    "Mar",
    "Apr",
    "May",
    "Jun",
    "Jul",
    "Aug",
    "Sep",
    "Oct",
    "Nov",
    "Dec",
  ];

  // SVG element variable
  let el;

  let height = 440;

  let date_parser = timeParse("%Y-%m-%d");

  // NEW SOLUTION: Map creates an array and returns into new variable, keeping the original dataset intact.
  const formatted = dataset.map((d) => {
    const b = {};
    b.Count = Number(d.Count);
    b.date = date_parser(d.Date);
    b.month = b.date.getMonth() + 1;
    b.year = b["date"].getFullYear();
    return b;
  });

  console.log(formatted)

  // OLD SOLUTION
  //     $: dataset.forEach(d => {
  //     d["date"] = date_parser(d.Date)
  //     d["year"] = d["date"].getFullYear();
  //     d["month"] = Number(d["date"].getMonth()) + Number(1);
  //     d.Count = Number(d.Count)
  //   });

    // Getting mouse position
    let mouse = { x: 0, y: 0, input: 0, data: 0 };
    let xmonth;

    let find_closest_x = bisector(d => d.date).right;

	function handleMousemove(event) {
		mouse.x = event.layerX;
		mouse.y = event.layerY;
        xmonth = xScale.invert(mouse.x)
        if (xmonth < date_parser("2021-11-01")){
        mouse.input = find_closest_x(formatted, xmonth);
        mouse.data = formatted[mouse.input];
        }
        else {
            mouse.input = 0;
            mouse.data = 0;
        }
	}

    $: console.log(xmonth)

    function handleMouseout(event) {
		mouse = { x: 0, y: 0, input: 0, data: 0 };
	}






  let events = [
    ["Trump declares COVID", "a national emergency."],
    ["The FDA authorizes COVID", " vaccines for use."],
  ];

  // Scales
  let xScale;
  let yScale;
  $: xScale = scaleTime()
    .domain([new Date("2020-01-01"), new Date("2021-11-01")])
    .range([margin.left, width - margin.right]);

  $: yScale = scaleLinear()
    .domain([0, 700])
    .range([height - margin.bottom, margin.top]);

  let path;
  $: path = line()
    .x((d) => xScale(d.date))
    .y((d) => yScale(d.Count));
  // Axes

  // // Ticks
  let xTicks = [];
  let count = 0;
  formatted.forEach((d) => {
    count = count + 1;
    if (count % 3 == 2) {
      xTicks.push(d.date);
    }
  });

  // X LABELS FOR MONTH AND YEAR
  let xLabel = (x) =>
    monthList[x.getMonth()] +
    " 20" +
    x.getYear().toString().substring(x.getYear(), 1);

  let xLabelMobile = (x) =>
    monthList[x.getMonth()] +
    " " +
    x.getYear().toString().substring(x.getYear(), 1);

  // Y TICKS BY STEPS OF 100
  let yTicks = [];
  for (let i = 0; i < Math.round(701); i = i + 100) {
    yTicks.push(Math.floor(i / 100) * 100);
  }

  // PLOT OUT AXIS PATHS
  let xPath;
  $: xPath = `M${margin.left + 0.5},6V0H${width - margin.right + 1}`;
  let yPath = `M-6,${height - margin.bottom}H0.5V0.5H-6`;
</script>

<svg bind:this={el} style="width: {width}" on:mousemove={handleMousemove} on:mouseleave={handleMouseout} >
  <!-- The data line -->
  <g>
    <path d={path(formatted)} fill="none" stroke="blue" stroke-width="3" />
  </g>

  <!-- y axis -->
  <g transform="translate({margin.left}, 0)">
    <path stroke="currentColor" d={yPath} fill="none" />

    {#each yTicks as y}
      <g class="tick" opacity="1" transform="translate(0,{yScale(y)})">
        <line stroke="currentColor" x2="-5" />
        <text dy="0.32em" fill="currentColor" x="-{margin.left}">
          {y}
        </text>
      </g>
    {/each}
  </g>

  <!-- Y ticks -->
  <g transform="translate(0, {height - margin.bottom})">
    <path stroke="currentColor" d={xPath} fill="none" />

    {#each xTicks as x}
      <g class="tick" opacity="1" transform="translate({xScale(x)},0)">
        <line stroke="currentColor" y2="6" />
        <text fill="currentColor" y="9" dy="0.71em" x="-{margin.left}">
          {#if width > 500}
            {xLabel(x)}
          {:else}
            {xLabelMobile(x)}
          {/if}
        </text>
      </g>
    {/each}
  </g>


  <g style="z-index:10">
  <!-- Event Flags -->
    <EventFlag
      text={events[0]}
      date={date_parser("2020-03-13")}
      {xScale}
      {path}
      {width}
      {height}
      {margin}
    />
    <EventFlag
      text={events[1]}
      date={date_parser("2020-12-11")}
      {xScale}
      {path}
      {width}
      {height}
      {margin}
    />

    <Tooltip {xScale} {yScale} {width} {height} {margin} {mouse} {monthList} {date_parser} />

  </g>
  <!-- Tooltip -->
</svg>

<style>
  svg {
    height: 400px;
    margin-left: auto;
    margin-right: auto;
  }
  .tick {
    font-size: 11px;
  }
</style>
