<script>
  export let info = {};
  // let p = info;
  export let hLevel = 2;
</script>

<style>
  

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
    <svelte:element this={"h" + hLevel}>{ info.title }</svelte:element>
    <img src="{info.image}" alt="">
    <p>{info.description}</p>
    <p>C. {info.year}</p>
</article>