 <div class="card m-auto" style="width: 18rem;">
   
   <div class="profile-pic">
	 <i class="fa-solid fa-user-astronaut"></i>
   </div>
  
   <div class="card-body text-center">
	 <h5 class="card-title text-capitalize">{cfg.site}</h5>
	 <p class="card-text"><a href="{cfg.live_url}" target="_blank">{cfg.live_url}</a></p>
	 <a href="/api/logout" class="btn btn-dark">Sign Out</a>
   </div>
 </div>


<style>
	
	.profile-pic{
		text-align: center;
		overflow: hidden;
		width: 100px;
		height: 100px;
		border: 1px solid #d2d2d2;
		border-radius: 50%;
		margin: 0 auto;
		margin-top: -50px;
		background-color: white;
	  }
	  
	  .profile-pic i{
		font-size: 80px;
		margin-top: 20px;
	  }
	  
</style>