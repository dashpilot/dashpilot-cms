<script>
	export let data;
	export let curPost;
	console.log(curPost)
	let item = data.posts.filter(x=>x.id==curPost)[0];
</script>

<div class="preview">
	
<h3>{item.title}</h3>
{@html item.body}

</div>