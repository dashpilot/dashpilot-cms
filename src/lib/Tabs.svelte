<script>
  export let data;
  export let postId;
	export let curTab;
  
  let post = data.posts.filter(x=>x.id==postId)[0]
  let cat = data.categories.filter(x=>x.id==post.category)[0]
  let curType = data.types.filter(x=>x.slug==cat.type)[0];
  let curFields = curType.fields;
  
  let hasImg = curFields.find(x => x.type === 'gal');
  let hasOptions = curFields.find(x => x.type === 'txt');
  let hasSettings = curFields.find(x => x.type === 'switch');
</script>

<div class="tabs">
  <ul>
	<li class:is-active={curTab=='title'} on:click={()=>curTab='title'}><a>Text</a></li>
  
  {#if hasImg}
  <li class:is-active={curTab=='images'} on:click={()=>curTab='images'}><a>Images</a></li>
  {/if}
  
  {#if hasOptions}
 <li class:is-active={curTab=='options'} on:click={()=>curTab='options'}><a>Options</a></li>
 {/if}
 
 {#if hasSettings}
 <li class:is-active={curTab=='settings'} on:click={()=>curTab='settings'}><a>Settings</a></li>
 {/if}
  </ul>
</div>

<style>
.tabs {
  -webkit-overflow-scrolling: touch;
  align-items: stretch;
  display: flex;
  font-size: 0.8rem;
  justify-content: space-between;
  overflow: hidden;
  overflow-x: auto;
  white-space: nowrap;
}

.tabs a {
  align-items: center;
  border-bottom-color: #dbdbdb;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  color: #4a4a4a;
  display: flex;
  justify-content: center;
  margin-bottom: -1px;
  padding: 0.5em 1em;
  vertical-align: top;
  font-size: 16px;
}

.tabs a:hover {
  border-bottom-color: #363636;
  color: #363636;
  cursor: pointer;
}

.tabs li {
  display: block;
}

.tabs li.is-active a {
  border-bottom-color: #485fc7;
  color: #485fc7;
}

.tabs ul {
  align-items: center;
  border-bottom-color: #dbdbdb;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  display: flex;
  flex-grow: 1;
  flex-shrink: 0;
  justify-content: flex-start;
  padding: 0;
margin-top: 10px;
}

.tabs ul.is-left {
  padding-right: 0.75em;
}

.tabs ul.is-center {
  flex: none;
  justify-content: center;
  padding-left: 0.75em;
  padding-right: 0.75em;
}

.tabs ul.is-right {
  justify-content: flex-end;
  padding-left: 0.75em;
}

.tabs .icon:first-child {
  margin-right: 0.5em;
}

.tabs .icon:last-child {
  margin-left: 0.5em;
}

.tabs.is-centered ul {
  justify-content: center;
}

.tabs.is-right ul {
  justify-content: flex-end;
}

.tabs.is-boxed a {
  border: 1px solid transparent;
  border-radius: 4px 4px 0 0;
}

.tabs.is-boxed a:hover {
  background-color: whitesmoke;
  border-bottom-color: #dbdbdb;
}

.tabs.is-boxed li.is-active a {
  background-color: white;
  border-color: #dbdbdb;
  border-bottom-color: transparent !important;
}

.tabs.is-fullwidth li {
  flex-grow: 1;
  flex-shrink: 0;
}

.tabs.is-toggle a {
  border-color: #dbdbdb;
  border-style: solid;
  border-width: 1px;
  margin-bottom: 0;
  position: relative;
}

.tabs.is-toggle a:hover {
  background-color: whitesmoke;
  border-color: #b5b5b5;
  z-index: 2;
}

.tabs.is-toggle li + li {
  margin-left: -1px;
}

.tabs.is-toggle li:first-child a {
  border-top-left-radius: 4px;
  border-bottom-left-radius: 4px;
}

.tabs.is-toggle li:last-child a {
  border-top-right-radius: 4px;
  border-bottom-right-radius: 4px;
}

.tabs.is-toggle li.is-active a {
  background-color: #485fc7;
  border-color: #485fc7;
  color: #fff;
  z-index: 1;
}

.tabs.is-toggle ul {
  border-bottom: none;
}

.tabs.is-toggle.is-toggle-rounded li:first-child a {
  border-bottom-left-radius: 9999px;
  border-top-left-radius: 9999px;
  padding-left: 1.25em;
}

.tabs.is-toggle.is-toggle-rounded li:last-child a {
  border-bottom-right-radius: 9999px;
  border-top-right-radius: 9999px;
  padding-right: 1.25em;
}

.tabs.is-small {
  font-size: 0.75rem;
}

.tabs.is-medium {
  font-size: 1.25rem;
}

.tabs.is-large {
  font-size: 1.5rem;
}
</style>