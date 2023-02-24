<script>
	import TextEditor from "../widgets/TextEditor.svelte"
	import Gallery from "../widgets/Gallery.svelte"
	import {slugify} from './Helpers.svelte';
	
	export let curPostId;
	export let data; 
	let curPost;
	let curCat;
	let curType;
	let curFields;
	let curIndex;
	
	curIndex = data.posts.findIndex(x=>x.id==curPostId);
	curPost = data.posts.filter(x=>x.id==curPostId)[0];
	
	curCat = data.categories.filter(x=>x.slug==curPost.category)[0]
	curType = data.types.filter(x=>x.slug==curCat.type)[0];
	curFields = curType.fields;
	

	
	function updateSlug(){
		data.posts[curIndex].slug = slugify(data.posts[curIndex].title, curPost.id);
	}
</script>
 

  
  <label>Title</label>
  <input type="text" class="form-control" bind:value={data.posts[curIndex].title} on:keyup={updateSlug}>
  
  {#each Object.keys(curFields) as key}

  <label>{curFields[key].title}</label>
 
  {#if curFields[key].type == 'rte'}

  <TextEditor bind:html={data.posts[curIndex][curFields[key].title]} />

  {/if}
  
  {#if curFields[key].type == 'gal'}

  
  <Gallery bind:item={data.posts[curIndex][curFields[key].title]} key="0" bind:settings={data.settings}  />
  
  {/if}
  
  {#if curFields[key].type == 'txt'}
  
	<input type="text" class="form-control" bind:value={data.posts[curIndex][curFields[key].title]}>
  
	{/if}
  
  {#if curFields[key].type == 'txta'}
  
	
	<textarea class="form-control" bind:value={data.posts[curIndex][curFields[key].title]}></textarea>
	
	{/if}
  
  
  {/each}

  
 