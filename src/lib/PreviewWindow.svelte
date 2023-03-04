<svelte:head>
<link rel="stylesheet" href="https://frontsome-sveltekit.vercel.app/global.css">
</svelte:head>

<script>
import { onMount } from 'svelte';

export let data;
export let postId;

let post;
let preview = false;
	
	
onMount(async () => {
	
		window.addEventListener(
			"message",
			function (e) {
			  // var origin = e.originalEvent.origin || e.origin;
			  // if (origin !== "https://dashpilot.cyclic.app") return;
			  
			  console.log("received message");
			
			  post = JSON.parse(e.data)
			  
			  var template = Handlebars.compile(`
				  <h2>{{title}}</h2>
				  
				  {{{body}}}
				  
			  `);
			  // execute the compiled template and print the output to the console
			  preview = template(post);
			 
			},
			false
		  );

});	
</script>
<div class="preview">
{#if preview}

{@html preview}

{:else}
Loading...
{/if}
</div>
<style>
	
	.preview{
		padding: 20px;
		background-color: white;
		min-height: 100%;
	}
	
</style>