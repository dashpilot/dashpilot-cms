<script>
	import TextEditor from "../widgets/TextEditor.svelte"
	import Markdown from "../widgets/Markdown.svelte"
  import Gallery from "../widgets/Gallery.svelte"

	import {slugify} from './Helpers.svelte';
	
	
	export let postId;
	export let data; 
  export let curTab;
	
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

	function updateSlug(){
    if(data.settings.has_id){
      data.posts[index].slug = slugify(data.posts[index].title, post.id);
    }else{
      data.posts[index].slug = slugify(data.posts[index].title, false);
    }	
	}
	
</script>
 
{#if curTab=='title'}
  
  <label>Title</label>
  <input type="text" class="form-control" bind:value={data.posts[index].title} on:keyup={updateSlug}>
  
  {#each Object.keys(curFields) as key}




  {#if curFields[key].type == 'rte'}
  
  {#if curFields[key].type !== 'txt'}
  <label>{curFields[key].title}</label>
  {/if}

  <TextEditor bind:html={data.posts[index][curFields[key].title]} />

  {/if}
  
  {#if curFields[key].type == 'mde'}
  
  {#if curFields[key].type !== 'txt'}
  <label>{curFields[key].title}</label>
  {/if}
  
	<Markdown bind:html={data.posts[index][curFields[key].title]} />
  
  {/if}
  
  {/each}
  
  {/if}
  
  
 {#if curTab=='images'} 
 
 {#each Object.keys(curFields) as key}
    
     {#if curFields[key].type == 'gal'}
     
     <label>{curFields[key].title.replaceAll('_', ' ')}</label>
     <Gallery bind:item={data.posts[index][curFields[key].title]} key="0" bind:settings={data.settings}  />
     
     {/if}
     {/each}
 {/if} 
  
  
 {#if curTab=='options'}   
    {#each Object.keys(curFields) as key}
   
   {#if curFields[key].type == 'txt'}
   
   <label>{curFields[key].title.replaceAll('_', ' ')}</label>
    
    <input type="text" class="form-control" bind:value={data.posts[index][curFields[key].title]}>
    
    {/if}


    
    {#if curFields[key].type == 'switch'}
    
    <label>{curFields[key].title.replaceAll('_', ' ')}</label>
    <div class="form-check form-switch">
       <input class="form-check-input" type="checkbox" id="flexSwitchCheckDefault" bind:checked={data.posts[index][curFields[key].title]}>
     </div>
     {/if}
    
    
    
    {#if curFields[key].type == 'txta'}
    <label>{curFields[key].title.replaceAll('_', ' ')}</label>
    
    <textarea class="form-control" bind:value={data.posts[index][curFields[key].title]}></textarea>
    
    {/if}
    
    <!--
    {#if curFields[key].type == 'subcategory'}
    {#if cat.subcategories}
    <label>{curFields[key].title.replaceAll('_', ' ')}</label>
     
     {#if curFields[key].type=='subcategory'}
  
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
    -->
    
    {/each}
    
    {/if}

