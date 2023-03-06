<script>
	import TextEditor from "../widgets/TextEditor.svelte"
	import Markdown from "../widgets/Markdown.svelte"
	import Gallery from "../widgets/Gallery.svelte"
	import {slugify} from './Helpers.svelte';
	
	
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

	function updateSlug(){
		data.posts[index].slug = slugify(data.posts[index].title, post.id);
	}
	
</script>
 

  
  <label>Title</label>
  <input type="text" class="form-control" bind:value={data.posts[index].title} on:keyup={updateSlug}>
  
  {#each Object.keys(curFields) as key}

  <label>{curFields[key].title}</label>
 
  {#if curFields[key].type == 'rte'}

  <TextEditor bind:html={data.posts[index][curFields[key].title]} />

  {/if}
  
  {#if curFields[key].type == 'mde'}
  
	<Markdown bind:html={data.posts[index][curFields[key].title]} />
  
  {/if}
  
  {#if curFields[key].type == 'gal'}

  
  <Gallery bind:item={data.posts[index][curFields[key].title]} key="0" bind:settings={data.settings}  />
  
  {/if}
  
 
  
  {#if curFields[key].type == 'txta'}
  
	
	<textarea class="form-control" bind:value={data.posts[index][curFields[key].title]}></textarea>
	
	{/if}
  
  
  {/each}
