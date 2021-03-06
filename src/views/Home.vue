<template>
  <div>
    <nav-bar :name="this.username" />
    <div id="home">
      <div class="wrapper">
        <div class="container">
          <div class="msg-header">
              <div class="active">
                  <h5># General</h5>
              </div>

              <div class="header-icons">
                  <span class="float-right" @click="logoutUser">Logout <i class="fa fa-sign-out"></i>
                  <i v-if="loggingOut" class="fa fa-spin fa-spinner"></i>
                  </span>
              </div>
          </div>

          <div class="chat-page">
              <div class="msg-inbox">
                  <div class="chats" id="chats">
                      <div class="msg-page" id="msg-page">

                        <div class="text-center img-fluid" v-if="!groupMessages.length" id="empty-chat">
                          <div class="empty-chat-holder">
                            <img src="../assets/illustration-empty-chat.svg" class="img-res" alt="empty chat image">
                          </div>

                          <div>
                            <h2> No new message? </h2>
                            <h6>
                              <center>Send your first message below.</center>
                            </h6>
                          </div>
                        </div>

                        <div v-else>
                          <div v-for="message in groupMessages" v-bind:key="message.id">
                            <div class="received-chats" v-if="message.sender.uid != uid">
                                <div class="received-chats-img">
                                    <img src="../assets/profile-man.svg" alt="">
                                </div>

                                <div class="received-msg">
                                    <div class="received-msg-inbox">
                                        <p><span>{{ message.sender.uid }}</span><br>{{ message.data.text }}</p>
                                    </div>
                                </div>
                              </div>


                            <div class="outgoing-chats" v-else>
                                  <div class="outgoing-chats-msg">
                                      <p>{{ message.data.text }}</p>
                                  </div>

                                  <div class="outgoing-chats-img">
                                      <img src="../assets/profile-lady.svg" alt="">
                                  </div>
                              </div>
                          </div>
                        </div>
                      </div>
                  </div>
              </div>

              <div class="msg-bottom">
                <form v-on:submit.prevent="sendGroupMessage">
                  <div class="input-group">
                      <input type="text" class="form-control" placeholder="Type something..." v-model="chatMessage">
                      <div class="input-group-append">
                          <span class="input-group-text" v-on:click="sendGroupMessage">
                              <i class="fa fa-paper-plane fa-2x"></i>
                              <i v-if="sendingMessage" class="fa fa-spin fa-spinner"></i>
                          </span>
                      </div>
                  </div>
                </form>
              </div>
          </div>
       </div>
      </div>
    </div>
  </div>    
</template>

<script>
// @ is an alias to /src
import { CometChat } from "@cometchat-pro/chat";
import NavBar from "../components/NavBar.vue";

export default {
  name: "home",
  components: {
    NavBar
  },
  data() {
    return {
      username: "",
      uid: "",
      sendingMessage: false,
      chatMessage: "",
      loggingOut: false,
      groupMessages: []
    };
  },
  mounted() {
    let globalContext = this;

    var listenerID = "UNIQUE_LISTENER_ID";
    CometChat.addMessageListener(
      listenerID,
      new CometChat.MessageListener({
        onTextMessageReceived: textMessage => {
          console.log("Text message received successfully", textMessage);
          // Handle text message
          globalContext.groupMessages = [
            ...globalContext.groupMessages,
            textMessage
          ];
          globalContext.scrollToBottom();
        }
      })
    );
  },

  created() {
    this.getLoggedInUser();
  },
  methods: {
    getLoggedInUser() {
      CometChat.getLoggedinUser().then(
        user => {
          this.username = user.name;
          this.uid = user.uid;
        },
        error => {
          this.$router.push({
            name: "homepage"
          });
          console.log(error);
        }
      );
    },

    scrollToBottom() {
      const chat = document.getElementById("msg-page");
      chat.scrollTop = chat.scrollHeight + 30 + "px";
    },

    logoutUser() {
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
    },

    sendGroupMessage() {
      this.sendingMessage = true;
      var receiverID = process.env.VUE_APP_COMMETCHAT_GUID;
      var messageText = this.chatMessage;
      var messageType = CometChat.MESSAGE_TYPE.TEXT;
      var receiverType = CometChat.RECEIVER_TYPE.GROUP;
      let globalContext = this;

      var textMessage = new CometChat.TextMessage(
        receiverID,
        messageText,
        messageType,
        receiverType
      );

      CometChat.sendMessage(textMessage).then(
        message => {
          console.log("Message sent successfully:", message);
          this.chatMessage = "";
          this.sendingMessage = false;
          // Text Message Sent Successfully
          this.groupMessages = [...globalContext.groupMessages, message];
        },
        error => {
          console.log("Message sending failed with error:", error);
        }
      );
    }
  }
};
</script>
