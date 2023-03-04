<script>
import { onMount } from 'svelte';

export let showPreview;
export let data;
export let postId;


let post = data.posts.filter(x=>x.id==postId)[0]

let preview;
let remote_preview = cfg.live_url+"/preview";

function previewPost(){
	
	setTimeout(()=>{
		var domain = cfg.live_url+"/preview";
		var iframe = document.getElementById('preview-frame').contentWindow;
		  
		  //message sender
		  var message = JSON.stringify(post);
		 
			iframe.postMessage(message,domain)
			console.log('message sent')
	}, 50)

}

</script>


<div class="backdrop">
<div class="modal modal-lg" tabindex="-1" style="display: block;">
  <div class="modal-dialog">
	<div class="modal-content">
	  <div class="modal-header">
		<h5 class="modal-title">Preview</h5>
		<button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close" on:click={()=>showPreview=false}></button>
	  </div>
	  <div class="modal-body p-0">
	

		<iframe src="{remote_preview}" width="798" height="600" id="preview-frame" class="fade-in" on:load={previewPost}></iframe>


	  </div>
	 
	</div>
  </div>
</div>
</div>

<style>

	
	.modal-body{
		min-height: 600px;
	
	}
	
	
	.fade-in { animation: fadeIn 2s; }
	
	@keyframes fadeIn {
	  0% { opacity: 0; }
	  100% { opacity: 1; }
	}
</style>