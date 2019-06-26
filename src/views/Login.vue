<template>
  <div id="auth">
    <div class="wrapper">
        <div class="inner">
            <div class="login-container">
                  <div class="login-form-container">
                      <form v-on:submit.prevent="authLoginUser">
                          <h3>Welcome Back</h3>
                          <div class="form-wrapper">
                              <label>Username</label>
                              <input type="text" name="username" id="username" v-model="username" placeholder="Enter your username" class="form-control" required>
                          </div>
                          <button type="submit">LOG IN &nbsp;&nbsp;<span v-if="showSpinner" class="fa fa-spin fa-spinner"></span> </button>
                      </form>
                  </div>

                  <div class="login-image-container">
                      <div class="image-holder">
                          <img src="../assets/illustration.svg" alt="">
                      </div>
                  </div>
           </div>
        </div>
    </div>
  </div>
</template>

<script>
import { CometChat } from "@cometchat-pro/chat";
export default {
  data() {
    return {
      username: "",
      showSpinner: false
    };
  },
  methods: {
    authLoginUser() {
      var apiKey = process.env.VUE_APP_COMMETCHAT_API_KEY;
      this.showSpinner = true;

      CometChat.login(this.username, apiKey).then(
        () => {
          this.showSpinner = false;
          this.$router.push({
            name: "home"
          });
        },
        error => {
          this.showSpinner = false;
          console.log("Login failed with error:", error.code);
        }
      );
    }
  }
};
</script>
