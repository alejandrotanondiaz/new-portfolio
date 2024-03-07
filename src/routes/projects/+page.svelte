<script>
import projects from '$lib/projects.json';
import Project from "$lib/Project.svelte";
</script>
<svelte:head>
	<title>My Projects</title>
</svelte:head>

<h1>Projects: {projects.length}</h1>
<div class="projects">
    {#each projects as p}
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