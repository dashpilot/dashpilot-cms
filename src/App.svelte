<script>
  import { onMount } from 'svelte';
  import { tick } from 'svelte';
  import Navigo from "navigo";
 
  import {slugify} from "./lib/Helpers.svelte";
  import Editor from "./lib/Editor.svelte";
  import Preview from "./lib/Preview.svelte";
  import Card from "./lib/Card.svelte";
  
  import SortablePosts from "./lib/SortablePosts.svelte";
  import SortableCategories from "./lib/SortableCategories.svelte";
  import ProfileCard from "./lib/ProfileCard.svelte";

  let data;
  let router;
  let show;
  let tab;
  let notfound;
  
  let showSave = false;
  let loading;
  let saving;
  let saved;
 
  let posts;
  let catSlug = 'home';
  let cat;
  let postId;
  
  let latest;
  let count;
  
  onMount(async () => {
    
    const res = await fetch(cfg.dataPath, {cache: "no-store"});
    data = await res.json();
    console.log(data)
    
    router = new Navigo("/");
    
    router.on("/", async function () {
      show = 'dashboard'
      tab = 'dashboard'
      showSave = false;
      
      latest = latestPost();
      count = latest.body.split(" ").length;
      
      hydrate()
    });
    
   
    router.on("/posts/:slug", async function (props) {
      show = 'posts'
      tab = 'posts'
      catSlug = props.data.slug;
      posts = data.posts.filter(x=>x.category==props.data.slug)
      showSave = true;
      
      hydrate()
    });
    
    router.on("/post/:id", async function (props) {    
      show = 'post'
      tab = 'posts'
      postId = props.data.id;
      showSave = true;
      
      hydrate()
    });
    
      
    router.on("/categories", async function (props) {    
      show = 'categories'
      tab = 'categories'
      showSave = true;
      
      hydrate()
    });
    
    router.on("/category/:id", async function (props) {    
      show = 'category'
      tab = 'categories'
      showSave = true;
      let catId = props.data.id;
      cat = data.categories.filter(x=>x.id==catId)[0];
      
      hydrate()
    });
    
    router.on("/settings", async function (props) {    
      show = 'settings'
      tab = 'settings'
      showSave = true;
      
      hydrate()
    });
    
     
    router.on("/account", async function (props) {    
      show = 'account'
      tab = 'account'
      showSave = false;
      hydrate()
    });
   
  
    router.notFound(() => {
      // this runs if there is no match found
      notfound = true;
    });
    
    
    setTimeout(()=>{
      router.resolve();
    }, 50)
  });  
  
  async function hydrate(){
    // make sure navigo updates the links on the page after the page has rendered
    await tick();
    router.updatePageLinks()
  }
 
  function latestPost(){
    let highest_id = Math.max(...data.posts.map(x => x.id));
    return data.posts.filter(x=>x.id==highest_id)[0]
  }

  function newId(){
    let highest_id = Math.max(...data.posts.map(x => x.id));
    return highest_id+1;
  }
  
  async function addPost(){
    
    let cat = data.categories.filter(x=>x.slug==catSlug)[0];
    let curType = data.types.filter(x=>x.slug==cat.type)[0];
  
    let newPost = {}
    let id = newId();
    
    newPost.title = "";
    newPost.id = id;
    newPost.category = cat.slug;
    
    curType.fields.forEach(item=>{
      if(item.type=='gal'){
        newPost[item.title] = [];
      }else{
        newPost[item.title] = "";
      }
    })
    
    newPost.slug = slugify("untitled", id);
    
    console.log(newPost)
  
    data.posts.unshift(newPost)
    data = data;
    
    posts = data.posts.filter(x=>x.category==cat.slug);
   
    hydrate()
    router.navigate('/post/'+id)
  }
  
  async function addCat(){

    let newCat = {}
    let id = newId();
    
    newCat.title = "";
    newCat.id = id;
    newCat.type = "post"
    
    newCat.slug = slugify("untitled", id);
    data.categories.push(newCat)
    data = data;
   
    hydrate()
    router.navigate('/category/'+id)
  }
  
  function updateCatSlug(id){
    let index = data.categories.findIndex(x=>x.id==id)
    data.categories[index].slug = slugify(data.categories[index].title, id);
  }
  
  function save(){
    saving = true;
    let opts = {};
    opts.path = "data.json";
    opts.type = 'json';
    opts.data = data;
    call_api('/api/save', opts).then(function(res) {
      if (res.status=='ok') {
        saving = false;
        saved = true;
        console.log('Saved');
        setTimeout(()=>{
          saved = false;
        }, 2000)
       
      } else {
        saving = false;
        console.log('Error saving');
      }
    });
  }
  
