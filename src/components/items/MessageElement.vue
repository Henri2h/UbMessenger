<template>
  <div>
    <div class="has-text-centered" v-bind:class="{'is-hidden':!message.UI_showDate}">
      <small>{{message.SendDateFormated}}</small>
    </div>

    <div class="message-container">
      <div v-bind:class="{'is-hidden':!isText, 'has-text-right':sendByUser}">
        <strong
          class="data_message_username"
          v-bind:class="{'is-hidden':!message.UI_showUsername}"
        >{{message.Username}}</strong>
      </div>
      <div class="box-container">
        <div
          class="box"
          v-bind:class="{'sentByUser to-right':sendByUser, 'sentByOther to-left':!sendByUser}"
        >
          <div v-for="image in pictures" :key="image.pos">
            <img class="data_image" :src="image.url" />
          </div>

          <figure class="image" v-bind:class="{'is-hidden':isText}">
            <font-awesome-icon
              :icon="['fas','image']"
              v-bind:class="{'is-hidden':!isLoading}"
              style="height:150px"
            />
            <progress
              class="progress is-large is-info"
              v-bind:class="{'is-hidden':!isLoading}"
              max="100"
              style="margin-top: -50px; position:absolute;"
            >60%</progress>
          </figure>
          <div
            class="data_message_text"
            v-bind:class="{'is-hidden':!isText, 'sentByUser':sendByUser}"
          >{{message.Content}}</div>
          <MessageReaction
            class="mReaction"
            v-bind:class="{'to-right':sendByUser, 'to-left':!sendByUser}"
            v-if="message.messageReactions.length > 0"
            :reactions="message.messageReactions"
          />
          <MessageReadReceipts v-if="message.readBy.length > 0" :readReceipts="message.readBy"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MessageReaction from "./MessageReaction";
import MessageReadReceipts from "./MessageReadReceipts"
export default {
  name: "MessageElement",
  data() {
    return {
      sendByUser: false,
      isLoading: true,
      isText: true,
      imgSource: "",
      pictures: []
    };
  },
  props: ["message"],
  components: { MessageReaction, MessageReadReceipts },
  created: function() {
    if (this.message.senderID == localStorage.userid) {
      this.sendByUser = true;
    }

    if (this.message.attachments !== undefined)
      for (let index = 0; index < this.message.attachments.length; index++) {
        const element = this.message.attachments[index];
        if (element.type == "photo") {
          var data = {
            pos: index,
            url: element.previewUrl,
            width: element.previewWidth,
            height: element.previewHeight
          };
          this.pictures.push(data);
        }
      }

    this.isLoading = false;
  }
};
</script>
<style scoped>
.message-container {
  padding: 0.1rem;
}

.box {
  border-radius: 20px;
  padding: 0.5rem 1rem;
  width: auto;
  max-width: 100%;
  word-wrap: break-word;
  max-width: 80%;
  font-family: Arial;
}

.box-container {
  overflow: auto;
}

.to-right {
  float: right;
}

.to-left {
  float: left;
}

.sentByOther {
  background-color: #eee;
}
.sentByUser {
  background-color: rgb(0, 153, 255);
  color: white;
}

.mReaction {
  position: absolute;
}
.mReaction.to-right {
  right: 2rem;
}
.mReaction.to-left {
  left: 0;
}

.data-image {
  min-width: 40%;
  min-height: 200px;
  max-width: 100%;
}
</style>