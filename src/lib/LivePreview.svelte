
<script>
	export let data;
	export let postId;
	let item = data.posts.filter(x=>x.id==postId)[0]
	
$: item = data.posts.filter(x=>x.id==postId)[0]



let css = `
@import url("https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=Playfair+Display:wght@500&display=swap");

h2{
	font-family: 'DM Serif Display', serif; font-weight: 600; font-size: 35px; margin-bottom: 25px; line-height: 1.2"
}

p{
	font-size: 19px; line-height: 29px; font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
}
`

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