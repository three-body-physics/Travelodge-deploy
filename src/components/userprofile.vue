<template>

<div style="position: relative;">

<div class="ui computer only tablet only grid">
	  <div class="ui fixed secondary menu" id="navbar">
      <div class="ui container">
         <router-link to="/home" style="margin: 0px;" class="header item" id="homeNav">Trave<span style="color: #FDBA90;">Lodge</span>
        </router-link>           
        
        <div class="right menu" style="margin: 0px;">
          <div class="item" v-if="checkAuth()" style="margin: 0px;"><router-link  to="/home/new" class="ui orange button">New Post</router-link></div>
     
          <!-- <div class="item"><span class="ui item" v-show="checkAuth()"> Hello {{ checkUser() }} </span></div> -->
          <div class="item" v-if="checkAuth()" style="margin: 0px;"><a class="ui item" @click="userLogout()"><i class="fa fa-sign-out" aria-hidden="true"></i> Logout</a></div>     
     
        </div>
      </div>
    </div>
</div>
<div class="ui mobile only grid">
  <div class="ui fixed stackable menu" id="navbar">
    <div class="item" style="display: flex; flex-direction:row; justify-content: space-between;">
      <router-link to="/home" id="homeNav" style="color: black;">Trave<span style="color: #FDBA90;">Lodge</span>
      </router-link>
      <i class="fa fa-bars" aria-hidden="true" @click="toggleMobileMenu"></i>
    </div>
      
    <transition name="mobile-menu">
    <div class="right menu" v-show="mobileshow">  
      <router-link to="/home/new" style="margin: 0px;" class="item" v-if="checkAuth()">
          New Post
      </router-link>
      <div style="margin: 0px;" class="item" v-if="!checkAuth()">
          <router-link to="/register" class="ui tiny header">
              <i class="fa fa fa-user-plus" aria-hidden="true"></i> Register
          </router-link>
      </div>

      <div class="item" v-if="checkAuth()" @click="userProfile()" style="margin: 0px;">
          Profile
      </div>
      <div class="item" v-if="checkAuth()" @click="userLogout()" style="margin: 0px;">                                
              <i class="fa fa-sign-out" aria-hidden="true"></i>                               
      </div>
    </div> 
    </transition>
  </div>
</div>



<transition name="form-holder">
  <div id="formholder" v-if="deleteCofirmation" class="ui middle aligned centered doubling grid">

    
    <div class="computer only tablet only sixteen wide column" style="background: white;">
      <h2 class="ui red image header">
          <div class="content" style="padding: 1em;">
            Type Yes to proceed
            <i class="fa fa-times-circle" style="cursor: pointer;" @click="toggleDelete()"></i>
          </div>
        </h2>
      <form class="ui large form">
        <div class="ui stacked segment">
          <div class="field">
            <div class="ui left icon input">
              <i class="lock icon"></i>
              <input type="text" placeholder="type yes to confirm" v-model="yesString">
            </div>
          </div>
          <div v-bind:class="yesClass" @click="deleteRequest()">
            Delete
          </div>   

        </div>
        <div v-show="errorDelete" class="ui error message" id="errorMsg">
          <ul class="list">
            <li>Delete request failed, no permission</li>            
          </ul>
        </div>

      </form>
    </div>
  

</div>
</transition>
<transition name="form-holder">
  <div id="mobileFormholder" v-if="deleteCofirmation" class="ui mobile only middle aligned centered grid">

    <div class="row">
    <div class="sixteen wide column" style="background: white;">
      <h2 class="ui red image header">
          <div class="content">
            Type Yes to proceed
            <i class="fa fa-times-circle" style="cursor: pointer;" @click="toggleDelete()"></i>
          </div>
        </h2>
      <form class="ui large form">
        <div class="ui stacked segment">
          <div class="field">
            <div class="ui left icon input">
              <i class="lock icon"></i>
              <input type="text" placeholder="type yes to confirm" v-model="yesString">
            </div>
          </div>
          <div v-bind:class="yesClass" @click="deleteRequest()">
            Delete
          </div>   

        </div>
        <div v-show="errorDelete" class="ui error message" id="errorMsg">
          <ul class="list">
            <li>Delete request failed, no permission</li>            
          </ul>
        </div>

      </form>
    </div>
    </div>

</div>
</transition>

<div class="ui stackable centered doubling grid" style="background: white; margin-bottom: 4em; margin-top: 6em">
  <div class="ten wide column">
  	<div class="ui container" style="margin-top: 2em;">
  	<div class="ui center aligned text container" style="margin-top: 2em; margin-bottom: 2em;">
  		<div class="row">
  			<i class="fa fa-user-circle fa-5x" aria-hidden="true" style="display: inline-block; letter-spacing: 2px;"></i>
  		</div>
  		<div class="row">
  			<h1>{{ checkUser() }}</h1>  			
  		</div>
    		
  	</div>

  			<div class="ui feed">

  				<h3>Activity</h3>
  				<div class="ui divider"></div>
          <transition-group name="activity-slide" class="ui feed">
  				<div v-for="(entry, i) in entries" :key="i" class="event" style="margin-top: 1em;">

  					<div class="content">
  						<div class="summary">
  							{{ entry.author }} added a post titled: {{ entry.name }}
  							<div class="date"> {{ entry.date }} </div>
  						</div>
  						<div class="extra images">
  							<a @click="goToPost(entry._id)"><img v-bind:src="entry.image"></a>
  						</div>
  						<div class="meta">
  							<i style="display: block;"> {{ entry.comments.length }} comments</i>
  							<div style="margin: 10px 0px 0px 0px;" class="ui red button" @click="toggleDelete(entry._id)">Delete</div>
  						</div>  						
  					</div> 					

  				</div>