</script>

 {#if showSave}
 <button class="btn btn-dark btn-save" on:click="{save}">{#if saving}<i class="fas fa-spinner fa-spin"></i> &nbsp;{:else}<i class="fas fa-save"></i> &nbsp;{/if}Save</button>
 {/if}

<nav>
  <header></header>
  
  <ul>
    
    <li>
      
      <a href="/" class:active={tab === 'dashboard'}  data-navigo><i class="fa-solid fa-gauge"></i> <span class="mob-hide">Dashboard</span></a></li>

    <li><a href="/posts/home" class:active={tab === 'posts'}  data-navigo><i class="fa-solid fa-bolt"></i> <span class="mob-hide">Posts</span></a></li>
    
    <li><a href="/categories" class:active={tab === 'categories'}  data-navigo><i class="fa-solid fa-tag"></i> <span class="mob-hide">Categories</span></a></li>
    
    <li><a href="/settings" class:active={tab === 'settings'}  data-navigo><i class="fa-solid fa-gear"></i> <span class="mob-hide">Settings</span></a></li>
    
    
    
    <li><a href="/account" class:active={tab === 'account'} data-navigo><i class="fa-solid fa-user-astronaut"></i> <span class="mob-hide">Account</span></a></li>

  </ul>
  

  
</nav>
<main class="h-100">
  
{#if show=='dashboard'}
<header>
  
  <h5>Dashboard</h5>
  
</header>
<div class="content">
  
  <div class="row">
    <div class="col-md-4">
      <Card title="Posts" bind:number={data.posts.length} msg="&nbsp;in {data.categories.length} categories" icon="fa-bolt" icon2="fa-tag" />
      
    </div>
    <div class="col-md-4">
      
      <Card title="Latest Post" number="{latest.title}" msg="&nbsp; {count} words" icon="fa-clock" icon2="fa-solid fa-calculator" />
    </div>
    <div class="col-md-4">
      <Card title="Version" number="0.4.3" msg="&nbsp;up to date" icon="fa-rocket" icon2="fa-check" />
      
    </div>
  </div>
  
</div>

{/if}
  
 
    
    {#if show=='posts'}

    <header>
     <h5>Posts</h5>
      </header>
  
    <div class="row g-0 h-100">
    <div class="col-md-3 h-100 col-categories">
  
    <ul>
      {#each data.categories as item}
      <li><a href="/posts/{item.slug}" class:active={catSlug === item.slug} data-navigo>{#if item.title==''}Untitled{:else}{item.title}{/if}</a></li>
      {/each}
    </ul>
  </div>
    
    <div class="col-md-9 brdr-start col-posts">
   
    <div class="content">
      
      <button class="btn btn-dark mb-3" on:click={addPost}><i class="fas fa-plus"></i></button>
      
      <SortablePosts bind:items={posts} bind:data bind:cat={catSlug} />
  
     
    </div>
  </div>
    </div>
 {/if}
  
    {#if show=='post'}
   <header><h5>Edit Post</h5>
 

 
 </header>
      
      <div class="row g-0 h-fill">
      <div class="col-md-6 h-100">
    
        <div class="content no-pad col-editor pb-5">
          
       
      <Editor bind:data bind:postId />
        
        </div>
        
       
      </div>
      <div class="col-md-6 h-100 preview-screen">
     
       <Preview bind:data bind:postId />
      </div>
      </div>

    {/if}
    
   
   
   {#if show=='categories'}
  
 
     <header>
    <h5>Categories</h5>
     </header>
       
  <!--
         <header>
           
           <button class="btn btn-outline-secondary" on:click={addPost}><i class="fas fa-plus"></i></button>
           
         </header>
         -->
         
       <div class="content">
         
         <button class="btn btn-dark mb-3" on:click={addCat}><i class="fas fa-plus"></i></button>
         
         <SortableCategories bind:items={data.categories} bind:data />
        
        
       </div>
  
    {/if}
    
    
    {#if show=='category'}
    
    
       <header>
      <h5>Edit Category</h5>
       </header>
       
       
       <div class="row g-0 h-fill">
       <div class="col-md-6 h-100">
       
         <div class="content no-pad col-editor">
       
       <label>Category title</label>
        <input type="text" class="form-control" bind:value={cat.title} on:keyup={()=>updateCatSlug(cat.id)}>
       
         </div>
       </div>
       <div class="col-md-6 h-100 preview-screen">
       
       </div>
       </div>
  
    
      {/if}
    
    
    
    {#if show=='settings'}
    <header>
      
      <h5>Settings</h5>
    </header>
    
        
    <div class="row g-0 h-fill">
    <div class="col-md-6 h-100">
    
      <div class="content no-pad col-editor">
     {#each Object.keys(data.settings) as key, val}
     <label>{key.replaceAll('_', ' ')}</label>
     <input type="text" class="form-control" bind:value={data.settings[key]} />
     {/each}
      </div>
    </div>
    <div class="col-md-6 h-100 preview-screen">
   
    </div>
    </div>

    
    {/if}
    
    
    {#if show=='account'}
    <header>
      <h5>Account</h5>
    </header>
    <div class="content">
      
     <ProfileCard />
      
    </div>
    
    {/if}
    


</main>


<style>
 
  nav{
    position: fixed;
    top: 0;
    left: 0;
    height: 100%;
    width: 180px;
    background-color: #333333;
  }
  
  main{
    padding-left: 180px;
  }
  
  header{
    height: 60px;
   
  }
  
  nav header{
    border-bottom: 5px solid #333333;
  }
  
  main header{
    border-bottom: 5px solid black;
    background-color: white;
    padding: 11px 20px;
  }
  
  nav ul{
    list-style-type: none;
    padding: 0;
    margin: 0;
  }
  
  nav ul li a{
    display: block;
    padding: 15px;
    padding-left: 20px;
    color: white;
  }
  
  nav a.active, nav a:hover{
    background-color: #666BEF;
    color: white;
  }
 
  .col-categories ul{
    list-style-type: none;
    padding: 0;
    margin: 0;
    
  }
  
  .col-categories ul li a{
    display: block;
    padding: 20px 10px 23px 25px;
    color: black;
    font-weight: 600;
    border-bottom: 1px solid #d2d2d2;
    border-left: 5px solid transparent;
  }
  
  .col-categories a.active, .col-categories a:hover{
    border-left: 5px solid #666BEF;
    background-color: #F8F8F8;
  }
  
  .col-categories{
  background-color: white;
  }
  
  .content{
    padding: 20px;
  }
  
  .content.no-pad{
    padding-top: 5px;
  }
  
  .preview-screen{
    border-left: 1px solid #d2d2d2;
    background-color: white;
    padding: 20px 30px;
  }
  
  .brdr-start{
    border-left: 1px solid #d2d2d2;
  }

  .h-fill{
    height: calc(100% - 60px);
  }
  

</style>