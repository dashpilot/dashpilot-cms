<script>
import { onMount } from 'svelte';
// import {call_api} from "./../lib/Helpers.svelte"

export let key;
export let item;
export let settings;

let uploading = false;



onMount(async () => {

  document.getElementById('fileInput').addEventListener('change', function(e) {

    uploading = true;

    if(typeof settings.image_width !== 'undefined'){
      var width = settings.image_width;
    }else{
      var width = 800;
    }
    
    console.log(width)
    var img = new Image();
    img.onload = function() {
      var canvas = document.createElement('canvas'),
        ctx = canvas.getContext("2d"),
        oc = document.createElement('canvas'),
        octx = oc.getContext('2d');

      canvas.width = width; // destination canvas size
      canvas.height = canvas.width * img.height / img.width;

      var cur = {
        width: Math.floor(img.width * 0.5),
        height: Math.floor(img.height * 0.5)
      }

      oc.width = cur.width;
      oc.height = cur.height;

      octx.drawImage(img, 0, 0, cur.width, cur.height);

      while (cur.width * 0.5 > width) {
        cur = {
          width: Math.floor(cur.width * 0.5),
          height: Math.floor(cur.height * 0.5)
        };
        octx.drawImage(oc, 0, 0, cur.width * 2, cur.height * 2, 0, 0, cur.width, cur.height);
      }

      ctx.drawImage(oc, 0, 0, cur.width, cur.height, 0, 0, canvas.width, canvas.height);
      var base64Image = canvas.toDataURL('image/jpeg')

      console.log(base64Image);
      let opts = {};
      opts.path = 'img/'+item.id+'-'+Date.now()+".jpg";
      opts.type = 'img';
      opts.data = base64Image;
      call_api('/api/save', opts).then(function(res) {
        if (res.ok) {
          console.log('Saved');
          let newItem = {'filename': res.filename};
          item[key].push(newItem);
          item = item;
          uploading = false;
        } else {
          console.log('Error saving');
          setTimeout(function(){
              uploading = false;
          }, 1000)

        }
      });

      // cleaning up
      URL.revokeObjectURL(img.src)

    }
    img.src = URL.createObjectURL(e.target.files[0]);


  })

});

function clickSelect(mykey){
  document.querySelector('#fileInput').click();
}

function deleteImage(key, i){
  console.log(key);

  var r = confirm("Are you sure you want to delete this image?");
  if (r == true) {
  uploading = true;

    let opts = {};
    opts.filename = item[key][i].filename;
    call_api('/api/delete', opts).then(function(res) {
      if (res.ok) {
        console.log('Deleted');
        item[key].splice(i, 1);
        item = item;
        uploading = false;
      } else {
        console.log('Error deleting');
        setTimeout(function(){
            uploading = false;
        }, 1000)

      }
    });
  }
}

function move(array, from, to) {
  if (to === from) return array

  var target = array[from]
  var increment = to < from ? -1 : 1

  for (var k = from; k != to; k += increment) {
    array[k] = array[k + increment]
  }
  array[to] = target
  return array
}

function moveDown(key, index) {

    var newindex = index + 1

  if (typeof item[key][newindex] !== 'undefined') {
    item[key] = move(item[key], index, index + 1)
    item = item;
  }
}

function imageExists(image_url){

    var http = new XMLHttpRequest();

    http.open('HEAD', image_url, false);
    http.send();

    return http.status != 404;

}
</script>

<input type="file" id="fileInput" class="fileInput" accept="image/*" data-name="{key}" />
<button class="btn btn-outline-secondary" on:click="{() => clickSelect(key)}">


{#if uploading}
<span class="fa-solid fa-spinner" role="status" aria-hidden="true"></span>
{:else}
<i class="fa-solid fa-images"></i>
{/if}

&nbsp;Choose Image</button>

{#if item[key].length > 0}

<ul class="list-group mt-3">

{#each item[key] as img, i}

<li class="list-group-item">

<div class="row">
  <div class="col-3">



    <div class="box" style="background-image: url({cfg.base}{img.filename});"></div>


  </div>
  <div class="col-3">
    
    {#if settings.has_img_title}<input type="text" class="form-control mb-0" bind:value="{item[key][i].title}" placeholder="{settings.has_img_title}" />{/if}
    
  </div>
 <div class="col-3">
    {#if settings.has_img_link}<input type="text" class="form-control mb-0" bind:value="{item[key][i].link}" placeholder="{settings.has_img_link}" />{/if}

</div>
  <div class="col-3">

<div class="btn-group float-end">

<button class="btn btn-outline-secondary btn-icon" on:click="{() => moveDown(key, i)}">
 <i class="fa-solid fa-caret-down"></i>
</button>


  <button class="btn btn-outline-secondary btn-icon" on:click="{() => deleteImage(key, i)}">
    <i class="fa-solid fa-trash"></i>
  </button>
</div>

  </div>
</div>

</li>

{/each}
</ul>
{/if}



<style>
.fileInput{
  display: none;
}


.btn-icon{
  margin-bottom: 0 !important;
}

.row .btn{
  margin-bottom: 0;
}


.box{
  display: inline-block;
  width: 37px;
  height: 37px;
  border: 1px solid #6c757d;
  background-size: cover;
  border-radius: 4px;
}

.list-group-item{
  padding-bottom: 5px;
  padding-left: 15px;
  padding-right: 15px;
}

.form-control{
  margin-bottom: 0;
}
</style>
