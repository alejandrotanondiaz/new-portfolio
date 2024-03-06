<script>
    import projects from '$lib/projects.json';
    import Project from "$lib/Project.svelte";
</script>
<h1>Alejandro J. Tañón Díaz</h1>   
<img src="images/Stable_Diffusion_Image.png" height="200px"  alt="AI Generated Portrait of Alejandro with a blue suit and white t-shirt">
<p>Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering.</p>

<h2>Latest Projects</h2>
<div class="projects">
    {#each projects.slice(0,3) as p}
        <Project info={p} hLevel=3 />
    {/each}
</div>
