<template>
<div>
    <nav>
        <div class="nav-left-section">
            <img class="logo" src="../assets/logo.svg">
            <span id="title">Chat</span>
        </div>
        <div class="nav-right-section">
            <span class="welcome-message">Welcome <b>{{ name }}</b> </span>&nbsp;
            <img src="../assets/profile-lady.svg">
            <span
                v-if="!loggingOut"
                class="logout-button"
                @click="logoutUser"
            >
                 Logout <i class="fa fa-sign-out"></i>
            </span>
            <spinner
                v-if="loggingOut"
                :size="20"
            />
        </div>
    </nav>
</div>
</template>

<script>
import Spinner from './Spinner.vue'
import { CometChat } from "@cometchat-pro/chat";

export default {
    components: { Spinner },
    props: ["name"],
    data () {
        return {
            loggingOut: false
        }
    },

    methods: {
        logoutUser() {
            // do not do anything if the user is already being logged out
            if (this.loggingOut) {
                return
            }
            this.loggingOut = true;
            CometChat.logout().then(
            success => {
              console.log("Logout completed successfully");
              this.$router.push({
                name: "homepage"
              });
              this.loggingOut = false;
              console.log(success);
            },
            error => {
              //Logout failed with exception
              console.log("Logout failed with exception:", {
                error
              });
            }
            );
        }
    }
};
</script>
