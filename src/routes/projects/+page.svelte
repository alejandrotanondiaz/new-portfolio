<script>
import * as d3 from "d3";
import Pie from '$lib/Pie.svelte';
import projects from '$lib/projects.json';
import Project from "$lib/Project.svelte";

let rolledData; 
$: rolledData = d3.rollups(filteredProjects, v => v.length, d => d.year);
let pieData; 
$: pieData = rolledData.map(([year, count]) => {
	return { value: count, label: year };
});

let query = "";
let filteredProjects;
$: filteredProjects = projects.filter(project => {
  let values = Object.values(project).join("\n").toLowerCase();
  return values.includes(query.toLowerCase());
});

let selectedYearIndex = -1;
let selectedYear;
$: selectedYear = selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;

let filteredByYear;
$: filteredByYear = filteredProjects.filter(project => {
  if (selectedYear) {
		return project.year === selectedYear;
	}
  return true;
});

</script>

<svelte:head>
	<title>My Projects</title>
</svelte:head>
<input type="search" bind:value={query}
       aria-label="Search projects" placeholder="🔍 Search projects…" />

<h1>Projects: {filteredProjects.length}</h1>
<Pie data={pieData} bind:selectedIndex={selectedYearIndex} />
<div class="projects">
    {#each filteredByYear as p}
    <Project info={p} />
    
    {/each}

</div>

<style>
    .projects {
  display: grid;
  gap: 1em;
  grid-template-columns: repeat(auto-fill, minmax(18em, 1fr));
  grid-template-rows: subgrid;
  article {
    display: grid;
    grid-row: span 3;
    grid-template-rows: subgrid;
    h2 {
      min-height: 3em;
      margin: 0;
    }
    
  }
}
</style>