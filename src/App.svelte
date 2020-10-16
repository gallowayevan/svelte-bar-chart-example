<script>
  import data from "./data.js";
  import { scaleLinear, scaleBand } from "d3-scale";
  import { max, ascending } from "d3-array";

  //These won't change
  const width = 960;
  const height = 500;
  const margin = { top: 30, right: 40, bottom: 0, left: 200 };

  const counties = Array.from(data.keys());

  //This variable can be changed and things that rely on it will 'automatically' change in response
  let selectedCounty = "Orange";

  //These are the things that will change/react in response to other changes in state, like a change to selectedCounty
  $: currentData = data
    .get(selectedCounty)
    .sort((a, b) => ascending(a[1], b[1]));
  $: x = scaleLinear()
    .domain([0, max(currentData, d => d[1])])
    .range([margin.left, width - margin.right]);
  $: y = scaleBand()
    .domain(currentData.map(d => d[0]))
    .range([height - margin.bottom, margin.top])
    .paddingInner(0.1);

  //add x axis
</script>

<style>
  rect {
    fill: steelblue;
  }

  .yAxis {
    text-anchor: end;
  }

  .xAxis {
    text-anchor: middle;
  }

  text {
    font-size: 0.7em;
  }
</style>

<main>
  <label>
    Select a county
    <select bind:value={selectedCounty}>
      {#each counties as county}
        <option>{county}</option>
      {/each}
    </select>
  </label>
  <div>
    <svg viewBox="0 0 {width} {height}">
      {#each currentData as row}
        <g transform="translate(0 {y(row[0])})">
          <rect width={x(row[1]) - x(0)} x={x(0)} height={y.bandwidth()} />
          <text
            class="yAxis"
            transform="translate({margin.left})"
            dy="1.5em"
            dx="-5">
            {row[0]}
          </text>
        </g>
      {/each}
      <g transform="translate(0 {margin.top})">
        {#each x.ticks() as tick}
          <g transform="translate({x(tick)} 0)">
            <line y1="0" y2={height} stroke="#fff" />
            <text class="xAxis" dy="-5">{tick}</text>
          </g>
        {/each}
      </g>
    </svg>
  </div>

</main>
