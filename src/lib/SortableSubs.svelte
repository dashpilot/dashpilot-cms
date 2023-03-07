<script>
import Sortable from "svelte-sortable"


export let data;
export let id;

let title = '';

let cat = data.categories.filter(x=>x.id==id)[0];
let index = data.categories.findIndex(x=>x.id==id);

let items = [];
if(cat.subcategories){
	items = cat.subcategories;
}else{
	data.categories[index].subcategories = [];
	data = data;
	items = data.categories[index].subcategories;
	
}


let options = {handle: '.handle'}


function onChange() {
	data.categories[index].subcategories = items;
	data = data;
	items = items;
	/*
	data.categories = items;
	// items = items;
	
	
	console.log(data.categories)
	*/
	
}


  
  function deleteCat(myid){
	  var sure = confirm("Are you sure you wish to delete this item?")
	  if(sure){
		  let myindex = data.categories[index].subcategories.findIndex(x=>x.id==myid);
		  data.categories[index].subcategories.splice(myindex, 1);
		  data = data;
		  items = items;
	  }
  }
  
  function newCatId(){
	  let highest_id = 1;
	  if(data.categories[index].subcategories.length){
		 highest_id = Math.max(...data.categories[index].subcategories.map(x => x.id)); 
	  }
	  return highest_id+1;
	}
	
  
  
  function addCat(){
	  let newItem = {};
	  newItem.id = newCatId();
	  newItem.title = title;
	  data.categories[index].subcategories.push(newItem);
	  data = data;
	  items = items;
	  title = '';
  }
</script>


<label>Add Subcategory</label>
<div class="input-group mb-3 w-50">
  <input type="text" class="form-control" placeholder="Subcategory title" aria-label="Recipient's username" aria-describedby="button-addon2" bind:value={title}>
  <button class="btn btn-outline-secondary" type="button" id="button-addon2" on:click={addCat}>Add</button>
</div>

{#if items.length}
<label>Subcategories</label>
<ul class="list-group">
<Sortable {items}
		  let:item={item}
		  on:change={onChange} bind:options={options}>
	<li class="list-group-item">
	
		
		<div class="row">
			<div class="col-9 text-truncate">
				
				<i class="fas fa-grip-vertical handle"></i> &nbsp;&nbsp;
				
				
				{#if item.title==''}Untitled{:else}{item.title}{/if}
			
				
			</div>
			<div class="col-3 text-end">
			<i class="fas fa-trash" on:click={()=>deleteCat(item.id)}></i>
				
			</div>
		</div>	
		
		
		
	</li>
</Sortable>
</ul>
{/if}


	
<!--
  <ul class="list-group entries-list" use:dndzone="{{items, flipDurationMs}}" on:consider="{handleDndConsider}" on:finalize="{handleDndFinalize}">
	{#each items as item(item.id)}
	<li class="list-group-item item-list" animate:flip="{{duration: flipDurationMs}}">
		
	<div class="row">
		<div class="col-9 text-truncate"><a href="/category/{item.id}/edit" data-navigo>
			
			{#if item.title==''}Untitled{:else}{item.title}{/if}
			
		</a></div>
		<div class="col-3 text-end">
			{#if item.slug!=='home'}
			<button class="btn btn-outline-secondary btn-delete" on:click={()=>deleteCat(item.id)}><i class="fas fa-trash"></i></button>
			{:else}
			<button class="btn btn-outline-secondary btn-opacity"><i class="fas fa-trash"></i></button>
			{/if}
			
		</div>
	</div>	
		</li>
	{/each}
  </ul>
  -->
  
  
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