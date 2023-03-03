<script>
export let showPreview;
export let data;
export let postId;

let post = data.posts.filter(x=>x.id=postId)[0]
let slug = post.slug;

function preview(){
  var domain = cfg.live_url;
  var iframe = document.getElementById('preview-frame').contentWindow;
  
  //message sender
  var message = JSON.stringify(data);
  setTimeout(()=>{
	iframe.postMessage(message,domain)
	console.log('message sent')
  }, 10)

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
		<iframe src="{cfg.live_url}/article/{post.id}" width="798" height="600" id="preview-frame" on:load={preview}></iframe>
	  </div>
	 
	</div>
  </div>
</div>
</div>


