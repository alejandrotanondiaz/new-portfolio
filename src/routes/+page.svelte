<script>
    import projects from '$lib/projects.json';
    import Project from "$lib/Project.svelte";
    let profileData = fetch("https://api.github.com/users/alejandrotanondiaz");
</script>

<h1>Alejandro J. Tañón Díaz</h1>   
<img src="images/Stable_Diffusion_Image.png" height="200px"  alt="AI Generated Portrait of Alejandro with a blue suit and white t-shirt">
<p>Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering. Third-Year Undergraduate at MIT majoring in Computer Science and Enginering.</p>

<section>
  <h2>My Github Stats</h2>
  <div>
    {#await profileData}
      <p>Loading...</p>
    {:then response}
      {#await response.json()}
        <p>Decoding...</p>
      {:then data}
        <dl>
          <dt>
            Followers 
          </dt>
          <dd>
            {data.followers}
          </dd>

          <dt>
            Following 
          </dt>
          <dd>
            {data.following}
          </dd>

          <dt>
            Public Repos 
          </dt>
          <dd>
            {data.public_repos}
          </dd>

          <dt>
            Public Gists 
          </dt>
          <dd>
            {data.public_gists}
          </dd>   
        </dl>
        
      {:catch error}
        <p class="error">
          Something went wrong: {error.message}
        </p>
      {/await}
    {:catch error}
      <p class="error">
        Something went wrong: {error.message}
      </p>
    {/await}
  </div>
</section>

<h2>Latest Projects</h2>
<div class="projects">
    {#each projects.slice(0,3) as p}
        <Project info={p} hLevel=3 />
    {/each}
</div>

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