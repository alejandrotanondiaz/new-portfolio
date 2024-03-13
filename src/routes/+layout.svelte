
<script>
	import { page } from '$app/stores';
    let pages = [
	{url: "./", title: "Home"},
	{url: "projects", title: "Projects"},
	{url: "resume", title: "Resume"},
	{url: "contact", title: "Contact"},
	{url: "https://github.com/alejandrotanondiaz", title: "Github"},
    ];

	let localStorage = globalThis.localStorage ?? {};
	let colorScheme = "light dark";
	if (localStorage.colorScheme) {
		colorScheme = localStorage.colorScheme;
	}
	$: localStorage.colorScheme = colorScheme;

	let root = globalThis?.document?.documentElement;
	$: root?.style.setProperty("color-scheme", colorScheme);


</script>

<style>
nav {
  display: flex;
  border-bottom-width: 1px;
  border-bottom-style: solid;
  border-bottom-color: oklch(80% 3% 200);
  margin-bottom: 1em;
  a {
    flex: 1;
    text-decoration: none;
    color: inherit;
    text-align: center;
    padding: 0.5em;
  }
  a:hover {
    background-color: oklch(from var(--color-accent) 95% 5% h);
    /* background-color: color-mix(in oklch, var(--color-accent) 100%, canvas 80%); */
    border-bottom-width: 0.4em;
    border-bottom-style: solid;
    border-bottom-color: var(--color-accent);
    padding-bottom: 0.5em;
  }
  
  .current {
    border-bottom-width: 0.4em;
    border-bottom-style: solid;
    border-bottom-color: var(--color-accent);
    padding-bottom: 0.5em;
  }
  
}

label {
	position: absolute;
	top: 1em;
	right: 1em;
}
label, textarea, select{
  display: grid;
  grid-template-columns: subgrid
} 
label, button, select {
  grid-column: 1 / -1
}

</style>

<label class="color-scheme">
	Theme:
	<select bind:value={ colorScheme }>
		<option value="light dark">Defaut</option>
		<option value="light">Light</option>
		<option value="dark">Dark</option>
	</select>
</label>

<nav>
	{#each pages as p }	
		<a href={ p.url } class:current={ "." + $page.route.id === p.url } target={ p.url.startsWith("http") ? "_blank" : null } >{ p.title }</a>
	{/each}
</nav>
<slot />

