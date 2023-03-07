<script>
import Sortable from "svelte-sortable"
import {flip} from "svelte/animate";

export let data;
let items = data.categories;

let options = {handle: '.handle'}


function onChange() {
	data.categories = items;
	items = items;
	data = data;
}


  
  function deleteCat(id){
	  var sure = confirm("Are you sure you wish to delete this item?")
	  if(sure){
		  let index = data.categories.findIndex(x=>x.id==id);
		  data.categories.splice(index, 1);
		  data = data;
		  items = items;
	  }
  }
</script>

<ul class="list-group">
<Sortable {items}
		  let:item={item}
		  on:change={onChange} bind:options={options}>
	<li class="list-group-item">
	
		
		<div class="row">
			<div class="col-9 text-truncate">
				
				<i class="fas fa-grip-vertical handle"></i> &nbsp;&nbsp;
				
				<a href="/category/{item.id}/edit" data-navigo>
				{#if item.title==''}Untitled{:else}{item.title}{/if}
				</a>
				
			</div>
			<div class="col-3 text-end">
			<i class="fas fa-trash" on:click={()=>deleteCat(item.id)}></i>
				
			</div>
		</div>	
		
		
		
	</li>
</Sortable>
</ul>


	
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