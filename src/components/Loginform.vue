<template>
    <div class="wrapper fadeInDown">
        <div v-if="loading" style="position:absolute; display: flex; width: 100%; height: 100%; justify-content: center; align-items: center; z-index: 10; background-color: rgba(0,0,0,0.5)">
            <div class="spinner-border text-primary " style="height: 7rem; width: 7rem"></div>
        </div>
        <div id="formContent">
            <!-- Tabs Titles -->
            <!-- Icon -->
            <div class="fadeIn first">
                <h1 id="burguer">New User</h1>
            </div>
            <!-- Login Form -->
            <form v-on:submit.prevent>
                <input type="text" id="name" class="fadeIn second" placeholder="name" required v-model="user.name">
                <input type="text" id="lastname" class="fadeIn third" placeholder="last name" required v-model="user.lastname">
                <input type="text" id="email" class="fadeIn third" placeholder="email" required v-model="user.email">
                <input type="text" id="phone" class="fadeIn third" placeholder="phone" required v-model="user.phone">
                <input type="text" id="address" class="fadeIn third" placeholder="address" required v-model="user.address">
                <input type="password" id="pass" class="fadeIn third" placeholder="Password" required v-model="user.password">
                <div class="fadeIn third">
                    <h5>Sign up as:</h5>
                  <toggle-switch :disabled="false" :options="myOptions" v-on:change="updateMap()" v-model="userType" />
                </div>
            </form>
            <input type="button" class="fadeIn fourth" value="Create" v-on:click="createUser" >
        </div>
    </div>
</template>

<script>
    import API from "../service/api";
    export default {
        name: "Loginform",
        data() {
            return {
                loading: false,
              userType: "Customer",
              user: {
                name: "",
                lastname: "",
                email: "",
                password: "",
                phone: "",
                address: "",
                usertype: "customer",
              },
              myOptions: {
                layout: {
                    color: '#007AFF',
                    backgroundColor: 'white',
                    selectedColor: '#007AFF',
                    selectedBackgroundColor: '#007AFF',
                    borderColor: '#007AFF',
                    fontFamily: 'inherit',
                    fontWeight: 'normal',
                    fontWeightSelected: 'bold',
                    squareCorners: false,
                    noBorder: false
                },
                size: {
                    fontSize: 1,
                    height: 3.5,
                    padding: 1,
                    width: 15
                },
                items: {
                    delay: .4,
                    preSelected: 'Customer',
                    disabled: false,
                    labels: [
                        {name: 'Customer', color: 'white', backgroundColor: '#007AFF'},
                        {name: 'Supplier', color: 'white', backgroundColor: '#007AFF'}
                    ]
                }
              }
            }
        },
        methods: {
            updateMap() {
                let self = this;
                self.user.usertype = self.userType.toLowerCase();
            },
            createUser() {
                this.loading = true;

                let self = this;
                API.post("/auth/signup", self.user)
                .then(() => this.userCreated())
                .catch((message) => {
                    if (message.response.data.errors != null) {
                        var errors = message.response.data.errors;
                        if (errors.address != null) {
                            this.loading = false;
                            this.$toastr.error(errors.address);
                        }
                        if (errors.email != null) {
                            this.loading = false;
                            this.$toastr.error(errors.email);
                        }
                        if (errors.lastname != null) {
                            this.loading = false;
                            this.$toastr.error(errors.lastname);
                        }
                        if (errors.name != null){
                            this.loading = false;
                            this.$toastr.error(errors.name);
                        }
                        if (errors.password != null){
                            this.$toastr.error(errors.name);
                            this.$toastr.error(errors.password);
                        }
                    }
                    else if (message.response.data != ""){
                        this.loading=false;
                        this.$toastr.error(message.response.data);
                    }
                    else {
                        this.loading = false;
                        this.$toastr.error("An unexpected error has happened.");
                    }
                    });
            },
            userCreated(){
              this.$toastr.success("User was successfully created!!!");
              this.$router.push('/');
            }
        }
    }
</script>

