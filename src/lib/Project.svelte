<script>
export let info = {};
let p = info;
export let hLevel = 2;
</script>

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

section {
  display: flex;
  flex-direction: column;
  header{
    font-weight: bold;
  }
  
  article {
    display: flex;
    flex-direction: column;
    margin-bottom: 1em;
    hgroup {
      display: flex;
      /* flex-direction: column; */
      justify-content: space-between;
      margin-top: 1px;
      margin-bottom: 1px;
    }
  }
}

</style>

<article>
    <svelte:element this={"h" + hLevel}>{ p.title }</svelte:element>
    <img src="{p.image}" alt="">
    <p>{p.description}</p>
</article>