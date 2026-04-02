<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import BarHorizontal from '../../lib/BarHorizontal.svelte';
    import * as d3 from 'd3';
    import { computePosition, autoPlacement, offset } from '@floating-ui/dom';

    let locData = [];
    let commits = [];

    onMount(async () => {
        locData = await d3.csv(`${base}/loc.csv`, row => ({
            ...row,
            line: Number(row.line),
            depth: Number(row.depth),
            length: Number(row.length),
            date: new Date(row.date + "T00:00" + row.timezone),
            datetime: new Date(row.datetime)
        }));
        commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
            let first = lines[0];
            let {author, date, time, timezone, datetime} = first;
            let ret = {
                id: commit,
                url: "https://github.com/vis-society/lab-7/commit/" + commit,
                author, date, time, timezone, datetime,
                hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
                totalLines: lines.length,
                lines: lines
            };

            return ret;
        });

        commits = d3.sort(commits, d => -d.totalLines);

    });

    
    $: data = d3.rollups(
        locData,
        v => d3.sum(v, d => d.length),
        d => d.type
    ).map(([type, count]) => ({ label: type, value: count }));

    let width = 1000, height = 600;

    $: [minDate, maxDate] = d3.extent(commits.map(d => d.date));
    $: maxDatePlusOne = new Date(maxDate);
    $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

    let margin = {top: 10, right: 10, bottom: 30, left:20}


    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;

    $: xScale = d3.scaleTime()
        .domain([minDate, maxDatePlusOne])
        .range([usableArea.left, usableArea.right])
        .nice();

    $: yScale = d3.scaleLinear()
                .domain([24, 0])
                .range([usableArea.bottom, usableArea.top]);

    let xAxis, yAxis;
    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
    }

    let yAxisGridlines;
    $: {
        d3.select(yAxisGridlines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
    }

    $: lineRange = d3.extent(commits.map(d => d.totalLines));
    $: rScale = d3.scaleSqrt()
        .domain(lineRange)
        .range([5, 30]);

    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

    let cursor = {x: 0, y: 0};

    let commitTooltip;
    let tooltipPosition = {x: 0, y: 0};

    async function dotInteraction (index, evt) {
	let hoveredDot = evt.target;
        if (evt.type === "mouseenter") {
            hoveredIndex = index;
            cursor = {x: evt.x, y: evt.y};
            tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
                strategy: "fixed", // because we use position: fixed
                middleware: [
                    offset(5), // spacing from tooltip to dot
                    autoPlacement() // see https://floating-ui.com/docs/autoplacement
                ],
            });        }
        else if (evt.type === "mouseleave") {
            hoveredIndex = -1
        }
        else if (evt.type === "click") {
            let commit = commits[index]
            if (!clickedCommits.includes(commit)) {
                // Add the commit to the clickedCommits array
                clickedCommits = [...clickedCommits, commit];
            }
            else {
                    // Remove the commit from the array
                    clickedCommits = clickedCommits.filter(c => c !== commit);
            }
        }

    }

    let clickedCommits = [];


    $: selectedLines = (clickedCommits.length > 0 ? clickedCommits.flatMap(d => d.lines) : locData);

    $: selectedCounts = d3.rollup(
        selectedLines,
        v => v.length,
        d => d.type
    );

    $: allTypes = Array.from(new Set(locData.map(d => d.type)));

    $: barData = allTypes.map(type =>  ({ label: String(type), value: selectedCounts.get(type) || 0 }));


</script>

<section> 
    <h1>Meta Analysis</h1>

    <h3>Commits by time of day</h3>

    <dl class="info tooltip"  bind:this={commitTooltip} hidden={hoveredIndex === -1} style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px">
        <dt>Commit</dt>
        <dd><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>

        <dt>Date</dt>
        <dd>{ hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"}) }</dd>

        <!-- Add: Time, author, lines edited -->
        <dt>Time</dt>
        <dd>{ hoveredCommit.time}</dd>

        <dt>Author</dt>
        <dd>{ hoveredCommit.author}</dd>

        <dt>Lines Edited</dt>
        <dd>{ hoveredCommit.totalLines}</dd>

    </dl>


    <svg viewBox="0 0 {width} {height}">
        <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
        <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
        <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />


        <g class="dots">
            {#each commits as commit, index }
                <circle
                	on:mouseenter={evt => dotInteraction(index, evt)}
	                on:mouseleave={evt => dotInteraction(index, evt)}
                    on:click={ evt => dotInteraction(index, evt) }
                    class:selected={ clickedCommits.includes(commit) }
                    cx={ xScale(commit.datetime) }
                    cy={ yScale(commit.hourFrac) }
                    r={ rScale(commit.totalLines) }
                    fill="steelblue"
                />
            {/each}
        </g>
    </svg>

    <BarHorizontal data={barData} />


</section>

<style>
	svg {
		overflow: visible;
	}
    .gridlines {
        stroke-opacity: .2;
    }
    circle {
        fill-opacity: 0.5;
        transition: 200ms;
        &:hover {
            fill:darkgreen;
        }
    }

    dl.info {
        display: grid;
        grid-template-columns: auto 1fr;
        margin: 0;
        transition-duration: 500ms;
        transition-property: opacity, visibility;

        &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
        }
    }

    dl.info dt {
        color: gray;
        font-size: 0.9em;
    }

    .tooltip {
        position: fixed;
        top: 1em;
        left: 1em;
        padding: 1em;
        background-color: oklch(100% 0% 0 / 80%);
        box-shadow: 0 0 8px rgb(130, 130, 130);

    }

    .selected {
        fill: var(--color-accent);
    }
</style>