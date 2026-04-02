<script>
    import * as d3 from 'd3';

    let width = 600;
    let height = 300;

    export let data = [];

    let margin = { top: 30, right: 150, bottom: 40, left: 100 };
    let innerWidth  = width  - margin.left - margin.right;
    let innerHeight = height - margin.top  - margin.bottom;

    $: yScale = d3.scaleBand()
        .domain(data.map(d => d.label))
        .range([0, innerHeight])
        .padding(0.2);

    $: xScale = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.value) || 1])
        .range([0, innerWidth]);

    $: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
        .domain(data.map(d => d.label));
    
    let xAxis, yAxis;

    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(d3.axisBottom(xScale)
            .tickFormat(d => Number.isInteger(d) ? d : "")
        );
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }

    $: maxBar = d3.greatest(data, d => d.value);

    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(
            d3.axisBottom(xScale)
            .ticks(Math.min(d3.max(data, d => d.value), 10))
        );
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }


</script>

<div class="container">
    <svg viewBox="0 0 {width} {height}">
        <text
            x={margin.left + innerWidth / 2}
            y={margin.top / 2}
            text-anchor="middle"
            class="chart-title">
            Lines of Code by Language
        </text>
       

        <g transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis} />
        <g transform="translate({margin.left}, {margin.top})"
        bind:this={yAxis} />
        
        <g transform="translate({margin.left}, {margin.top})">
            <!-- x-axis label -->
            <text
                x={innerWidth / 2}
                y={innerHeight + margin.bottom + 5}
                text-anchor="middle"
                class="axis-label">
                Lines of Code
            </text>

            <!-- y-axis label -->
            <text
                x={-(innerHeight / 2)}
                y={-margin.left + 30}
                text-anchor="middle"
                transform="rotate(-90)"
                class="axis-label">
                Programming Language
            </text>
            {#each data as d}
                <rect
                    x={0}
                    y={yScale(d.label)}
                    width={xScale(d.value)}
                    height={yScale.bandwidth()}
                    fill={colorScale(d.label)}
                />
            {/each}

            {#if maxBar}
                <!-- highlight outline around the tallest bar -->
                <rect
                    x={0}
                    y={yScale(maxBar.label)}
                    width={xScale(maxBar.value)}
                    height={yScale.bandwidth()}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- annotation text at end of leader line -->
                <text
                    x={xScale(maxBar.value) + 10}
                    text-anchor="start"
                    y={yScale(maxBar.label) + yScale.bandwidth() / 2}
                    dominant-baseline="middle"
                    class="annotation">
                    Language with most lines
                </text>
            {/if}
        </g>

    </svg>
    <ul class="legend">
        {#each data as d}
            <li style="--color: {colorScale(d.label)}">
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>



<style> 
    svg {
        max-width: 100%;
        height: auto;
        overflow: visible;
    }

    .container {
        display: flex;
        align-items: center;
    }

    .legend {
        flex: 1;
    }

    .swatch {
        background-color: var(--color);
        width: 1em;
        height: 1em;
        margin-right: 0.2em;
    }

    li {
        display: flex;
        align-items: center;
        gap: 0.5em;
    }

    .chart-title {
        font-size: 1em;
        font-weight: bold;
        fill: currentColor;
    }

    .axis-label {
        font-size: 0.8em;
        fill: currentColor;
    }

    .annotation {
        font-size: 0.7em;
        fill: black;
        font-style: italic;
    }
</style>