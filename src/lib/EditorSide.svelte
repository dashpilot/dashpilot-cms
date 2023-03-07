<script>

	export let postId;
	export let data; 
	
	let index;
	let post;
	let cat;
	let curType;
	let curFields;


	index = data.posts.findIndex(x=>x.id==postId);
	post = data.posts.filter(x=>x.id==postId)[0];
		
	cat = data.categories.filter(x=>x.id==post.category)[0]
	curType = data.types.filter(x=>x.slug==cat.type)[0];
	curFields = curType.fields;


	
</script>
 

  {#each Object.keys(curFields) as key}


 
 {#if curFields[key].type == 'txt'}
 
 <label>{curFields[key].title.replaceAll('_', ' ')}</label>
  
  <input type="text" class="form-control" bind:value={data.posts[index][curFields[key].title]}>
  
  {/if}
  
  
  {#if curFields[key].type == 'subcategory'}
  
  <label>{curFields[key].title.replaceAll('_', ' ')}</label>
   
   {#if curFields[key].type=='subcategory'}
   <select bind:value={data.posts[index][curFields[key].title]} class="form-control">
 
   {#each cat.subcategories as subcat}
    
    <option value={subcat}>
       {subcat}
     </option>
  
    {/each}
    </select>
    
    {/if}
    
    {/if}
  
  {/each}
