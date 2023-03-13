<script>
  import { onMount, onDestroy } from 'svelte'
  import { Editor } from '@tiptap/core'
  import StarterKit from '@tiptap/starter-kit'
  import Image from '@tiptap/extension-image'
  import Link from '@tiptap/extension-link'
  
  // export let key;
  export let html;
  
  if(typeof html === 'undefined'){
    html = '';
  }

  let element
  let editor

  onMount(() => {
    editor = new Editor({
      element: element,
      extensions: [
        StarterKit,
        Image.configure({
          inline: true,
        }),
        Link.configure({
          openOnClick: false,
        }),
      ],
      content: html,
      onTransaction: () => {
        // force re-render so `editor.isActive` works as expected
        editor = editor
        
      },
      onUpdate({ editor }) {
        html = editor.getHTML()
        // console.log(html)
      },
    })
  })

  onDestroy(() => {
    if (editor) {
      editor.destroy()
    }
  })
  
  
  function setLink() {
    const previousUrl = editor.getAttributes('link').href
    const url = window.prompt('URL', previousUrl)
  
    // cancelled
    if (url === null) {
      return
    }
  
    // empty
    if (url === '') {
      editor
        .chain()
        .focus()
        .extendMarkRange('link')
        .unsetLink()
        .run()
  
      return
    }
  
    // update link
    editor
      .chain()
      .focus()
      .extendMarkRange('link')
      .setLink({
        href: url
      })
      .run()
  }
  
  function addImage() {
    const url = window.prompt('URL')
  
    if (url) {
      this.editor.chain().focus().setImage({
        src: url
      }).run()
    }
  }
</script>

{#if editor}
 <div class="btn-group mt-1 w-100">
 

       <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleBold().run()}" class:is-active="{editor.isActive('bold')}"><i class="fas fa-bold"></i></button>
       
       <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleItalic().run()}" class:is-active="{editor.isActive('italic')}"><i class="fas fa-italic"></i></button>
       
       <button class="btn btn-outline-secondary" on:click="{setLink}" class:is-active="{editor.isActive('link')}"><i class="fas fa-link"></i></button>
       
       <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleBulletList().run()}" class:is-active="{editor.isActive('bulletList')}"><i class="fas fa-list-ul"></i></button>
       
       
       
        <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleHeading({ level: 1 }).run()}" class:is-active="{editor.isActive('heading', { level: 1 }) }"><strong>H1</strong></button>
        
        <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleHeading({ level: 2 }).run()}" class:is-active="{editor.isActive('heading', { level: 2 }) }"><strong>H2</strong></button>
        
        
        <button class="btn btn-outline-secondary" on:click="{editor.chain().focus().toggleHeading({ level: 3 }).run()}" class:is-active="{editor.isActive('heading', { level: 3 }) }"><strong>H3</strong></button>

 
 <!--


 
 
 <template v-if="settings.rte_buttons.includes('image')">
 <button @click="addImage" class="btn btn-outline-secondary" :class="{ 'is-active': editor.isActive('image') }">
   <i class=" fas fa-image"></i>
 </button>
 </template>
 

 -->
     <button class="btn btn btn-outline-secondary w-25" disabled></button>
  
   </div>
   
   
{/if}

<div class="editor" spellcheck="false" bind:this={element} />

<style>
  button.active {
    background: black;
    color: white;
  }

  .is-active {
    background-color: #EEE;
  }
  
  blockquote {
    border-left: 5px solid #8391F1;
    padding-left: 10px;
  }
  
  img {
    max-width: 100%;
  }
  
button{
  border: 1px solid #ddd;
  margin-bottom: 0 !important;
}

button:hover{
  background-color: #EEE;
  color: black;
}
  
  .btn-group .btn:first-child{
    border-bottom-left-radius: 0;
  }
  .btn-group .btn:last-child{
    border-bottom-right-radius: 0;
  }
  .btn-outline-secondary{
    padding-top: 4px !important;
  }
 
  
</style>

