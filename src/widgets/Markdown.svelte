<svelte:head>
<link rel="stylesheet" href="https://unpkg.com/easymde/dist/easymde.min.css">
</svelte:head>
<script>
import EasyMDE from "easymde";
import { onMount, onDestroy } from 'svelte';
let key = Math.floor(Math.random() * 99999999999999);
export let html;
let easyMDE = false;

onMount(() => {
  easyMDE = new EasyMDE({element: document.getElementById('mde-'+key), spellChecker: false});
  easyMDE.codemirror.on("change", function(){
    // console.log(easyMDE.value());
    html = easyMDE.value();
  });
});

onDestroy(() => {
    if(easyMDE && easyMDE !== null && typeof easyMDE !== 'undefined'){
      easyMDE.toTextArea();
      easyMDE = null;
    }
});

function defined(val){
  if(typeof val === 'undefined'){
    val = '';
  }
  return val;
}
</script>

<textarea id="mde-{key}">{defined(html)}</textarea>

