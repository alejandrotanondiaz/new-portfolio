<script>
    import * as d3 from "d3";
    import { onMount } from "svelte";
    import Pie from "$lib/Pie.svelte";


    let data = [];
    let commits = [];
    onMount(async () => {
        // data = await d3.csv("loc.csv");
        data = await d3.csv("loc.csv", row => ({
        ...row,
        line: Number(row.line), // or just +row.line
        depth: Number(row.depth),
        length: Number(row.length),
        date: new Date(row.date + "T00:00" + row.timezone),
        datetime: new Date(row.datetime)
    }));
    // commits = d3.groups(data, d => d.commit);
    commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
        let first = lines[0];
        let {author, date, time, timezone, datetime} = first;
        let ret = {
            id: commit,
            url: "https://github.com/vis-society/lab-7/commit/" + commit,
            author, date, time, timezone, datetime,
            hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
            totalLines: lines.length
        };

        // Like ret.lines = lines
        // but non-enumerable so it doesn’t show up in JSON.stringify
        Object.defineProperty(ret, "lines", {
            value: lines,
            configurable: true,
            writable: true,
            enumerable: false,
        });

        return ret;
        });

        // console.log((commits[0]))
    });
    $: workByPeriod = d3.rollups(data, v => v.length, d => d.datetime.toLocaleString("en", {dayPeriod: "short"}))
    $: maxPeriod = d3.greatest(workByPeriod, (d) => d[1])?.[0];    
    
    let width = 1000, height = 600;
    let margin = {top: 10, right: 10, bottom: 30, left: 20};
    let usableArea = {
	top: margin.top,
	right: width - margin.right,
	bottom: height - margin.bottom,
	left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    
    let xScale;
    $: xScale = d3.scaleTime()
        .domain(d3.extent(commits.map((d) => d.datetime)))
        .range([usableArea.left, usableArea.right])
        .nice() 

    let yScale;
    $: yScale = d3.scaleLinear()
		.domain([0, 24])
		.range([usableArea.bottom, usableArea.top])
        
    let rScale;
    $: rScale = d3.scaleSqrt()
        .domain(d3.extent(commits.map((d) => d.totalLines)))
        .range([2, 30])
    
    let xAxis, yAxis;

    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
    }
    
    let yAxisGridLines;
    $: {
        d3.select(yAxisGridLines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
    }

    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? {};

    let cursor = {x: 0, y: 0};
    let svg;
    let brushSelection; 
    $: brushSelection;
    function brushed (evt) {
        console.log("brushing")
        brushSelection = evt.selection;
    }
    
    function isCommitSelected (commit) {
        console.log("In commitselect")
        console.log(brushSelection);
        
        if (!brushSelection) {
            console.log("ret false");
            return false;
        }
        let min = {x: brushSelection[0][0], y: brushSelection[0][1]};
        let max = {x: brushSelection[1][0], y: brushSelection[1][1]};
        let x = xScale(commit.date);
        let y = yScale(commit.hourFrac);
        return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
    }
    $: {
        d3.select(svg).call(d3.brush().on("start brush end", brushed));
        d3.select(svg).selectAll(".dots, .overlay ~ *").raise();
    }
    $: selectedCommits = brushSelection ? commits.filter(isCommitSelected) : [];
    $: hasSelection = brushSelection && selectedCommits.length > 0;
    $: selectedLines = (hasSelection ? selectedCommits : commits).flatMap(d => d.lines);
    $: languageBreakdown = d3.rollup(selectedLines,(d) => d.length, (d) => d.type);
    
</script>

<style>
    dl{
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: repeat(2, 1fr);
  }
  dt {
    grid-row: 1;
  }
  dd {
    grid-row: 2;
    margin-inline-start: 0px;
    font-size: 30px;
  }

  svg {
    overflow: visible;
  }

  .gridlines {
	stroke-opacity: .2;
  }
  circle {
	transition: 200ms;
    transform-origin: center;
    transform-box: fill-box;

    fill-opacity: 80%;
	&:hover {
		transform: scale(1.5);
	}
}


  dl.info {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(5, 1fr);
    margin: 0px;

    background-color: white ;
    border-radius: 3%;
    box-shadow: 5px 5px 5px;
    
    transition-duration: 500ms;
	transition-property: opacity, visibility;

	&[hidden]:not(:hover, :focus-within) {
		opacity: 0;
		visibility: hidden;
    }
  dt {
    grid-column: 1;
    grid-row: auto;
    margin: 0px;
  }
  dd {
    grid-column: 2;
    grid-row: auto;
    margin: 0px;
    font-size: 30px;
  }
    
  }
  .tooltip {
    position: fixed;
    top: 1em;
    left: 1em;
  }

  @keyframes marching-ants {
	to {
		stroke-dashoffset: -8; /* 5 + 3 */
	}
}

    svg :global(.selection) {
        fill-opacity: 10%;
        stroke: black;
        stroke-opacity: 70%;
        stroke-dasharray: 5 3;
        animation: marching-ants 2s linear infinite;
    }

    circle.selected {
        fill: coral;
    } 
</style>

<svelte:head>
	<title>Meta Data</title>
</svelte:head>

<h1>Meta</h1>

<p>Stats about this website</p>

<h2>Summary</h2>
<dl class="stats">
	<dt>Total <abbr title="Lines of code">LOC</abbr></dt>
	<dd>{data.length}</dd>

    <dt>Commits</dt>
	<dd>{commits.length}</dd>

    <dt>Busiest Time of Day</dt>
	<dd>{maxPeriod}</dd>

</dl>

<h2>Commits by time of day</h2>
<svg viewBox="0 0 {width} {height}" bind:this={svg}>
    <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridLines} />

    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />

	<g class="dots">
        
        {#each commits as commit, index }
            <circle
                class:selected={isCommitSelected(commit)}
                cx={ xScale(commit.datetime) }
                cy={ yScale(commit.hourFrac) }
                r= {rScale(commit.totalLines)}
                fill="steelblue"
                on:mouseenter={evt => {
                    hoveredIndex = index;
                    cursor = {x: evt.x, y: evt.y};
                }}
	            on:mouseleave={evt => hoveredIndex = -1}
                
            />
        {/each}
        </g>
        
</svg>
<p>{hasSelection ? selectedCommits.length : "No"} commits selected</p>
<Pie data={Array.from(languageBreakdown).map(([language, lines]) => ({label: language, value: lines}))} />
<dl id="commit-tooltip" class="info tooltip" hidden={hoveredIndex === -1} style="top: {cursor.y}px; left: {cursor.x}px">
    <dt>Commit</dt>
    <dd><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>

    <dt>Date</dt>
    <dd>{ hoveredCommit.datetime?.toLocaleString("en", {date: "full"}) }</dd>

    <dt>Time</dt>
    <dd>{ hoveredCommit.time }</dd>

    <dt>Author</dt>
    <dd>{ hoveredCommit.author}</dd>

    <dt>Lines Edited</dt>
    <dd>{ hoveredCommit.totalLines}</dd>

</dl>