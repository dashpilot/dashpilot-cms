<svelte:head>
<link rel="stylesheet" href="https://frontsome-sveltekit.vercel.app/global.css">
</svelte:head>

<script>
import { onMount } from 'svelte';

export let data;
export let postId;

let post = data.posts.filter(x=>x.id=postId)[0]
let preview;
	
	
onMount(async () => {
	
var template = Handlebars.compile(`
	<h2>{{title}}</h2>
	
	{{{body}}}
	
`);
// execute the compiled template and print the output to the console
preview = template(post);

});	
</script>

<div class="preview">
{@html preview}
</div>

<style>
	
	.preview{
		padding: 20px;
		background-color: white;
		min-height: 100%;
	}
	
</style>