<script>
  import { onMount } from 'svelte';
  import { tick } from 'svelte';
  import Navigo from "navigo";
 
  import {slugify} from "./lib/Helpers.svelte";
  import Editor from "./lib/Editor.svelte";
  import Preview from "./lib/Preview.svelte";
  import Card from "./lib/Card.svelte";
  
  import SortableList from "./lib/SortableList.svelte";

 
  
  let data;
  let router;
  let route;
  let tab;
  let notfound;
 
  let posts;
  let curCat = 'home';
  let curPost;
  
  onMount(async () => {
    
    const res = await fetch('/data.json');
    data = await res.json();
    console.log(data)
    
    router = new Navigo("/");
    
    router.on("/", async function () {
      console.log("home")
      route = '';
      tab = 'dashboard'
      update()
    });
    
   
    router.on("/category/:slug", async function (props) {
      route = 'category';
      tab = 'posts'
      curCat = props.data.slug;
      posts = data.posts.filter(x=>x.category==props.data.slug)
      
      
      update()
    });
    
    router.on("/post/:slug", async function (props) {    
      route = 'post';
      tab = 'posts'
      curPost = props.data.slug;
      console.log(props.data.slug)
      
      update()
    });
    
    router.on("/settings", async function (props) {    
      route = false;
      tab = 'settings'
      
      
      update()
    });
    
     
    router.on("/account", async function (props) {    
      route = false;
      tab = 'account'
      update()
    });
   
  
    router.notFound(() => {
      // this runs if there is no match found
      notfound = true;
    });
    
    
    setTimeout(()=>{
      router.resolve();
    }, 50)
  });  
  
  async function update(){
    // make sure navigo updates the links on the page after the page has rendered
    await tick();
    router.updatePageLinks()
  }

  
  function newId(){
    let highest_id = Math.max(...data.posts.map(x => x.id));
    return highest_id+1;
  }
  
  async function addPost(){
    
    let cat = data.categories.filter(x=>x.slug==curCat)[0];
    let curType = data.types.filter(x=>x.slug==cat.type)[0];
  
    let newPost = {}
    let id = newId();
    
    newPost.title = "Untitled";
    newPost.id = id;
    newPost.category = cat.slug;
    
    curType.fields.forEach(item=>{
      if(item.type=='gal'){
        newPost[item.title] = [];
      }else{
        newPost[item.title] = "";
      }
    })
    
    newPost.slug = slugify(newPost.title, id);
    
    console.log(newPost)
  
    data.posts.unshift(newPost)
    data = data;
    
    posts = data.posts.filter(x=>x.category==cat.slug);
    
 
  }
  
  function save(){
    saving = true;
    let opts = {};
    opts.path = "data.json";
    opts.type = 'json';
    opts.data = data;
    call_api('api/save', opts).then(function(res) {
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
  
  function test(){
    console.log(data.posts)
  }
  
  
</script>

<nav>
  <header></header>
  
  <ul>
    
    <li><a href="/" class:active={tab === 'dashboard'}  data-navigo>Dashboard</a></li>

    <li><a href="/category/home" class:active={tab === 'posts'}  data-navigo>Posts</a></li>
    
    <li><a href="/settings" class:active={tab === 'settings'}  data-navigo>Settings</a></li>
    
    
    
    <li><a href="/account" class:active={tab === 'account'} data-navigo>Account</a></li>

  </ul>
  

  
</nav>
<main class="h-100">
  
{#if tab=='dashboard'}
<header>
  
  <h5>Dashboard</h5>
  
</header>
<div class="content">
  
  <div class="row">
    <div class="col-md-4">
      <Card title="Posts" bind:number={data.posts.length} percent="33" icon="fa-chart-bar" />
      
    </div>
    <div class="col-md-4">
      
      <Card title="Categories" bind:number={data.categories.length} percent="25" icon="fa-tag" />
    </div>
    <div class="col-md-4">
      <Card title="Posts" bind:number={data.posts.length} percent="33" icon="fa-chart-bar" />
      
    </div>
  </div>
  
  
  
</div>

{/if}
  
 
    
    {#if route=='category'}

    {#if posts}
  
    <div class="row g-0 h-100">
    <div class="col-md-3 h-100 col-categories">
  <header>
 <h5>Posts</h5>
  </header>
    <ul>
      {#each data.categories as item}
      <li><a href="/category/{item.slug}" class:active={curCat === item.slug} data-navigo>{item.title}</a></li>
      {/each}
    </ul>
  </div>
    
    <div class="col-md-9 brdr-start col-posts">
      <header>
        
        <button class="btn btn-outline-secondary" on:click={addPost}><i class="fas fa-plus"></i></button>
        
      </header>
      
    <div class="content">
      
      <SortableList bind:items={posts} bind:data bind:cat={curCat} />
  
     
    </div>
  </div>
    </div>
    {/if}
  
    {:else if route=='post'}
   <header><h5>Edit Post</h5></header>
      
      <div class="row g-0">
      <div class="col-md-6">
    
        <div class="content no-pad col-editor">
        <Editor bind:data bind:curPostId={curPost} />
        </div>
      </div>
      <div class="col-md-6 preview-screen">
     
        <Preview bind:data bind:curPost={curPost} />
      </div>
      </div>

    {/if}
    
    {#if tab=='settings'}
    <header>
      
      <h5>Settings</h5>
    </header>
    <div class="content">
      
      <div class="settings-container">
      {#each Object.keys(data.settings) as key, val}
      <label>{key.replaceAll('_', ' ')}</label>
      <input type="text" class="form-control" bind:value={data.settings[key]} />
      {/each}
      </div>
      
    </div>
    
    {/if}
    
    
    {#if tab=='account'}
    <header>
      
      <h5>Account</h5>
    </header>
    <div class="content">
      
     
     
     <div class="card m-auto" style="width: 18rem;">
      
       <div class="card-body text-center">
         <h5 class="card-title text-capitalize">{cfg.site}</h5>
         <p class="card-text"><a href="{cfg.live_url}" target="_blank">{cfg.live_url}</a></p>
         <a href="/api/logout" class="btn btn-primary">Sign Out</a>
       </div>
     </div>
      
    </div>
    
    {/if}
    


</main>


<style>
 
  nav{
    position: fixed;
    top: 0;
    left: 0;
    height: 100%;
    width: 150px;
    background-color: #333333;
  }
  
  main{
    padding-left: 150px;
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
    padding: 10px;
    padding-left: 25px;
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
  
  .settings-container{
    max-width: 600px;
    margin: 0 auto;
    padding: 5px 20px 25px 20px;
    border: 1px solid #d2d2d2;
    background-color: white;
    border-radius: 8px;
    margin-top: 10px;
  }

</style>