<style scoped>
    #formContent{
        height: 100%;
        align-content: center;
        background-color: rgba(0,0,0,0.5) !important;
    }

    body {
        font-family: "Poppins", sans-serif;
        height: 100vh;
    }

    img{
        max-width: 3%;
        display: inline-block;

    }

    #burguer{
        color: white;
        display: inline-block;
    }

    a {
        color: #92badd;
        display:inline-block;
        text-decoration: none;
        font-weight: 400;
    }

    h2 {
        text-align: center;
        font-size: 16px;
        font-weight: 600;
        text-transform: uppercase;
        display:inline-block;
        margin: 40px 8px 10px 8px;
        color: #cccccc;
    }

    h5 {
      color: white;
    }

    label {
        color: white;
    }

    /* STRUCTURE */

    .wrapper {
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: center;
        width: 100%;
        min-height: 100%;
        padding: 20px;
    }

    #formContent {
        -webkit-border-radius: 10px 10px 10px 10px;
        border-radius: 10px 10px 10px 10px;
        background: #fff;
        padding: 30px;
        width: 90%;
        max-width: 450px;
        position: relative;
        padding: 0px;
        -webkit-box-shadow: 0 30px 60px 0 rgba(0,0,0,0.3);
        box-shadow: 0 30px 60px 0 rgba(0,0,0,0.3);
        text-align: center;
    }

    #formFooter {
        background-color: #f6f6f6;
        border-top: 1px solid #dce8f1;
        padding: 25px;
        text-align: center;
        -webkit-border-radius: 0 0 10px 10px;
        border-radius: 0 0 10px 10px;
    }

    /* TABS */

    h2.inactive {
        color: #cccccc;
    }

    h2.active {
        color: #0d0d0d;
        border-bottom: 2px solid #5fbae9;
    }

    /* FORM TYPOGRAPHY*/

    input[type=radio] {
      margin: 10px;
    }

    input[type=button], input[type=submit], input[type=reset]  {
        background-color: #56baed;
        border: none;
        color: white;
        padding: 15px 80px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        text-transform: uppercase;
        font-size: 13px;
        width: 85%;
        -webkit-box-shadow: 0 10px 30px 0 rgba(95,186,233,0.4);
        box-shadow: 0 10px 30px 0 rgba(95,186,233,0.4);
        -webkit-border-radius: 5px 5px 5px 5px;
        border-radius: 5px 5px 5px 5px;
        margin: 5px 20px 40px 20px;
        -webkit-transition: all 0.3s ease-in-out;
        -moz-transition: all 0.3s ease-in-out;
        -ms-transition: all 0.3s ease-in-out;
        -o-transition: all 0.3s ease-in-out;
        transition: all 0.3s ease-in-out;
    }

    input[type=button]:hover, input[type=submit]:hover, input[type=reset]:hover  {
        background-color: #39ace7;
    }

    input[type=button]:active, input[type=submit]:active, input[type=reset]:active  {
        -moz-transform: scale(0.95);
        -webkit-transform: scale(0.95);
        -o-transform: scale(0.95);
        -ms-transform: scale(0.95);
        transform: scale(0.95);
    }

    input[type=text] {
        background-color: #f6f6f6;
        border: none;
        color: #0d0d0d;
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        margin: 5px;
        width: 85%;
        border: 2px solid #f6f6f6;
        -webkit-transition: all 0.5s ease-in-out;
        -moz-transition: all 0.5s ease-in-out;
        -ms-transition: all 0.5s ease-in-out;
        -o-transition: all 0.5s ease-in-out;
        transition: all 0.5s ease-in-out;
        -webkit-border-radius: 5px 5px 5px 5px;
        border-radius: 5px 5px 5px 5px;
    }

    input[type=text]:focus {
        background-color: #fff;
        border-bottom: 2px solid #5fbae9;
    }

    input[type=password] {
        background-color: #f6f6f6;
        border: none;
        color: #0d0d0d;
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        margin: 5px;
        width: 85%;
        border: 2px solid #f6f6f6;
        -webkit-transition: all 0.5s ease-in-out;
        -moz-transition: all 0.5s ease-in-out;
        -ms-transition: all 0.5s ease-in-out;
        -o-transition: all 0.5s ease-in-out;
        transition: all 0.5s ease-in-out;
        -webkit-border-radius: 5px 5px 5px 5px;
        border-radius: 5px 5px 5px 5px;
    }

    input[type=password]:focus {
        background-color: #fff;
        border-bottom: 2px solid #5fbae9;
    }

    /* ANIMATIONS */

    /* Simple CSS3 Fade-in-down Animation */
    .fadeInDown {
        -webkit-animation-name: fadeInDown;
        animation-name: fadeInDown;
        -webkit-animation-duration: 1s;
        animation-duration: 1s;
        -webkit-animation-fill-mode: both;
        animation-fill-mode: both;
    }

    @-webkit-keyframes fadeInDown {
        0% {
            opacity: 0;
            -webkit-transform: translate3d(0, -100%, 0);
            transform: translate3d(0, -100%, 0);
        }
        100% {
            opacity: 1;
            -webkit-transform: none;
            transform: none;
        }
    }

    @keyframes fadeInDown {
        0% {
            opacity: 0;
            -webkit-transform: translate3d(0, -100%, 0);
            transform: translate3d(0, -100%, 0);
        }
        100% {
            opacity: 1;
            -webkit-transform: none;
            transform: none;
        }
    }

    /* Simple CSS3 Fade-in Animation */
    @-webkit-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
    @-moz-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
    @keyframes fadeIn { from { opacity:0; } to { opacity:1; } }

    .fadeIn {
        opacity:0;
        -webkit-animation:fadeIn ease-in 1;
        -moz-animation:fadeIn ease-in 1;
        animation:fadeIn ease-in 1;

        -webkit-animation-fill-mode:forwards;
        -moz-animation-fill-mode:forwards;
        animation-fill-mode:forwards;

        -webkit-animation-duration:1s;
        -moz-animation-duration:1s;
        animation-duration:1s;
    }

    .fadeIn.first {
        -webkit-animation-delay: 0.4s;
        -moz-animation-delay: 0.4s;
        animation-delay: 0.4s;
    }

    .fadeIn.second {
        -webkit-animation-delay: 0.6s;
        -moz-animation-delay: 0.6s;
        animation-delay: 0.6s;
    }

    .fadeIn.third {
        -webkit-animation-delay: 0.8s;
        -moz-animation-delay: 0.8s;
        animation-delay: 0.8s;
    }

    .fadeIn.fourth {
        -webkit-animation-delay: 1s;
        -moz-animation-delay: 1s;
        animation-delay: 1s;
    }

    /* Simple CSS3 Fade-in Animation */
    .underlineHover:after {
        display: block;
        left: 0;
        bottom: -10px;
        width: 0;
        height: 2px;
        background-color: #2586ed;
        content: "";
        transition: width 0.2s;
    }

    .underlineHover:hover {
        color: #0d0d0d;
    }

    .underlineHover:hover:after{
        width: 100%;
    }
</style>