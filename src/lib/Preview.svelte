<script>
	export let data;
	export let postId;
	

	let item = data.posts.filter(x=>x.id==postId)[0];
	let index = data.posts.findIndex(x=>x.id==postId);
	
</script>

<div class="preview">
	
<h3>{data.posts[index].title}</h3>
{@html data.posts[index].body}

</div>