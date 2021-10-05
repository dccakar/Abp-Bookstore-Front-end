<template>
  <div>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card class="logo py-4 d-flex justify-center">
        <img src='../components/bookstoreLogo.jpg' alt='bookstore'>
      </v-card>
      <v-card>
        <v-card-title class="headline">
          Welcome to the BookStore
        </v-card-title>
        <v-card-text>
          <p>This is a bookstore app developed by Emay engineers.</p>

          <p>Thank you for using this app.</p>
          <v-snackbar
            v-model="snackbar"
          >
            Hello {{ $auth.user.name }}, thank you for using my bookstore.

            <template #action="{ attrs }">
              <v-btn
                color="pink"
                text
                v-bind="attrs"
                @click="snackbar = false"
              >
                Close
              </v-btn>
            </template>
          </v-snackbar>
          <div class="text-xs-right">
            <em><small>&mdash; Deniz Can Çakar</small></em>
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/Books"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
  </div>
</template>
<script>

export default {
  data () {
    return {
      snackbar: true,
    }
  }
}
</script>
