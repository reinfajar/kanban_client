<template>
      <article class="article">
        <div class="container" :class="{'sign-up-active' : signUp}">
          <div class="overlay-container">
            <div class="overlay">
              <div class="overlay-left">
                <h2>Welcome Back!</h2>
                <p>Please login with your personal info</p>
                <button class="invert" id="signIn" @click="signUp = !signUp">Sign In</button>
              </div>
              <div class="overlay-right">
                <h2>Hello, Friend!</h2>
                <p>Please enter your personal details</p>
                <button class="invert" id="signUp" @click="signUp = !signUp">Sign Up</button>
              </div>
            </div>
          </div>
          <form class="sign-up" @submit.prevent="register">
            <h2>Create login</h2>
            <div>Use your email for registration</div>
            <input type="text" placeholder="Name" v-model="name" />
            <input type="email" placeholder="Email" v-model="email" />
            <input type="password" placeholder="Password" v-model="password" />
            <button>Sign Up</button>
          </form>
          <form class="sign-in" @submit.prevent="login">
            <h2>Sign In</h2>
            <div>Use your account</div>
            <input type="email" placeholder="Email" v-model="emailLogin" autofocus/>
            <input type="password" placeholder="Password" v-model="passwordLogin" />
            <a href="#">Forgot your password?</a>
            <button>Sign In</button>
           <g-signin-button
            :params="googleSignInParams"
             @success="onSignInSuccess"
             @error="onSignInError">
             Sign in with Google
            </g-signin-button>
          </form>
        </div>
      </article>
</template>

<script>
import Axios from '../config/axios'
import GSignInButton from 'vue-google-signin-button'
export default {
    name : 'Login',
    components: {
            GSignInButton
        }, 
    data() {
        return {
            name : '',
            email : '',
            password : '',
            emailLogin : '',
            passwordLogin : '',
            signUp: false,
            googleSignInParams: {
              client_id: '572723180428-759mrmtuddaipef9u0bv9vneo46ap6nb.apps.googleusercontent.com'
            }
        }
    },
    methods : {
        login(){
          axios({
            url : "/login",
            method : "POST",
            data : {
              email : this.emailLogin,
              password : this.passwordLogin
            }
          })
              .then(({data}) => {
                  localStorage.setItem("token", data.token)
                  this.emailLogin = ""
                  this.passwordLogin = ""
                  this.$emit('loginSuccess')
                })
              .catch(({err})=> console.log(err.response))
        },
        onSignInSuccess(googleUser) {
            const idtoken = googleUser.getAuthResponse().id_token;
            axios({
                method: 'POST',
                url: '/google',
                data: {
                  idtoken
                }
              })
              .then(res => {
                localStorage.setItem('token', res.data.token)
                this.$emit('loginSuccess')
              })
              .catch(err => {
                console.log(err)
              })
          },
        onSignInError(error) {
            console.log('OH NOES', error)
        },
        register(){
          axios({
            url : "/register",
            method : "POST",
            data : {
              name : this.name,
              email : this.email,
              password : this.password
            }
          })
                .then(({data}) => {
                  localStorage.setItem("token", data.token)
                  this.name = ""
                  this.email = ""
                  this.password = ""
                })
              .catch(({err})=> console.log(err.response))
        }
    }
}
</script>

<style lang="scss" scoped>
.article {
  padding-top: 20vh;
}
.container {
  position: relative;
  width: 768px;
  height: 480px;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 15px 30px rgba(0, 0, 0, .2),
    0 10px 10px rgba(0, 0, 0, .2);
  background: linear-gradient(to bottom, #efefef, #ccc);

  .overlay-container {
    position: absolute;
    top: 0;
    left: 50%;
    width: 50%;
    height: 100%;
    overflow: hidden;
    transition: transform .5s ease-in-out;
    z-index: 100;
  }

  .overlay {
    position: relative;
    left: -100%;
    height: 100%;
    width: 200%;
    background: linear-gradient(to bottom right, #7FD625, #009345);
    color: #fff;
    transform: translateX(0);
    transition: transform .5s ease-in-out;
  }

  @mixin overlays($property) {
    position: absolute;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: space-around;
    flex-direction: column;
    padding: 70px 40px;
    width: calc(66% - 80px);
    height: calc(140% - 140px);
    text-align: center;
    transform: translateX($property);
    transition: transform .5s ease-in-out;
  }

  .overlay-left {
    @include overlays(-20%);
  }

  .overlay-right {
    @include overlays(0);
    right: 0;
  }
}

h2 {
  margin: 0;
}

p {
  margin: 20px 0 30px;
}

a {
  color: #222;
  text-decoration: none;
  margin: 15px 0;
  font-size: 1rem;
}

button {
  border-radius: 20px;
  border: 1px solid #009345;
  background-color: #009345;
  color: #fff;
  font-size: 1rem;
  font-weight: bold;
  padding: 10px 40px;
  letter-spacing: 1px;
  text-transform: uppercase;
  cursor: pointer;
  transition: transform .1s ease-in;

  &:active {
    transform: scale(.9);
  }

  &:focus {
    outline: none;
  }
}

button.invert {
  background-color: transparent;
  border-color: #fff;
}

form {
  position: absolute;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: space-around;
  flex-direction: column;
  padding: 90px 60px;
  width: calc(66% - 120px);
  height: calc(140% - 180px);
  text-align: center;
  background: linear-gradient(to bottom, #efefef, #ccc);
  transition: all .5s ease-in-out;

  div {
    font-size: 1rem;
  }

  input {
    background-color: #eee;
    border: none;
    padding: 8px 15px;
    margin: 6px 0;
    width: calc(100% - 30px);
    border-radius: 15px;
    border-bottom: 1px solid #ddd;
    box-shadow: inset 0 1px 2px rgba(0, 0, 0, .4), 
      0 -1px 1px #fff, 
      0 1px 0 #fff;
    overflow: hidden;

    &:focus {
      outline: none;
      background-color: #fff;
    }
  }
}

.sign-in {
  left: 0;
  z-index: 2;
}

.sign-up {
  left: 0;
  z-index: 1;
  opacity: 0;
}

.sign-up-active {
  .sign-in {
    transform: translateX(100%);
  }

  .sign-up {
    transform: translateX(100%);
    opacity: 1;
    z-index: 5;
    animation: show .5s;
  }

  .overlay-container {
    transform: translateX(-100%);
  }

  .overlay {
    transform: translateX(50%);
  }

  .overlay-left {
    transform: translateX(0);
  }

  .overlay-right {
    transform: translateX(20%);
  }
}

@keyframes show {
  0% {
    opacity: 0;
    z-index: 1;
  }
  49% {
    opacity: 0;
    z-index: 1;
  }
  50% {
    opacity: 1;
    z-index: 10;
  }
}

.g-signin-button {
  display: inline-block;
  padding: 4px 8px;
  border-radius: 3px;
  background-color: #3c82f7;
  color: #fff;
  box-shadow: 0 3px 0 #0f69ff;
  width: 100%;
  text-align: center;
  height: 2rem;
}
.g-signin-button:hover{
  cursor: pointer;
}

</style>