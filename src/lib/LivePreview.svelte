
<script>
	import SvelteMarkdown from 'svelte-markdown'
	
	export let data;
	export let postId;
	let item = data.posts.filter(x=>x.id==postId)[0]
	let cat = data.categories.filter(x=>x.id==item.category)[0]
	let type = data.types.filter(x=>x.slug==cat.type)[0]
	let fields = type.fields;
	
$: item = data.posts.filter(x=>x.id==postId)[0]

let css  = data.settings.preview_css;


 

</script>

<div class="preview">
	


<h2>{item.title}</h2>

{#each fields as field}
	{#if field.type=='rte'}
		{@html item[field.title]}
	{/if}
	
	{#if field.type=='mde'}
		<SvelteMarkdown bind:source={item[field.title]} />
	{/if}
	
	{#if field.type=='txta'}
		{item[field.title]}
	{/if}

{/each}



{@html `<style>${css}</style>`}
</div>


<style>
	.preview{
		padding-top: 20px;
		font-synthesis: none;
		text-rendering: optimizeLegibility;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		-webkit-text-size-adjust: 100%;
	}
</style>