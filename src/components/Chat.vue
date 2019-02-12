<template>
	<beautiful-chat
        :participants="participants"
        :titleImageUrl="titleImageUrl"
        :onMessageWasSent="onMessageWasSent"
        :messageList="messageList"
        :newMessagesCount="newMessagesCount"
        :isOpen="isChatOpen"
        :close="closeChat"
        :open="openChat"
        :showEmoji="true"
        :showFile="true"
        :showTypingIndicator="showTypingIndicator"
        :colors="colors"
        :alwaysScrollToBottom="alwaysScrollToBottom"
        :messageStyling="messageStyling"
	/>
</template>

<script>
import messageHistory from "../chatting/messageHistory";
import chatParticipants from "../chatting/chatProfiles";
import availableColors from "../chatting/colors";
import io from "socket.io-client";

export default {
	data() {
		return {
			participants: chatParticipants,
			titleImageUrl: "https://a.slack-edge.com/66f9/img/avatars-teams/ava_0001-34.png",
			messageList: messageHistory,
			newMessagesCount: 0,
			isChatOpen: false,
			showTypingIndicator: "",
			colors: null,
			availableColors,
			chosenColor: null,
			alwaysScrollToBottom: false,
			messageStyling: true,

			user: "",
			message: "",
			messages: [],
            socket: io("localhost:3000")
		};
	},
	created() {
		this.setColor("green");
    },
	mounted() {
		this.socket.on("MESSAGE", data => {
            this.messageList = [...this.messageList, data];
            //this.messageList.push(data)
		});
	},
	methods: {
		sendMessage(text) {
			if (text.length > 0) {
				this.newMessagesCount = this.isChatOpen ? this.newMessagesCount : this.newMessagesCount + 1
				this.onMessageWasSent({ author: 'support', type: 'text', data: { text } })
			}
		},
		handleTyping(text) {
			this.showTypingIndicator = text.length > 0 ? this.participants[this.participants.length - 1].id : "";
		},
		onMessageWasSent(message) {
			//"SEND_MESSAGE" 이름으로 두번째 인자의 데이터를 보낸다.
			this.socket.emit("SEND_MESSAGE", {
                author: 'support',
                type: message.type,
                data: message.data
			});
			this.messageList = [...this.messageList, message];
		},
		openChat() {
			this.isChatOpen = true;
			this.newMessagesCount = 0;
		},
		closeChat() {
			this.isChatOpen = false;
		},
		setColor(color) {
			this.colors = this.availableColors[color];
			this.chosenColor = color;
		},
		showStylingInfo() {
			this.$modal.show("dialog", {
				title: "Info",
				text:
					"You can use *word* to <strong>boldify</strong>, /word/ to <em>emphasize</em>, _word_ to <u>underline</u>, `code` to <code>write = code;</code>, ~this~ to <del>delete</del> and ^sup^ or ¡sub¡ to write <sup>sup</sup> and <sub>sub</sub>"
			});
		},
		messageStylingToggled(e) {
			this.messageStyling = e.target.checked;
		}
	},
	computed: {
		linkColor() {
			return this.chosenColor === "dark"
				? this.colors.sentMessage.text
				: this.colors.launcher.bg;
		},
		backgroundColor() {
			return this.chosenColor === "dark"
				? this.colors.messageList.bg
				: "#fff";
		}
	}
};
</script>

<style></style>
