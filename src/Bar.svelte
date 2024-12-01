<script>
    import * as d3 from 'd3';

    export let data;
    export let fullData;
    export let variable;

    let margin = {top: 10, right: 30, bottom: 30, left: 40};
    let width = 500;
    let height = 400;
    let chartW = width - margin.left - margin.right;
    let chartH = height - margin.top - margin.bottom;

    let brushLayer;
    let xAxis;
    let yAxis;   

    // create bar data
    // create bar data
    $: barFullData = fullData
        ? Array.from(
              d3.rollup(fullData, (v) => v.length, (d) => d[variable]),
              ([key, value]) => ({ key, value })
          )
          .sort((a, b) => d3.ascending(a.key, b.key))
        : [];
    $: barData = data
        ? Array.from(
              d3.rollup(data, (v) => v.length, (d) => d[variable]),
              ([key, value]) => ({ key, value })
          )
          .sort((a, b) => d3.ascending(a.key, b.key))
        : [];

    // make y scale for the bar chart (categorical variable)
    // make x and y scales for the bar chart
    $: xScale = d3.scaleBand()
        .range([0, chartW])
        .domain(barFullData.map((d) => d.key))
        .padding(0.1);

    $: yScale = d3.scaleLinear()
        .range([chartH, 0])
        .domain([0, d3.max(barFullData, (d) => d.value)]);
    $: {
            d3.select(xAxis)
                .call(d3.axisBottom(xScale))
                .selectAll(".tick text") // Select tick labels
                .attr("transform", "rotate(-45)")
                .style("text-anchor", "end");
            d3.select(yAxis)
                .call(d3.axisLeft(yScale));
        }
</script>

<main>
    <h2> Number of Data Breaches by {variable} </h2>
    <p> Here is a chart showing the number of data breaches by {variable} </p>
    <svg {width} {height}>
        <g transform="translate({margin.left}, {margin.top})">
            {#each barFullData as d}
                <rect class="backgroundbar"
                    x={xScale(d.key)}
                    y={yScale(d.value)}
                    width={xScale.bandwidth()}
                    height={chartH - yScale(d.value)} />
            {/each}
            {#each barData as d}
                <rect class="bar"
                    x={xScale(d.key)}
                    y={yScale(d.value)}
                    width={xScale.bandwidth() * 0.9}
                    height={chartH - yScale(d.value)} />
            {/each}
        </g>

        <g transform="translate({margin.left}, {margin.top})"
            bind:this={brushLayer} />
       
        <g transform="translate({margin.left}, {chartH + margin.top})" 
            bind:this={xAxis} />

        <g transform="translate({margin.left}, {margin.top})"
            bind:this={yAxis} />

        <!-- Axis labels -->
        <text x={width / 2} y={height} text-anchor="middle" fill="white">{variable}</text>
        <text x={-chartH / 2} y="15" transform="rotate(-90)" text-anchor="middle" fill="white">Count</text>
      </svg>
</main>

<style>
    .bar {
        fill: orangered;
        stroke: white;
        stroke-width: 1px;
    }

    .backgroundbar {
        fill: grey;
    }
 </style>