<script>
export let title;
export let number;
export let msg;
export let icon;	
export let icon2;
</script>  
  
  
   <div class="card card-stats mb-4 mb-xl-0">
	<div class="card-body">
	  <div class="row">
		<div class="col">
		  <h5 class="card-title text-uppercase text-muted mb-0">{title}</h5>
		  <span class="h2 font-weight-bold mb-0">{number}</span>
		</div>
		<div class="col-auto">
		  <div class="icon icon-shape bg-danger text-white rounded-circle">
			<i class="fas {icon}"></i>
		  </div>
		</div>
	  </div>
	  <p class="mt-3 mb-0 text-muted text-sm">
		<span class="text-success mr-2"><i class="fas {icon2}"></i> {@html msg}</span>
		
	  </p>
	</div>
  </div>
  
  
  <style>
	  .icon{
		  width: 3rem;
		  height: 3rem;
		  border-radius: 50%;

		  padding-top: 12px;
		  text-align: center;
	  }
  </style>