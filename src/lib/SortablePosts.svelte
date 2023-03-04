<script>
	import Sortable from "svelte-sortable"
	
	export let items;
	export let data;
	export let catId;
	let options = {handle: '.handle'}
	
 
	function onChange() {
		//`items` are mutated
		console.log(items)
		let nothere = data.posts.filter(x => x.category !== catId)
		data.posts = items.concat(nothere);
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
			<div class="col-9 text-truncate">
				
				<i class="fas fa-grip-vertical handle"></i> &nbsp;&nbsp;
				
				<a href="/post/{item.id}" data-navigo>
				{#if item.title==''}Untitled{:else}{item.title}{/if}
				</a>
				
			</div>
			<div class="col-3 text-end">
				<button class="btn btn-outline-secondary btn-delete" on:click={()=>deletePost(item.id)}><i class="fas fa-trash"></i></button>
				
			</div>
		</div>	
		
		
		
	</li>
</Sortable>

</ul>

<!--
<script>
import {dndzone} from "svelte-dnd-action";
import {flip} from "svelte/animate";

export let items;
export let data;
export let catId;


const flipDurationMs = 300;
  
  function handleDndConsider(e) {
	items = e.detail.items;
  }
  function handleDndFinalize(e) {
	items = e.detail.items;
	// get items not in this cat
	let nothere = data.posts.filter(x => x.category !== catId)
	data.posts = items.concat(nothere);
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

	
  <ul class="list-group entries-list" use:dndzone="{{items, flipDurationMs}}" on:consider="{handleDndConsider}" on:finalize="{handleDndFinalize}">
	{#each items as item(item.id)}
	<li class="list-group-item item-list" animate:flip="{{duration: flipDurationMs}}">
		
	<div class="row">
		<div class="col-9 text-truncate"><a href="/post/{item.id}" data-navigo>
			
			{#if item.title==''}Untitled{:else}{item.title}{/if}
			
			
		</a></div>
		<div class="col-3 text-end">
			<button class="btn btn-outline-secondary btn-delete" on:click={()=>deletePost(item.id)}><i class="fas fa-trash"></i></button>
			
		</div>
	</div>	
		</li>
	{/each}
  </ul>
  
  
 
  -->
  
  <style>
		  .item-list{
			border: 1px solid #D2D2D2;
			border-bottom: 0;
			padding: 15px;
		
		  }
		  
		  .item-list:last-child{
			border-bottom: 1px solid #D2D2D2;
		  }
		  
		  .item-list a{
			color: black;
		  }
		  
		  .item-list a:hover{
			text-decoration: underline;
		  }
		  
		  .col-9{
			  padding-top: 6px;
			  padding-left: 20px;
			  color: black;
		  }
		  
		  .col-9 a{
			  color: black;
		  }
		  
		  .handle{
			  cursor: grab;
		  }
		  
	</style>