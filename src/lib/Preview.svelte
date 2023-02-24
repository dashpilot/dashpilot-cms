<script>
	export let data;
	export let curPost;
	console.log(curPost)
	let item = data.posts.filter(x=>x.id==curPost)[0];
	let index = data.posts.findIndex(x=>x.id==curPost);
</script>

<div class="preview">
	
<h3>{data.posts[index].title}</h3>
{@html data.posts[index].body}

</div>