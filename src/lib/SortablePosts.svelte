<script>
	import Sortable from "svelte-sortable"
	
	export let postId;
	export let items;
	export let data;
	export let catId;
	export let activeSub;
	export let router;
	let options = {handle: '.handle'}
	
	

 
	function onChange() {
		console.log('changed')
	
		let nothere = data.posts.filter(x => x.category !== catId)
		data.posts = items.concat(nothere);
		
		data = data;
//items = data.posts.filter(x=>x.category==catId);
	
	}
	
	function deletePost(id){
		  var sure = confirm("Are you sure you wish to delete this item?")
		  if(sure){
			  let index = data.posts.findIndex(x=>x.id==id);
			  console.log(index)
			  data.posts.splice(index, 1);
			  data = data;
			  items = data.posts.filter(x=>x.category==catId)
		  }
	  }
	  
	   
	   function truncateString(str, no_words) {
		 str = str.replace(/(<([^>]+)>)/gi, "");
		 return str.split(" ").splice(0,no_words).join(" ").replace('**', '');
	   }
	   function stripTags(str){
		 return str.replace(/(<([^>]+)>)/gi, "");
	   } 
	   
	   function edit(id){
		   router.navigate("/post/"+id);
		   if(window.innerWidth<1024){
			 showPostList = false;
			 
		   }
		 }
 
</script>



{#if items.length}
	
  <div class="list-group" id="post-list">
 
<Sortable {items}
		  let:item={item}
		  on:change={onChange} bind:options={options}>


	  
		<a class="list-group-item" class:active2={postId==item.id} on:click={()=>edit(item.id)} data-navigo>
			
			<i class="fas fa-grip-vertical  handle"></i>
		  
		  <b>{stripTags(item.title)}</b>
		  <br>
		  <span>{truncateString(item.body, 7)}...</span>
		  
		</a>
	  

</Sortable>

</div>

{:else}
<div class="text-center empty-container">
<i class="fa-regular fa-folder-open empty"></i>
<div>No posts found</div>
</div>

{/if}

  <style>
	
		  .col-9 a{
			  color: black;
		  }
		  
		  .col-9 a:hover{
			  color: black;
			  text-decoration: underline;
		  }
		  
		  .list-group-item{
			  padding: 15px;
			  padding-left: 20px;
			  padding-right: 20px;
		  }
		  
		  .handle{
			  cursor: grab;
			  position: absolute;
			  left: 15px;
			  top: 32px;
			  color: #CFD3DA;
		  }
		  
		  .handle:hover{
			  color: black;
		  }
		  
		  .fa-trash{
			  cursor: pointer;
			  
		  }
		
		  
	</style>