
<script>
	export let data;
	export let postId;
	let item = data.posts.filter(x=>x.id==postId)[0]
	
$: item = data.posts.filter(x=>x.id==postId)[0]

let css  = data.settings.preview_css;

</script>

<div class="preview">

<h2>{item.title}</h2>


<div>
{@html item.body}
</div>

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