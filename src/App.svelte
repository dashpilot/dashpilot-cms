<svelte:head>
 <script src="https://cdn.jsdelivr.net/npm/handlebars@latest/dist/handlebars.js"></script>
</svelte:head>
<script>
  import { onMount } from 'svelte';
  import { tick } from 'svelte';
  import Navigo from "navigo";
 
  import {slugify} from "./lib/Helpers.svelte";
  import Editor from "./lib/Editor.svelte";
  import EditorSide from "./lib/EditorSide.svelte";
  
  import Card from "./lib/Card.svelte";
  import Publish from "./lib/Publish.svelte";
  import Preview from "./lib/Preview.svelte";

  
  // import SortablePosts from "./lib/SortablePosts.svelte";
  import SortableCategories from "./lib/SortableCategories.svelte";
  import SortableSubs from "./lib/SortableSubs.svelte";
  import ProfileCard from "./lib/ProfileCard.svelte";
  
  import Tabs from "./lib/Tabs.svelte";

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
  let items;
  let catId;
  let cat;
  let postId;
  let action = 'edit';
  
  let latest;
  let count;
  
  let showPublish;
  let showPreview = false;
  
  
  let activeSub = 0;
  let showPostList = true;
  
  let curTab = 'title';

  
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
    
   
    router.on("/posts/:catid", async function (props) {
      show = 'posts'
      activeSub=0;
    
      catId = props.data.catid;
    
      
    
      showSave = true;
      
      let catExists = data.categories.filter(x=>x.id==catId)[0];
      if(!catExists){
        router.navigate('/posts/1')
      }
      
      cat = data.categories.filter(x=>x.id==catId)[0];
      items = data.posts.filter(x=>x.category==catId);
  
      tab = "cat-"+props.data.catid;
      
      hydrate()
    });
    
    router.on("/post/:id", async function (props) {    
      show = 'post'
      
     
      postId = props.data.id;
    
      showSave = true;
      
      let postExists = data.posts.filter(x=>x.id==postId)[0];
      if(!postExists){
        router.navigate('/posts/1')
      }
      
      catId = postExists.category;
      
      tab = "cat-"+postExists.category;
      
      hydrate()
    });
    
      
    router.on("/categories", async function (props) {    
      show = 'categories'
      tab = 'categories'
      showSave = true;
      
      hydrate()
    });
    
   
  
    router.on("/category/:id/:action", async function (props) {    
      show = 'category'
      tab = 'categories'
      showSave = true;
      action = props.data.action;
      let catId = props.data.id;
      cat = data.categories.filter(x=>x.id==catId)[0];
      if(!cat){
        router.navigate('/categories')
      }
      
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
  
  function truncateString(str, no_words) {
    str = str.replace(/(<([^>]+)>)/gi, "");
    return str.split(" ").splice(0,no_words).join(" ").replace('**', '');
  }
  function stripTags(str){
    return str.replace(/(<([^>]+)>)/gi, "");
  }
  
 
  function latestPost(){
    let highest_id = Math.max(...data.posts.map(x => x.id));
    return data.posts.filter(x=>x.id==highest_id)[0]
  }

  function newId(){
    let highest_id = 0;
    if(data.posts.length){
      highest_id = Math.max(...data.posts.map(x => x.id));
    }
    return highest_id+1;
  }
  
  function newCatId(){
    let highest_id = 0;
    if(data.categories.length){
      highest_id = Math.max(...data.categories.map(x => x.id));
    }
    return highest_id+1;
  }
  

  
  async function addPost(){
    
 
    
    let cat = data.categories.filter(x=>x.id==catId)[0];
    let curType = data.types.filter(x=>x.slug==cat.type)[0];
  
    let newPost = {}
    let id = newId();
    
    newPost.title = "";
    newPost.id = id;
    newPost.category = cat.id;
    
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
    let id = newCatId();
    
    newCat.title = "";
    newCat.id = id;
    newCat.type = "post"
    
    newCat.slug = slugify("category", id);
    data.categories.push(newCat)
    data = data;
   
    hydrate()
    router.navigate('/category/'+id+'/add')
    
  
  }
  
  function updateCatSlug(id){

    let index = data.categories.findIndex(x=>x.id==id)
    var slug = slugify(data.categories[index].title, false);
    
    var exists = data.categories.filter(x=>x.slug==slug)[0]
    if(exists){
      console.log('exists')
      slug = slugify(data.categories[index].title, id)
    }
   
    data.categories[index].slug = slug;
    data.categories = data.categories;
   document.getElementById('cat-slug').innerText = slug;
    
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
  
  function preview(){
    save();
    showPreview=true;
  }
  
  function publish(){
    save();
    showPublish=true;
  }
  
  function closeDropDown(){
    document.querySelector('#cat-dropdown').classList.remove('show');
  }
  
  function openFolder(catId){
    let highest_id = 0;
    let catPosts = data.posts.filter(x=>x.category==catId);
    let path;
    if(catPosts.length){
      highest_id = catPosts.at(0).id;
      //highest_id = Math.max(...catPosts.map(x => x.id));
      path = "post/"+highest_id;
      
    }else{
      path = "posts/"+catId;
    }
    
    router.navigate(path)
    showPostList = true;
    
  }
  
  function edit(id){
    router.navigate("/post/"+id);
    if(window.innerWidth<1024){
      showPostList = false;
      
    }
  }

</script>

 {#if showSave}
 

<div class="saveBtns">
 <button class="btn btn-dark" on:click="{save}">{#if saving}<i class="fas fa-spinner fa-spin"></i> {:else}<i class="fas fa-save"></i> {/if}<span class="mob-hide">&nbsp;Save</span></button>
 
 {#if cfg.live_url}    
  <button class="btn btn-dark" on:click="{preview}"><i class="fas fa-binoculars"></i><span class="mob-hide"> &nbsp;&nbsp;Save &amp; View</span></button>
  {/if}

 <button class="btn btn-dark" on:click="{publish}"><i class="fas fa-rocket"></i><span class="mob-hide"> &nbsp;Publish</span></button>
</div>
 
 {/if}
 


<nav>
  <header></header>
  
  
  <ul>
    
    <li>
      
      <a href="/" class:active={tab === 'dashboard'}  data-navigo><i class="fa-solid fa-gauge"></i> <span class="mob-hide">Dashboard</span></a></li>

    <!--
    <li><a href="/posts/{default_cat}" class:active={tab === 'posts'}  data-navigo><i class="fa-solid fa-bolt"></i> <span class="mob-hide">Posts</span></a></li>
    -->
    
    {#if data}
    {#each data.categories as item}
    <li><a on:click={()=>openFolder(item.id)} class="indent" class:active={tab === "cat-"+item.id}  data-navigo>{#if tab=="cat-"+item.id}<i class="fa-solid fa-folder-open"></i>{:else}<i class="fa-solid fa-folder"></i>{/if} <span class="mob-hide">{item.title}</span></a></li>
    {/each}
    {/if}
    
    
    <li><a href="/categories" class:active={tab === 'categories'}  data-navigo><i class="fa-solid fa-tag"></i> <span class="mob-hide">Categories</span></a></li>
    
    <li><a href="/settings" class:active={tab === 'settings'}  data-navigo><i class="fa-solid fa-gear"></i> <span class="mob-hide">Settings</span></a></li>
    
    
    
    <li><a href="/account" class:active={tab === 'account'} data-navigo><i class="fa-solid fa-user-astronaut"></i> <span class="mob-hide">Account</span></a></li>

  </ul>
  

  
</nav>

{#if show == 'post' && showPostList}
<!-- col 2 -->
<div class="col-3 col2">

  
  <div class="list-group" id="post-list">
    
 
    {#each data.posts.filter(x=>x.category==catId) as item}
  
    <a class="list-group-item" class:active2={postId==item.id} on:click={()=>edit(item.id)} data-navigo>
      
      <b>{stripTags(item.title)}</b>
      <br>
      <span>{truncateString(item.body, 7)}...</span>
      
    </a>
  
    {/each}
 
    
    
    <!--  class:active2="{curPostId === item.id}"
      on:click={()=>curPostId=item.id} -->
   
  
  </div>
  

  
</div>
{/if}





<main class="min-vh-100">
  
{#if show=='dashboard'}
<header>
  
  <h5>Dashboard</h5>
  
</header>
<div class="content">
  
  <div class="row">
    <div class="col-md-4">
      <Card title="Posts" bind:number={data.posts.length} msg="&nbsp;in {data.categories.length} categories" icon="fa-bolt" icon2="fa-tag" muted="" />
      
    </div>
    <div class="col-md-4">
     
      <Card title="Latest Post" number="{latest.title}" msg="&nbsp; {count} words" icon="fa-clock" icon2="fa-solid fa-calculator" muted="" />
    
    </div>
    <div class="col-md-4">
      <Card title="Version" number="0.4.3" msg="&nbsp;up to date" icon="fa-rocket" icon2="fa-check"  muted="" />
      
    </div>
  </div>
  
</div>

{/if}
  
 
    
    {#if show=='posts'}

    <header>
 
     <button class="btn btn-dark mb-3 ms-1" on:click={addPost}><i class="fas fa-plus"></i></button>
      </header>
  
    <div class="row g-0 min-vh-100">

   
    <div class="content">
      
      
      <div class="text-center empty-container">
      <i class="fa-regular fa-folder-open empty"></i>
      <div>No posts found</div>
      </div>

<!--
      {#if data.settings.has_subcategories}
         {#if cat.subcategories && cat.subcategories.length}
         <ul class="nav nav-pills mb-3">
           
           <li class="nav-item">
           
             <a class="nav-link" class:active={activeSub==0} on:click={()=>activeSub=0}>All</a>
           
            
           </li>
           {#each cat.subcategories as sub, i}
           
           <li class="nav-item">
          
              <a class="nav-link" class:active={activeSub==sub.id} on:click={()=>activeSub=sub.id}>{sub.title}</a>
        
             
            </li>
           {/each}
           </ul>
         {/if}
       {/if}
    
      {#key catId}
      <SortablePosts bind:items bind:data bind:catId bind:activeSub />
      {/key}
    -->
    
    </div>
    
    </div>
  
 {/if}
  
    {#if show=='post'}
   <header>
   


  <button class="btn btn-dark mb-3 ms-1" on:click={addPost}><i class="fas fa-plus"></i></button>


 
 </header>
      
      {#key postId}
      <div class="row g-0 h-fill">
    
    
        <div class="content no-pad col-editor pb-5 post-editor">
          <Tabs bind:curTab />
       
    <Editor bind:data bind:postId bind:curTab />
      <!-- <EditorSide bind:data bind:postId />-->
        
        </div>
        
      </div>
     
   {#if showPreview} 
     <Preview bind:data bind:postId bind:showPreview />
    {/if}

      
      {/key}

    {/if}
    
   
   
   {#if show=='categories'}
  
 
     <header>
        
    <button class="btn btn-dark mb-3 ms-auto" on:click={addCat}><i class="fas fa-plus"></i></button>
   
     </header>


    <div class="row g-0 h-fill">
      <div class="col-md-8 min-vh-100">
       <div class="content">
    
         <SortableCategories bind:data />
        
       </div>
      </div>
      
      <div class="col-md-4 min-vh-100 sidebar">
       
       </div>
       </div>
      
  
    {/if}
    
    
    {#if show=='category'}
    
    
       <header>
      <h5>{#if action=='add'}Add{:else}Edit{/if} Category</h5>
       </header>
       
       
       <div class="row g-0 h-fill">
       <div class="col-md-8 min-vh-100">
       
         <div class="content no-pad col-editor">
       
       <label>Category title</label>
       
       {#if action=='add'}
        <input type="text" class="form-control" bind:value={cat.title} on:keyup={()=>updateCatSlug(cat.id)}>
        
      
        <label>Category Slug</label>
        
        <div class="alert alert-warning" id="cat-slug">category-{cat.id}</div>
       
  
        {:else}
        <input type="text" class="form-control" bind:value={cat.title}>
        
     <!--
        <label>Category Slug</label>
        <div class="alert alert-warning" id="cat-slug">{cat.slug}</div>
        -->
        
        {#if data.settings.has_subcategories}
        
       <SortableSubs bind:data bind:id={cat.id} />
        
        {/if}
    
        
        {/if}
        
       
       
         </div>
       </div>
       <div class="col-md-4 min-vh-100 sidebar">
       
       </div>
       </div>
  
    
      {/if}
    
    
    
    {#if show=='settings'}
    <header>
      <h5>Settings</h5>
    </header>
    
        
    <div class="row g-0 h-fill">
    <div class="col-md-8 min-vh-100">
    
      <div class="content no-pad col-editor">
     {#each Object.keys(data.settings) as key, val}
     <label>{key.replaceAll('_', ' ')}</label>
     
     {#if key.includes('has')}
    
     
     <div class="form-check form-switch">
       <input class="form-check-input" type="checkbox" id="flexSwitchCheckDefault" bind:checked={data.settings[key]}>
     </div>
     
     {:else}
     <input type="text" class="form-control" bind:value={data.settings[key]} />
     {/if}
     {/each}
      </div>
    </div>
    <div class="col-md-4 min-vh-100 sidebar">
   
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

{#if showPublish}
<Publish bind:showPublish />
{/if}


<style>
 
  nav{
    position: fixed;
    top: 0;
    left: 0;
    height: 100%;
    width:200px;
    background-color: #333333;
  }
  
  main{
    padding-left: 200px;
   
  }
  
 
  
  main .row{
    padding-top: 60px;
  }
  
  header{
  
   position: fixed;
   width: 100%;
   left: 200px;
   z-index: 9999999;
  }
  
  header{
    height: 60px;
  }
  
  nav header{
    position: relative;
    height: 60px;
    border-bottom: 5px solid #333333;
  }
  
  main header{
    border-bottom: 5px solid black;
    background-color: white;
    padding: 8px 20px;
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
    user-select: none;
    cursor: pointer;
  }
  
  nav a.active, nav a:hover{
    background-color: #666BEF;
    color: white;
  }
  
  .indent{
    padding-left: 45px;
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
  
  .posts-side{
    padding: 19px;
  }
  
  .content{
    padding: 20px;
  }
  
  .content.no-pad{
    padding-top: 5px;
  }
  
  .sidebar{
    border-left: 1px solid #d2d2d2;
    background-color: white;
    padding: 5px 30px;
   
  }
  
  
  .editor-sidebar{
    border-left: 1px solid #d2d2d2;
    background-color: white;
    padding: 5px 30px;
    overflow: hidden;
    overflow-y: auto;
    padding-bottom: 30px !important;
  }
  
  .brdr-start{
    border-left: 1px solid #d2d2d2;
  }

  .h-fill{
    height: calc(100% - 60px);
  }
  
  #preview_css{
    height: 200px;
  }
  
 
  
  .empty-container{
    color: #777;
  }
  
  .empty{
    font-size: 40px;
    margin-top: 40px;
    margin-bottom: 5px;
   
  }

</style>