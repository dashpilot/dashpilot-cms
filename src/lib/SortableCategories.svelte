<script>
import {dndzone} from "svelte-dnd-action";
import {flip} from "svelte/animate";

export let data;
let items = data.categories;


const flipDurationMs = 300;
  
  function handleDndConsider(e) {
	items = e.detail.items;
  }
  function handleDndFinalize(e) {
	items = e.detail.items;
	data.categories = items;
	console.log(data)
  }
  
  function deleteCat(id){
	  var sure = confirm("Are you sure you wish to delete this item?")
	  if(sure){
		  let index = data.categories.findIndex(x=>x.id==id);
		  data.categories.splice(index, 1);
		  data = data;
	  }
  }
</script>

	
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
		}
		
		.btn-opacity{
			opacity: 0;
		}
  </style>