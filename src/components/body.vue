<template>

<div id="holder">

    <div class="ui inverted vertical masthead center aligned segment">

        <div class="ui container">
            <div class="ui computer only tablet only grid">
                <div class="ui fixed secondary menu" id="navbar">
                    <div class="ui container">

                        <router-link style="margin: 0px;" to="/home" class="ui header item" id="homeNav">Trave<span style="color: #FDBA90;">Lodge</span>
                        </router-link>

                        <div style="margin: 0px;" class="right menu">
                            <div v-if="checkAuth()" style="margin: 0px;" class="item">
                                <router-link class="ui orange button" to="/home/new">New Post</router-link>
                            </div>
                            <div v-if="!checkAuth()" style="margin: 0px;" class="item">
                                <router-link to="/register" class="ui blue button">Register</router-link>
                            </div>
                            <div v-show="!checkAuth()" style="margin: 0px;" class="item">
                                <a class="ui orange button" @click="toggleLogin()">
                                    <i class="fa fa-sign-in" aria-hidden="true"></i> Login
                                </a>
                            </div>

                            <div v-if="checkAuth()" style="margin: 0px;" class="item"><a class="ui item" @click="userProfile()"><i class="fa fa-user-circle fa-2x" aria-hidden="true" style="display: inline-block; letter-spacing: 2px;"></i> {{ checkUser() }} </a></div>
                            <div v-if="checkAuth()" style="margin: 0px;" class="item"><a class="ui item" @click="userLogout()"><i class="fa fa-sign-out" aria-hidden="true"></i> Logout
                              </a>
                            </div>

                        </div>
                    </div>
                </div>
            </div>

            <div class="ui mobile only grid">
                <div class="ui fixed stackable menu" id="navbar">
                    <div class="item" style="display: flex; flex-direction:row; justify-content: space-between;">
                        <router-link to="/home" id="homeNav" style="color: black;">Trave<span style="color: #FDBA90;">Lodge</span>
                        </router-link>
                        <i class="fa fa-bars" aria-hidden="true" @click="mobileshowToggle"></i>
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
                            <div style="margin: 0px;" class="item" v-show="!checkAuth()">
                                <a class="ui tiny header" @click="toggleLogin()">
                                    <i class="fa fa-sign-in" aria-hidden="true"></i> Login
                                </a>
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

        </div>

        <div class="ui text container" style="transition: all 2s">
            <h1 class="ui inverted header" id="bannerh1">
              Share your adventure with the world!
            </h1>
            <h2>Register now to start posting.</h2 id="bannerh2">

  </div>

</div>

