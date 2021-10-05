<template>
  <v-form  v-model="valid">
    <v-text-field
      v-if="hasName"
      v-model="userInfo.emailAddress"
      :rules="emailRules"
      label="E-mail"
      required
    />
    <v-text-field v-model="userInfo.userName"
                  label="Name"
                  :rules='userNameRules'
                  :counter='10'
                  />
    <v-text-field v-model="userInfo.password"
                  label="Password"
                  :type="showPassword ? 'text' : 'password'"
                  :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                  :rules='passwordRegisterRules'
                  counter=true
                  @click:append="showPassword = !showPassword"
    />
    <v-btn :disabled="!valid" @click="submitForm(userInfo)">{{ buttonText }}</v-btn>
    <v-dialog
      v-if='hasName'
      transition="dialog-top-transition"
      max-width="600"
    >
      <template #activator="{ on, attrs }">
        <v-btn
          class="mx-2"
          v-bind="attrs"
          fab
          dark
          small
          color="primary"
          v-on="on"
        >
          <v-icon dark>
            mdi-help-circle-outline
          </v-icon>
        </v-btn>
      </template>
      <template #default="dialog">
        <v-card>
          <v-toolbar
            color="primary"
            dark
          >Password specifications.</v-toolbar>
          <v-card-text>
            <div class="text-sm-h5 pa-12">{{ passwordInfo }}</div>
          </v-card-text>
          <v-card-actions class="justify-end">
            <v-btn
              text
              @click="dialog.value = false"
            >Close</v-btn>
          </v-card-actions>
        </v-card>
      </template>
    </v-dialog>
  </v-form>
</template>

<script>

export default {
  // eslint-disable-next-line vue/no-dupe-keys
  props: ["submitForm", "buttonText", "hasName"],
  data() {
    return {
      valid: false,
      help : "mdi-help-circle-outline",
      showPassword: false,
      // eslint-disable-next-line vue/no-dupe-keys
      hasName: false,
      userInfo : {
        "userName": "",
        "emailAddress": "",
        "password": "",
        "appName": "Bookstore"
      },
      dialog : false,
      passwordInfo : "Passwords must be at least 6 characters. " +
        "Passwords must have at least one non alphanumeric character." +
        " Passwords must have at least one digit ('0'-'9'). " +
        "Passwords must have at least one uppercase ('A'-'Z').",
      emailRules: [
        v => !!v || 'E-mail is required',
        v => /.+@.+/.test(v) || 'E-mail must be valid',
      ],
      userNameRules: [
        v => !!v || 'Username is required',
        v => v.length <= 10 || 'Username must be less than 10 characters',
      ],
      passwordRegisterRules: [
        v => !!v || 'Password is required',
        v => v.length >= 6 || 'Password must be at least 6 characters',
        (v) => this.containUpper(v) || 'Password should have capital',
        (v) => this.containsDigit(v) || 'Password should contain a digit'
      ]
    }
  },
  methods : {
    containUpper(input){
      return !!/[A-Z]/.exec(input);
    },
    containsDigit(input){
      return /\d/.test(input);
    }
  }
}
</script>

