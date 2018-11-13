<template>

<div id="formholder" class="ui middle aligned centered grid">

    <div class="six wide computer only center aligned column">
        <h2 class="ui teal image header">
          <div class="content">
            Register an account
          </div>
        </h2>
        <form class="ui large form">
            <div class="ui stacked segment">
                <div class="field">
                    <div class="ui left icon input">
                        <i class="user icon"></i>
                        <input type="text" placeholder="username 5-10 characters" v-model="username" maxlength="10">
                    </div>
                </div>
                <div class="field">
                    <div class="ui left icon input">
                        <i class="lock icon"></i>
                        <input type="password" placeholder="password 5-10 characters" v-model="userPass" maxlength="10">
                    </div>
                </div>
                <div :class="submitClass" @click="userRegister">
                    Register
                </div>
                <div class="ui message">
                    Already registered?
                    <router-link to="/home"> Login here</router-link>
                </div>


            </div>
            <div v-show="errorForm" class="ui error message" id="errorMsg">
                <ul class="list">
                    <li> {{ errorMessage }} </li>
                </ul>
            </div>

        </form>
    </div>

    <div class="mobile only sixteen wide center aligned column" id="mobileform">
        <h2 class="ui teal image header">
          <div class="content">
            Register an account

          </div>
        </h2>
        <form class="ui large form">
            <div class="ui stacked segment">
                <div class="field">
                    <div class="ui left icon input">
                        <i class="user icon"></i>
                        <input type="text" placeholder="username 6-10 characters" v-model="username" maxlength="10">
                    </div>
                </div>
                <div class="field">
                    <div class="ui left icon input">
                        <i class="lock icon"></i>
                        <input type="password" placeholder="password 6-10 characters" v-model="userPass" maxlength="10">
                    </div>
                </div>
                <div class="ui fluid large teal submit button" @click="userRegister">
                    Register
                </div>
                <div class="ui message">
                    Already registered?
                    <router-link to="/home"> Login here</router-link>
                </div>


            </div>
            <div v-show="errorForm" class="ui error message" id="errorMsg">
                <ul class="list">
                    <li> {{ errorMessage }} </li>
                </ul>
            </div>

        </form>
    </div>

    <div class="tablet only ten wide center aligned column">
        <h2 class="ui teal image header">
          <div class="content">
            Register an account

          </div>
        </h2>
        <form class="ui large form">
            <div class="ui stacked segment">
                <div class="field">
                    <div class="ui left icon input">
                        <i class="user icon"></i>
                        <input type="text" placeholder="username 6-10 characters" v-model="username" maxlength="10">
                    </div>
                </div>
                <div class="field">
                    <div class="ui left icon input">
                        <i class="lock icon"></i>
                        <input type="password" placeholder="password 6-10 characters" v-model="userPass" maxlength="10">
                    </div>
                </div>
                <div class="ui fluid large teal submit button" @click="userRegister">
                    Register
                </div>
                <div class="ui message">
                    Already registered?
                    <router-link to="/home"> Login here</router-link>
                </div>

            </div>
            <div v-show="errorForm" class="ui error message" id="errorMsg">
                <ul class="list">
                    <li> {{ errorMessage }} </li>
                </ul>
            </div>

        </form>
    </div>

</div>

</template>

<script>

import bodyComp from "./body.vue"

export default {
    data() {
            return {
                username: "",
                userPass: "",
                errorMessage: "",
                errorForm: false
            }
        },
        methods: {

            userRegister() {

                    var self = this;

                    this.$http.post("https://tbprestapi.herokuapp.com/api/register", {
                        username: this.username,
                        password: this.userPass
                    }).then(function(res) {

                        if (res.body.success === false) {

                            self.errorForm = true;
                            self.errorMessage = res.body.message;

                        } else if (res.body.success === true) {

                            console.log(res);

                            self.errorMessage = res.body.message;
                            localStorage.setItem("JWTtoken", res.body.token);
                            localStorage.setItem("username", res.body.username);
                            localStorage.setItem("userId", res.body.userId);


                            self.$router.push("/home");

                        } else {

                            self.errorForm = true;
                            self.errorMessage = res.body.message;
                        }

                    }, function(res) {

                        console.log(res);

                    });
                },

                validateForm() {

                    if (this.username.length >= 4 && this.userPass.length >= 4) {
                        return true;
                    } else {
                        return false;
                    }
                }

        },
        computed: {
            submitClass: function() {

                return {
                    "ui fluid large teal submit button": this.validateForm(),
                    "ui fluid large teal submit disabled button": !this.validateForm()
                }

            }
        }

}

</script>

<style scoped>

#formholder {
    margin-top: 5em;
    margin-bottom: 5em;
}

.fa-times-circle {
    cursor: pointer;
}

#errorMsg {
    display: block;
}
</style>
