<template>
  <v-row>
    <v-col class="text-center">
      <img
        src="/v.png"
        alt="Vuetify.js"
        class="mb-5"
      >
      <v-container>
        <h3>Register</h3>
        <UserAuthForm button-text='Register' :submit-form='registerUser' has-name='true'/>
      </v-container>
      <blockquote class="blockquote">
        &#8220;First, solve the problem. Then, write the code.&#8221;
        <footer>
          <small>
            <em>&mdash;John Johnson</em>
          </small>
        </footer>
      </blockquote>
    </v-col>
  </v-row>
</template>
<script>
import axios from 'axios'
import UserAuthForm from '../components/UserAuthForm'

export default {
  auth : false,
  components : {
    UserAuthForm
  },
  methods : {
    registerUser(data) {
      console.log(data)
      axios.post('https://localhost:44319/api/account/register', data).then(response => {
        console.log(response);
        if (response.status === 200){
          location.href = 'https://localhost:44349/login'
        }
      })
    }
  }
}
</script>