<transition name="form-holder">

  <div v-if="loginForm" id="formholder" class="ui middle aligned centered grid">

    <div class="six wide computer only center aligned column" id="computerForm">
      <h2 class="ui teal image header">
          <div class="content">
            Log in to your account
            <i class="fa fa-times-circle" @click="toggleLogin()"></i>
          </div>
        </h2>
            <form class="ui large form">
                <div class="ui stacked segment">
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="user icon"></i>
                            <input type="text" placeholder="username" v-model="loginName">
                        </div>
                    </div>
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="lock icon"></i>
                            <input type="password" placeholder="password" v-model="loginPass">
                        </div>
                    </div>
                    <div class="ui fluid large teal submit button" @click="userLogin">
                        login
                    </div>
                    <div class="ui message">
                        "New to us?"
                        <router-link to="/register">Register here</router-link>
                    </div>
                </div>
                <div v-show="errorForm" class="ui error message" id="errorMsg">
                    <ul class="list">
                        <li>Username or password does not match.</li>
                        <li>Inputs are case sensitive.</li>
                    </ul>
                </div>
            </form>
        </div>

        <div class="mobile only sixteen wide center aligned column" id="mobileform">
            <h2 class="ui teal image header">
          <div class="content">
            Log in to your account
            <i class="fa fa-times-circle" @click="toggleLogin()"></i>
          </div>
        </h2>
            <form class="ui large form">
                <div class="ui stacked segment">
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="user icon"></i>
                            <input type="text" placeholder="username" v-model="loginName">
                        </div>
                    </div>
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="lock icon"></i>
                            <input type="password" placeholder="password" v-model="loginPass">
                        </div>
                    </div>
                    <div class="ui fluid large teal submit button" @click="userLogin">
                        login
                    </div>
                    <div class="ui message">
                        "New to us?"
                        <router-link to="/register">Register here</router-link>
                    </div>
                </div>
                <div v-show="errorForm" class="ui error message" id="errorMsg">
                    <ul class="list">
                        <li>Username or password does not match.</li>
                        <li>Inputs are case sensitive.</li>
                    </ul>
                </div>
            </form>
        </div>

        <div class="tablet only ten wide center aligned column">
            <h2 class="ui teal image header">
              <div class="content">
                Log in to your account
                <i class="fa fa-times-circle" @click="toggleLogin()"></i>
              </div>
            </h2>
            <form class="ui large form">
                <div class="ui stacked segment">
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="user icon"></i>
                            <input type="text" placeholder="username" v-model="loginName">
                        </div>
                    </div>
                    <div class="field">
                        <div class="ui left icon input">
                            <i class="lock icon"></i>
                            <input type="password" placeholder="password" v-model="loginPass">
                        </div>
                    </div>
                    <div class="ui fluid large teal submit button" @click="userLogin">
                        login
                    </div>
                    <div class="ui message">
                        "New to us?"
                        <router-link to="/register">Register here</router-link>
                    </div>

                </div>
                <div v-show="errorForm" class="ui error message" id="errorMsg">
                    <ul class="list">
                        <li>Username or password does not match.</li>
                        <li>Inputs are case sensitive.</li>
                    </ul>
                </div>

            </form>
        </div>

    </div>
    </transition>

    <div class="ui centered middle aligned stackable grid container" id="body">

        <div class="ui two column row" id="headerRow">
            <div class="column">
                <h4 class="ui header">
                  Latest - {{ entries.length }} entries :
                </h4>
            </div>
            <div class="tablet computer only column">

            </div>

        </div>
        <div class="ui divider" id="headerdivider">

        </div>

        <div class="row" id="pagination">
            <div class="ui pagination menu">
                <a v-for="num in pages" class="item" @click="pageToggle(num)">
                  {{ num + 1 }}
                </a>
            </div>
        </div>
        <div class="row" style="min-height: 550px;">
            <!--   <div class="ui stackable centered cards" id="ui-overrides"> -->

            <transition-group name="card-ani" class="ui stackable centered cards" tag="div">

                <div v-for="(entry, index) in activeEntries" class="doubling four wide column card" :key="index" style="-webkit-transition: all 300ms ease-in-out 50ms; transition: all 300ms ease-in-out 50ms;">
                    <div @click="goToBLog(entry._id)" class="image">
                        <img v-bind:src="entry.image">
                    </div>
                    <div class="right aligned content">
                        <a @click="goToBLog(entry._id)" class="center aligned header">
                          {{ entry.name }}
                        </a>
                        <div class="ui divider"></div>
                        <div class="center aligned meta">
                            <span class="date">Posted: {{ entry.date }} , <strong>{{ entry.author }}</strong> </span>
                        </div>
                        <div class="ui divider">
                        </div>
                        <div class="left aligned description">
                            {{ entry.text | limitText(150) }}
                            <div class="ui divider">
                            </div>
                        </div>

                        <i class="comment icon"></i><span id="comments" @click="goToBLog(entry._id)"> {{ entry.comments.length | commentText("comment") }}  </span>
                    </div>
                </div>
            </transition-group>
        </div>
    </div>
</div>

</template>

<script>

export default {

    data() {
            return {
                entries: [],
                entryHolder: [],
                activeEntries: [],
                errorForm: false,
                loginForm: false,
                activePage: 0,
                pages: [],
                loginName: "",
                loginPass: "",
                loggedUserName: "",
                mobileshow: false

            }
        },

        created() {
            this.getBlogs();

        },

        methods: {

            getBlogs() {
                    this.$http.get("https://tbprestapi.herokuapp.com/api/home", {

                        flag: "hello"

                    }).then(function(data) {

                        this.entries = data.body.reverse();

                        this.divideEntries();

                    }, function(error) {

                        console.log(error);

                    });

                },

                checkAuth() {

                    var tk = "token: " + localStorage.getItem("JWTtoken");

                    if (tk.length < 20) {
                        return false;
                    } else {
                        return true;
                    }

                },

                checkUser() {
                    var who = "" + localStorage.getItem("username");
                    return who;
                },

                userLogin(username, password) {

                    if (username, password) {

                        this.$http.post("https://tbprestapi.herokuapp.com/api/login", {
                            username: username,
                            password: password
                        }).then(function(res) {

                            console.log(res);

                            localStorage.setItem("JWTtoken", res.body.token);
                            localStorage.setItem("userId", res.body.user._id);
                            localStorage.setItem("username", res.body.user.username);

                            this.loggedUser = "token: " + res.body.token;

                            if (this.loggedUser.length > 20) {

                                this.loginForm = false;

                            }

                            this.$router.push("/")

                        }, function(err) {
                            console.log(err);
                        });


                    } else {

                        this.$http.post("https://tbprestapi.herokuapp.com/api/login", {
                            username: this.loginName,
                            password: this.loginPass
                        }).then(function(res) {

                            console.log(res);
                            console.log("second call");

                            if (res.body.success === true) {

                                localStorage.setItem("JWTtoken", res.body.token);
                                localStorage.setItem("userId", res.body.user._id);
                                localStorage.setItem("username", res.body.user.username);
                                this.loginForm = false;

                            } else {
                                this.errorForm = true;
                            }

                        }, function(res) {
                            console.log(res);
                        });
                    }
                },

                userLogout() {
                    localStorage.removeItem("JWTtoken");
                    localStorage.removeItem("userId");
                    localStorage.removeItem("username");

                    
                    setTimeout(() => {
                        this.$router.push("/logout");
                    }, 1000);

                },

                mobileshowToggle() {
                    this.mobileshow = !this.mobileshow;
                },

                userProfile(username) {

                    if (username) {

                        this.$router.push("/home/user/" + username);

                    } else {

                        this.$router.push("/home/user/" + "" + localStorage.getItem("username"));

                    }

                },

                pageToggle(num) {
                    this.activePage = num;
                    this.pagination();
                },

                goToBLog(id) {
                    this.$router.push("/home/entry/" + id);
                },

                toggleLogin() {
                    this.loginForm = !this.loginForm;
                    this.mobileshow = false;
                },

                divideEntries() { //six per page

                    for (let i = 0; i <= this.entries.length / 6; i++) {
                        this.pages.push(i);
                    }

                    for (let i = 0; i <= this.entries.length / 6; i++) {
                        this.entryHolder[i] = this.entries.slice((6 * i), ((6 * i) + 6));
                    }

                    this.pagination();
                },

                pagination() {
                                       
                    this.activeEntries = [];

                    setTimeout(() => {
                        this.activeEntries = this.entryHolder[this.activePage]
                    }, 500)



                    // var e = this.entryHolder[this.activePage];
                    // var t = 0;
                    // e.forEach((o) =>{ 
                    //     setTimeout(() => {
                    //     this.activeEntries.push(o)
                    // }, 100 + t)
                    //     t += 100;
                    // });

                  
                } //

        },

        filters: {
            limitText: function(text, limit) {
                if (text.length < 100) {
                    return text;
                } else {
                    return text.substring(0, limit) + "...";
                }
            },

            commentText: function(text, string) {
                if (text >= 2) {
                    return text + " comments";
                } else {
                    return text + " comment";
                }
            }
        }
}