</transition-group>
  			</div>
    
  </div>
  </div>
</div>

</div>

</template>

<script>
export default {
  
  data () {
    return {
    	entries: [],
    	error: false,
    	errorMessage: "",
    	errorDelete: false,
    	errorMessageDelete: "",
    	deleteCofirmation: false,
    	userPass: "",
    	postToDelete: "",
    	yesString: "",
      mobileshow: false

    }
  },

  created() {
  	this.getUserPosts();
  },

  methods: {

  	 checkUser() {
      var who = "" + localStorage.getItem("username");
      return who; 
    },

    getUserPosts() {
    	
    	this.$http.post("https://tbprestapi.herokuapp.com/api/home/user/" + localStorage.getItem("userId"), {userId: localStorage.getItem("userId"), username: localStorage.getItem("username")}, {headers: {Authorization: "Bearer " + localStorage.getItem("JWTtoken")}}).then(function(res){
    		if(res.body.success === false) {
    			this.error = true;
    			this.errorMessage = res.body.message;
    		} else {
    			this.entries = res.body.entries.reverse();
    		}
    	}, function(res) {
    		this.error = true;
    		this.errorMessage = "Something went wrong";
    		console.log(res)
    	});
    },

  goToPost(id) {

    	this.$router.push("/home/entry/" + id);

    
  },

  deleteRequest() {

  	this.$http.delete("https://tbprestapi.herokuapp.com/api/home/entry/" + this.postToDelete,
       {headers: {Authorization: "Bearer " + localStorage.getItem("JWTtoken")}}).then(function(res){
  			if(res.body.success === false) {
  				this.errorDelete = true;
  				this.errorMessageDelete = res.body.message;
  			} else {
  				this.getUserPosts();
          this.deleteCofirmation = false;
  			}

  	}, function(res) {

  		this.errorDelete = true;
  		this.errorMessageDelete = "Request cannot be made."

  	});
  },

  toggleDelete(postID) {

  	if(postID) {

  	this.postToDelete = postID;
  	this.deleteCofirmation = !this.deleteCofirmation;

  } else {

  	this.postToDelete = "";
  	this.deleteCofirmation = !this.deleteCofirmation;

  	}
  },

  toggleMobileMenu() {
    this.mobileshow = !this.mobileshow;
  },

  yesConfirmation() {

  		if (this.yesString.toLowerCase() === "yes") {
  			return true;
  		} else {
  			return false;
  		}
  	},

  checkAuth() {

      var tk = "token: " + localStorage.getItem("JWTtoken");      

      if (tk.length < 20) {
        return false;
      } else {
        return true;
      }

    },

  userLogout() {
      localStorage.removeItem("JWTtoken");
      localStorage.removeItem("userId");
      localStorage.removeItem("username");     
      
      setTimeout(() => {this.$router.push("/logout");}, 1000);   

       
    }

},

  computed: {
  	yesClass: function() {
  	
  		return {
  			"ui fluid large red submit button": this.yesConfirmation(),
  			"ui fluid large red submit disabled button": !this.yesConfirmation()
  		}

  	}
  }



}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>



#formholder {
  position: fixed;  
  top: 30%;
  left: 40.5%;  
  z-index: 1;

}

#mobileFormholder {
  position: fixed;
  top: 35%;
  left: 20%;
  z-index: 1;
}

@import url('https://fonts.googleapis.com/css?family=Catamaran|Montserrat:300i');


.menu {
  background: white;
}

#navbar {
 
  background: white;

}

#navbar a {
  font-family: 'Catamaran', sans-serif;
  font-size: 1em;
  font-weight: bold;
  
}

.right.menu {
  margin-right: 1em;
}

.activity-slide-enter-active, .activity-slide-leave-active {
  -o-transition: all 1s;
  transition: all 1s;
  -webkit-transition: all 1s;
}

.activity-slide-enter, .activity-slide-leave-to {
  opacity: 0;
  -ms-transform: translateX(50px);
      transform: translateX(50px);
  -webkit-transform: translateX(50px);
}

.form-holder-enter-active, .form-holder-leave-active {
  -o-transition: all 0.5s;
  transition: all 0.5s;
  -webkit-transition: all 0.5s;
}

.form-holder-enter, .form-holder-leave-to {
  opacity: 0;
  -webkit-transform: translateY(-250px);
      -ms-transform: translateY(-250px);
          transform: translateY(-250px);
}


.mobile-menu-enter-active, .mobile-menu-leave-active {
    -webkit-transition: all 500ms ease-in;
    -o-transition: all 500ms ease-in;
    transition: all 500ms ease-in;
}

.mobile-menu-enter, .mobile-menu-leave-to {
    opacity: 0;
    height: 0;
} 

.mobile-menu-enter-to, .mobile-menu-leave {
    opacity: 1;
    height: 100%;
}    
</style>
