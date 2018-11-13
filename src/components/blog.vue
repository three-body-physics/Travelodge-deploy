<template>

<div>
    <div class="ui computer only tablet only grid">
    <div class="ui fixed secondary menu" id="navbar">
        <div class="ui container">
            <router-link to="/home" style="margin: 0px;" class="header item" id="homeNav">Trave<span style="color: #FDBA90;">Lodge</span>
            </router-link>
            <div class="right menu" style="margin: 0px;">
                <div class="item" v-if="checkAuth()" style="margin: 0px;">
                    <router-link to="/home/new" class="ui orange button">New Post</router-link>
                </div>
                <div class="item" v-if="checkAuth()" style="margin: 0px;"><a class="ui item" @click="userProfile()"><i class="fa fa-user-circle fa-2x" aria-hidden="true" style="display: inline-block; letter-spacing: 2px;"></i></a></div>
                <div class="item" v-if="!checkAuth()" style="margin: 0px;">
                    <router-link to="/register" class="ui blue button">Register</router-link>
                </div>
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

    <div class="ui doubling stackable centered grid" id="content">
   
        <div class="column">
            <div class="ui centered segment" id="entryBox">
                <transition name="infobox" appear>
                    <img class="ui centered fluid image" :src="entry.image">
                </transition>
                <div class="ui divider">
                </div>
                <transition name="infobox" appear>
                    <div class="ui items">
                        <div class="item">
                            <div class="content">
                                <div class="header">
                                    {{ entry.name }}
                                </div>
                                <div class="meta">
                                    Posted: {{ entry.date }} , {{ entry.author }}
                                </div>
                                <div class="ui divider">
                                </div>
                                <div class="description" id="description">
                                    {{ entry.text }}
                                </div>
                                <div class="extra">
                                    <p>Share this post:</p>
                                    <social-sharing style="display: inline-block;" v-bind:url="'https://salty-chamber-39698.herokuapp.com' + this.$route.path" inline-template>
                                        <div>
                                            <network network="facebook">
                                                <i class="fa fa-fw fa-facebook-square fa-2x" id="fbicon" style="cursor: pointer; margin-left: 2px;"></i>
                                            </network>
                                            <network network="pinterest">
                                                <i class="fa fa-fw fa-pinterest fa-2x" style="cursor: pointer;"></i>
                                            </network>
                                            <network network="reddit">
                                                <i class="fa fa-fw fa-reddit fa-2x" style="cursor: pointer;"></i>
                                            </network>
                                        </div>
                                    </social-sharing>
                                </div>
                            </div>
                        </div>
                    </div>
                </transition>
            </div>
            <transition name="drop-in">
                <form v-show="replyBox" class="ui reply form">
                    <div class="field">
                        <div class="ui floating message">
                            <p>Posting as: {{ checkUser() }}</p>
                        </div>
                        <textarea placeholder="type your comment here" id="commentbox" value="comment.comment" v-model="comment.comment"></textarea>
                    </div>
                    <div :class="commentClass" @click="postComment()">
                        <i class="icon edit"></i> Add Reply
                    </div>
                </form>
            </transition>
            <div class="ui segment" id="commentSession">
                <div :class='buttonClass' @click="toggleReply()">
                    Add Comment
                </div>
                <div class="ui comments">
                    <h3 class="ui dividing header"><span v-if="entry.comments">{{ entry.comments.length | commentText("comment") }}</span><span v-if="!entry.comments">0 comment</span> </h3>
                    <transition-group name="comment-seg">
                        <div v-for="(comment, index) in entry.comments" :key="index" class="comment">
                            <a class="avatar">
                                <i class="big user circle icon"></i>
                            </a>
                            <div class="content">
                                <i class="author">{{ comment.author.username }}</i>
                                <div class="metadata">
                                    <span class="date"> {{ comment.date }} </span>
                                </div>
                                <div class="text">
                                    {{ comment.comment }}
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
    data() {
            return {
                id: this.$route.params.id,
                entry: {},
                replyBox: false,
                comment: {
                    comment: "",

                },
                loaded: false,
                info: false,
                mobileshow: false
            }
        },

        computed: {

            buttonClass: function() {
                return {
                    "ui right floated blue button": this.checkAuth(),
                    "ui right floated blue disabled button": !this.checkAuth()
                }
            },

            commentClass: function() {

                return {
                    "ui blue labeled submit icon button": this.validateForm(),
                    "ui blue labeled submit icon disabled button": !this.validateForm()
                }

            }

        },

        created() {
            this.postReq();
            this.checkAuth();

        },


        filters: {

            commentText: function(text, string) {
                if (text >= 2) {
                    return text + " comments";
                } else {
                    return text + " comment";
                }
            }
        },

        methods: {
            postComment() {

                    this.$http.post("https://tbprestapi.herokuapp.com/api/home/entry/" + this.id, {
                        _id: this.id,
                        content: this.comment,
                        userId: localStorage.getItem("userId"),
                        username: localStorage.getItem("username"),
                        date: this.getTimeStamp()
                    }, {
                        headers: {
                            Authorization: "Bearer " + localStorage.getItem("JWTtoken")
                        }
                    }).then(function(res) {

                        console.log(res.body.post);

                        this.entry = res.body.post;

                        this.comment.comment = "";



                    }, function(res) {
                        console.log(res);
                    });


                },

                animateInfo() {

                },

                userProfile() {
                    this.$router.push("/home/user/" + localStorage.getItem("username"));
                },



                checkAuth() {

                    var tk = "token: " + localStorage.getItem("JWTtoken");

                    if (tk.length < 20) {

                        return false;
                    } else {

                        return true;
                    }

                },

                toggleReply() {
                    this.replyBox = !this.replyBox;
                    this.info = !this.info;

                },

                userLogout() {
                    localStorage.removeItem("JWTtoken");
                    localStorage.removeItem("userId");
                    localStorage.removeItem("username");

                    
                    setTimeout(() => {
                        this.$router.push("/logout");
                    }, 1000);


                },

                checkUser() {
                    var who = "" + localStorage.getItem("username");
                    return who;
                },

                checkAuth() {

                    var tk = "token: " + localStorage.getItem("JWTtoken");

                    if (tk.length < 20) {
                        return false;
                    } else {
                        return true;
                    }

                },

                postReq() {
                    this.$http.get("https://tbprestapi.herokuapp.com/api/home/entry/" + this.id, {
                        flag: "hi"
                    }).then(function(data) {

                        this.entry = data.body;

                    }, function(res) {
                        console.log(res);
                    });

                },

                getTimeStamp() {
                    var now = new Date();
                    var day = now.getDate();
                    var month = now.getMonth() + 1;
                    var year = now.getFullYear();

                    if (day < 10) {
                        day = "0" + day;
                    }
                    if (month < 10) {
                        month = "0" + month;
                    }
                    return day + "/" + month + "/" + year;
                },

                toggleMobileMenu() {
                    this.mobileshow = !this.mobileshow;
                },

                validateForm() {

                    if(this.comment.comment.length > 0) {  
                     return true ;
                    } else {
                     return false; 
                    }

                }

        },


}