</script>

<style scoped>

@import url('https://fonts.googleapis.com/css?family=Catamaran|Montserrat:300i');
@import url('https://fonts.googleapis.com/css?family=Alfa+Slab+One|Fjalla+One');

#formholder {
    position: relative;
    z-index: 1;
    margin-bottom: 1.5em;
}

#formholder .column {
    position: fixed;
    background: rgba(255, 255, 255, 1);
}

.grid.container {
    margin-top: 2em;
}

#mobileform {
    top: 20%;
}

#errorMsg {
    display: block;
}

#computerForm {
    top: 25%;
}

img {
    margin-bottom: 1em;
}

#headerdivider {
    margin: 0.5rem;
}

#headerRow {
    padding: 0px;
}

.segment {
    border: 0px !important;
}

#ui-overrides.card {
    margin-top: 1em;
    -webkit-box-shadow: 0px !important;
            box-shadow: 0px !important;
    border: none;
}

#comments {
    opacity: 0.6;
    cursor: pointer;
}

#comments:hover {
    opacity: 1;
}

.image:hover {
    cursor: pointer;
}

#body {
    margin-bottom: 3em;
}

#homeNav {
    font-weight: bold;
}

.masthead h1.ui.header {
    font-size: 2em;
    margin-top: 9em;
}

.masthead h2 {
    margin-bottom: 10em;
    font-size: 1.2em;
}

.menu {
    background: white;
}

#navbar {
    font-family: 'Catamaran', sans-serif;
    font-size: 1em;
    background: white;
}

.masthead {
    /*  background: url("../assets/hero3.jpg") no-repeat center center fixed !important;*/
    background: url("https://i.redd.it/lghllrc86vdy.jpg") no-repeat center center fixed !important;
}

.right.menu {
    margin-right: 1em;
}

.entry-box-enter-active,
.entry-box-leave-active {
    -o-transition: all 1s;
    transition: all 1s;
    -webkit-transition: all 1s;
}

.entry-box-enter,
.entry-box-leave-to {
    opacity: 0;
}

.form-holder-enter-active,
.form-holder-leave-active {
    -o-transition: all 0.5s;
    transition: all 0.5s;
    -webkit-transition: all 0.5s;
}

.form-holder-enter,
.form-holder-leave-to {
    opacity: 0;
}

.card-ani-enter-active {
    -webkit-transition: all 600ms ease-in-out 50ms;
    -o-transition: all 600ms ease-in-out 50ms;
    transition: all 600ms ease-in-out 50ms;
}

.card-ani-leave-active {
    -webkit-transition: all 200ms ease-in;
    -o-transition: all 200ms ease-in;
    transition: all 200ms ease-in;
    position: absolute;
    z-index: 0;
}

.card-ani-enter,
.card-ani-leave-to {
    opacity: 0;
    -ms-transform: translateY(50px);
        transform: translateY(50px);
    -webkit-transform: translateY(50px);
}

.card-ani-move {
    -webkit-transition: all 600ms ease-in-out 50ms;
    -o-transition: all 600ms ease-in-out 50ms;
    transition: all 600ms ease-in-out 50ms;
}

@media only screen 
  and (min-device-width: 320px) 
  and (max-device-width: 768px)
   {

    #pagination {
      margin-bottom: 2em;
    }
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
