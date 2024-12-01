<script>
    import * as d3 from 'd3';

    export let data;
    export let variable;
    export let filter;
    export let update;
    export let fullData;

    let margin = {top: 10, right: 30, bottom: 30, left: 100}; // Adjust left margin for labels
    let width = 500;
    let height = 400;
    let chartW = width - margin.left - margin.right;
    let chartH = height - margin.top - margin.bottom;

    let xAxis;
    let yAxis;

    function handleBarClick(category) {
        console.log('Bar clicked:', category);
        if (filter.includes(category)) {
            filter = []; // Clear the filter if the category is already selected
        } else {
        filter = [category]; // Set the filter to the clicked category
        }
        update(); // Trigger the update function to apply the filter
    }

    // Create bar data
    $: barData = data
        ? Array.from(
              d3.rollup(data, (v) => v.length, (d) => d[variable]),
              ([key, value]) => ({ key, value })
          )
            .sort((a, b) => d3.descending(a.value, b.value))
            .slice(0, 10)
        : [];

    $: barFullData = fullData
    ? Array.from(
            d3.rollup(fullData, (v) => v.length, (d) => d[variable]),
            ([key, value]) => ({ key, value })
        )
        .sort((a, b) => d3.descending(a.value, b.value))
        .slice(0, 10)
    : [];

    // Filter barData to only include categories in barFullData
    $: {
        const fullDataKeys = new Set(barFullData.map((d) => d.key));
        barData = barData.filter((d) => fullDataKeys.has(d.key));
    }

    // Make scales for horizontal bar chart
    $: xScale = d3.scaleLinear()
        .range([0, chartW])
        .domain([0, d3.max(barFullData, (d) => d.value)]);

    $: yScale = d3.scaleBand()
        .range([0, chartH])
        .domain(barFullData.map((d) => d.key))
        .padding(0.1);

    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }
</script>

<main>
    <h2> Data Breaches by {variable} </h2>
    <!--  Here is a bar chart showing the number of data breaches, categorized by the {variable} variable. </p>--> 
    <svg {width} {height}>
        <g transform="translate({margin.left}, {margin.top})">
            {#each barFullData as d}
                <!-- Background bar for each item (optional) -->
                <rect class="backgroundbar"
                    x={0}
                    y={yScale(d.key)}
                    width={xScale(d.value)}
                    height={yScale.bandwidth()} />
            {/each}
            
            {#each barData as d}
                <!-- Foreground bar -->
                <rect class="bar"
                    x={0}
                    y={yScale(d.key)}
                    width={xScale(d.value)}
                    height={yScale.bandwidth()}
                    on:click={() => handleBarClick(d.key)}
                    on:keydown={(e) => e.key === 'Enter' && handleBarClick(d.key)}
                    role="button"
                    tabindex=0 />
                <!-- Text label inside or next to bar -->
                <text x={xScale(d.value) + 5}
                    y={yScale(d.key) + yScale.bandwidth() / 2}
                    alignment-baseline="middle"
                    fill="white">
                    {d.value}
                </text>
            {/each}
        </g>

        <g transform="translate({margin.left}, {chartH + margin.top})" bind:this={xAxis} />
        <g transform="translate({margin.left}, {margin.top})" bind:this={yAxis} />

        <!-- Axis labels -->
        <text x={width / 2} y={height} text-anchor="middle" fill="black">Count of Data Breaches</text>
        <text x={-chartH / 2} y="14" transform="rotate(-90)" text-anchor="middle" fill="black">{variable}</text>
    </svg>
</main>

<style>
    .bar {
        fill: blue;
        stroke: white;
        stroke-width: 1px;
    }

    .backgroundbar {
        fill: grey;
    }

    text {
        fill: white;
    }
</style>
