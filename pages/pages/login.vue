<template>
  <v-row>
    <v-col class="text-center">
      <img
        src="/v.png"
        alt="Vuetify.js"
        class="mb-5"
      >
      <v-container>
        <h3>Login</h3>
        <UserAuthForm button-text='Login' :submit-form='Login'/>
      </v-container>
      <blockquote class="blockquote">
        &#8220;First, solve the problem. Then, write the code.&#8221;
        <footer>
          <small>
            <em>&mdash;John Johnson</em>
          </small>
        </footer>
      </blockquote>
      <v-snackbar
        v-if='stranger'
        v-model="stranger"
      >
        Fuck off stranger.

        <template #action="{ attrs }">
          <v-btn
            color="pink"
            text
            v-bind="attrs"
            @click="stranger = false"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-col>
  </v-row>
</template>
<script>
import UserAuthForm from '../components/UserAuthForm'

export default {
  auth : false,
  components: {
    UserAuthForm
  },
  data() {
    return {
      user: {
        "userNameOrEmailAddress": "",
        "password": "",
        "rememberMe": true
      },
      stranger : false
    }
  },
  methods: {
    Login(payload) {
      try {
        this.user.userNameOrEmailAddress = payload.userName;
        this.user.password = payload.password;
        this.$auth.loginWith('local', { data: this.user }).then(response => {
          if (response.status === 200){
            this.$auth.redirect('home')
            console.log('You came in')
          }
        })
        console.log("Hello")
      }catch (e){
        console.log(e);
        this.stranger = true
      }
    }
  }
}

</script>