</script>

<style scoped>

@media only screen 
  and (min-device-width: 320px) 
  and (max-device-width: 768px)
   {

    #entryBox {
        margin-top: 3em;
    }
}

@media only screen   
  and (min-device-width: 769px)
   {

  #entryBox {
    margin-top: 5em;
}
}



#description {
    font-family: helvetica, arial, sans-serif;
    line-height: 2rem;
    word-break: break-word;
    font-size: 12pt;
}

#commentSession {
    margin-top: 2em;
    margin-bottom: 2em;
}

#commentbox {
    margin-top: 0.5em;
}

#content {
    margin: 2em 0.1em 0em 0.1em;
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

.drop-in-enter-active,
.drop-in-leave-active {
    -o-transition: all 0.5s;
    transition: all 0.5s;
    -webkit-transition: all 0.5s;
}

.drop-in-enter,
.drop-in-leave-to {
    opacity: 0;
    -ms-transform: translateX(-200px);
        transform: translateX(-200px);
    -webkit-transform: translateX(-200px);
}

.infobox-enter-active,
.info-box-leave-active {
    -o-transition: all 1s;
    transition: all 1s;
    -webkit-transition: all 1s;
}

.infobox-enter,
.infobox-leave-to {
    opacity: 0;
    -ms-transform: translateY(-50px);
        transform: translateY(-50px);
    -webkit-transform: translateY(-50px);
}

.comment-seg-enter-active,
.comment-seg-leave-active {
    -webkit-transition: all 1s;
    -o-transition: all 1s;
    transition: all 1s;
}

.comment-seg-enter,
.comment-seg-leave-to {
    opacity: 0;
    -ms-transform: translateX(30px);
        transform: translateX(30px);
    -webkit-transform: translateX(30px);
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
