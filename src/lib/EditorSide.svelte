<script>
  import Gallery from "../widgets/Gallery.svelte"

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
  
  {#if curFields[key].type == 'gal'}
  
  <label>{curFields[key].title.replaceAll('_', ' ')}</label>
  <Gallery bind:item={data.posts[index][curFields[key].title]} key="0" bind:settings={data.settings}  />
  
  {/if}
  
  
  
  {#if curFields[key].type == 'txta'}
  <label>{curFields[key].title.replaceAll('_', ' ')}</label>
  
  <textarea class="form-control" bind:value={data.posts[index][curFields[key].title]}></textarea>
  
  {/if}
  
  
  {#if curFields[key].type == 'subcategory'}
  
  <label>{curFields[key].title.replaceAll('_', ' ')}</label>
   
   {#if curFields[key].type=='subcategory'}
   {#if cat.subcategories}
   <select bind:value={data.posts[index][curFields[key].title]} class="form-select">
 
   {#each cat.subcategories as subcat}
    
    <option value={subcat.id}>
       {subcat.title}
     </option>
  
    {/each}
    </select>
    {/if}
    {/if}
    
    {/if}
  
  {/each}
