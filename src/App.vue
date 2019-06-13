<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Login</router-link> |
      <router-link to="/home">Home</router-link>
    </div>
    <router-view/>
  </div>
</template>

<script>
import { CometChat } from "@cometchat-pro/chat";

import "./App.css";

export default {
  created() {
    this.initializeApp();
  },
  methods: {
    initializeApp() {
      var appID = process.env.VUE_APP_COMMETCHAT_APP_ID;

      CometChat.init(appID).then(
        () => {
          console.log("Initialization completed successfully");
        },
        error => {
          console.log("Initialization failed with error:", error);
        }
      );
    }
  }
};
</script>
