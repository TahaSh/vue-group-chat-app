<template>
  <div class="home">
     <div id="nav">
       <button class="btn btn-success" @click="logoutUser">Logout</button> 
    </div>

    <div class="form-group">
      <div class="form-group">
        <p>Hi <b>{{ this.username}}</b>, your UID is <b>{{ this.uid }}</b> <br>
        Welcome to this group chat
         </p>

         <div v-for="message in groupMessages" v-bind:key="message.id">
           <p> {{ message.data.text }} </p>
         </div>

         <form v-on:submit.prevent="sendGroupMessage">
           <input type="text" class="form-control" placeholder="Enter your message" v-model="chatMessage">
           <button class="btn btn-success">Send Message</button>
         </form>
        
      </div>
    </div>

    <div id="callScreen"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import { CometChat } from "@cometchat-pro/chat";

export default {
  name: "home",
  data() {
    return {
      username: "",
      uid: "",
      session_id: "",
      receiver_id: null,
      error: false,
      showSpinner: false,
      incomingCall: false,
      ongoingCall: false,
      chatMessage: "",
      groupMessages: []
    };
  },
  created() {
    this.getLoggedInUser();
    let globalContext = this;
    this.fetchGroupMessages();

    var listenerID = "UNIQUE_LISTENER_ID";

    CometChat.addMessageListener(
      listenerID,
      new CometChat.MessageListener({
        onTextMessageReceived: textMessage => {
          console.log("Text message received successfully", textMessage);
          // Handle text message
          // this.groupMessages = [...globalContext.groupMessages, textMessage];
        }
      })
    );
  },
  methods: {
    getLoggedInUser() {
      CometChat.getLoggedinUser().then(
        user => {
          this.username = user.name;
          this.uid = user.uid;
        },
        error => {
          this.$router.push({ name: "homepage" });
          console.log(error);
        }
      );
    },

    logoutUser() {
      CometChat.logout().then(
        success => {
          console.log("Logout completed successfully");
          this.$router.push({ name: "homepage" });
          console.log(success);
        },
        error => {
          //Logout failed with exception
          console.log("Logout failed with exception:", { error });
        }
      );
    },

    sendGroupMessage() {
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
          // Text Message Sent Successfully
          // this.groupMessages = [...globalContext.groupMessages, message];
        },
        error => {
          console.log("Message sending failed with error:", error);
        }
      );
    },

    fetchGroupMessages() {
      const GUID = process.env.VUE_APP_COMMETCHAT_GUID;
      var limit = 30;

      var messagesRequest = new CometChat.MessagesRequestBuilder()
        .setGUID(GUID)
        .setLimit(limit)
        .build();

      messagesRequest.fetchPrevious().then(
        messages => {
          console.log("Message list fetched:", messages);
          // console.log(messages[0].data.text);
          // Handle the list of messages
          this.groupMessages = messages;
        },
        error => {
          console.log("Message fetching failed with error:", error);
        }
      );
    }
  }
};
</script>