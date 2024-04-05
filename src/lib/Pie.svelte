<script>
    import * as d3 from 'd3';
    export let selectedIndex = -1;
    let arcGenerator = d3.arc().innerRadius(0).outerRadius(50);
    let arc = arcGenerator({
        startAngle: 0,
        endAngle: 2 * Math.PI
    });
    export let data = [];
    
    // let data = [
    //     { value: 1, label: "apples" },
    //     { value: 2, label: "oranges" },
    //     { value: 3, label: "mangos" },
    //     { value: 4, label: "pears" },
    //     { value: 5, label: "limes" },
    //     { value: 5, label: "cherries" }
    // ];

    let sliceGenerator = d3.pie().value(d => d.value);
    let arcData;
    $: arcData = sliceGenerator(data);
    let arcs; 
    $: arcs = arcData.map(d => arcGenerator(d));
    let colors = d3.scaleOrdinal(d3.schemeTableau10);

</script>
    <div class="container"> 
        <svg viewBox="-50 -50 100 100">

            {#each arcs as arc, i}
                <path d={arc} fill={colors(i)}
                tabindex="0"
                role="button"
                aria-label="section"
                class:selected={selectedIndex === i}
                on:click={e => selectedIndex = selectedIndex === i ? -1 : i} />
            {/each}
        </svg>
        <ul class="legend">
            {#each data as d, index}
                <li style="--color: { colors(index) }"
                class:selected={selectedIndex === index}>
                    <span class="swatch"></span>
                    {d.label} <em>({d.value})</em>
                </li>
            {/each}
        </ul>
    </div>
<style>
    svg {
        max-width: 20em;
        margin-block: 2em;

        /* Do not clip shapes outside the viewBox */
        overflow: visible;
    }
    svg:has(path:hover) {
        path:not(:hover) {
            opacity: 50%;
        }
    }
    path {
        transition: 300ms;
    }
    .legend {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(9em, 1fr));
        gap: 1em;
        padding: 1em;
        border: solid;
        flex: 1;

        
        /* border-color: black; */
    }
    li {
        display: flex;
        align-items: center;

    }
    span{
        width: auto;
        aspect-ratio: 1 / 1;
        background-color: var(--color);
        border-radius: 50%;

    }
    .container {
        display: flex;
        align-items: center;
        gap: 2em;
        flex: 1;
    }
    .selected {
        --color: oklch(60% 45% 0) !important;

        &:is(path) {
            fill: var(--color);
        }
    }
</style>