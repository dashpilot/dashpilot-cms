<script>
	import Sortable from "svelte-sortable"
	
	export let items;
	export let data;
	export let catId;
	let options = {handle: '.handle'}
	
	let cat = data.categories.filter(x=>x.id==catId)[0];
	
 
	function onChange() {
		console.log('changed')
		//`items` are mutated
		console.log(items)
		let nothere = data.posts.filter(x => x.category !== catId)
		data.posts = items.concat(nothere);
		// items = items;
		data = data;
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
 
</script>

	
  <ul class="list-group entries-list">
 
<Sortable {items}
		  let:item={item}
		  on:change={onChange} bind:options={options}>
	<li class="list-group-item">
	
		
		<div class="row">
			<div class="col-8 text-truncate">
				
				<i class="fas fa-grip-vertical handle"></i> &nbsp;&nbsp;
				
				<a href="/post/{item.id}" data-navigo>
				{#if item.title==''}Untitled{:else}{item.title.replace(/(<([^>]+)>)/gi, "")}{/if}
				</a>
				
				{#if item.draft}
				<span class="badge rounded-pill bg-secondary ms-2">draft</span>
				{/if}
				
			
				
			</div>
			<div class="col-4 text-end">
				{#if item.subcategory}
				<span class="badge rounded-pill bg-primary me-2">{cat.subcategories.filter(x=>x.id==item.subcategory)[0].title}</span>
				{/if}
				
			<i class="fas fa-trash" on:click={()=>deletePost(item.id)}></i>
				
			</div>
		</div>	
		
		
		
	</li>
</Sortable>

</ul>

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
		  }
		  
		  .fa-trash{
			  cursor: pointer;
		  }
		  
	</style>