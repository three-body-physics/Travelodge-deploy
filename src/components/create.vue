<template>
<transition name="drop-in" appear>
    <div class="ui text container segment">
        <div class="ui huge header">
            <router-link to="/home"><i class="fa fa-caret-square-o-left" style="letter-spacing: 0.25em; color: black;"></i></router-link>Create New Post</div>

        <form class="ui form">
            <div class="field">
                <label>Title</label>
                <input type="text" v-model="blog.name" placeholder="Title">
            </div>
            <div class="field">
                <label>Image</label>
                <input type="text" v-model="blog.image" placeholder="Image url: works with direct urls from reddit, imgur etc.">
            </div>
            <div class="field">
                <label>Description</label>
                <textarea v-model="blog.text" placeholder="Type something here"></textarea>
            </div>

            <div class="ui basic button" @click="postRequest">Submit</div>
        </form>
    </div>
</transition>
</template>

<script>

export default {
    data() {
            return {
                blog: {
                    name: "",
                    image: "",
                    text: ""
                }
            }
        },

        methods: {
            postRequest() {
                    var date = this.getTimeStamp();

                    this.$http.post("https://tbprestapi.herokuapp.com/api/home", {
                        name: this.blog.name,
                        image: this.blog.image,
                        text: this.blog.text,
                        author: localStorage.getItem("username"),
                        date: date
                    }, {
                        headers: {
                            Authorization: "Bearer " + localStorage.getItem("JWTtoken")
                        }
                    }).then(function(res) {

                        console.log(res);
                        this.$router.push("/home/entry/" + res.body.data._id);

                    }, function(res) {

                        console.log("Error Unauthorized");

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

                }

        }
}

</script>

<style scoped>

.container.segment {
    margin-top: 9em;
    margin-bottom: 6em;
}

.drop-in-enter-active,
.drop-in-leave-active {
    -webkit-transition: all 3s;
    -o-transition: all 3s;
    transition: all 3s;
}

.drop-in-enter,
.drop-in-leave-to {
    opacity: 0;
    -webkit-transform: translateY(-50px);
        -ms-transform: translateY(-50px);
            transform: translateY(-50px);
}

</style>
