---
layout: default
title: Chatbot
nav_order: 4
description: "Student blog"
permalink: /chatbot
---



<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    #chat-container {
        max-width: 600px;
        margin: 20px auto;
        border: 1px solid #ccc;
        padding: 20px;
        height: 100vh;
    }

    #messages {
        list-style-type: none;
        padding: 0;
        margin: 0;
        overflow-y: scroll;
        height: 80%;
    }

    .message {
        padding: 8px;
        margin-bottom: 8px;
        border-radius: 8px;
        overflow-wrap: break-word;
    }

    .user1 {
        background-color: #a0c4ff;
        text-align: left;
    }

    .user2 {
        background-color: #ffc0cb;
        text-align: right;
    }

    #message-input {
        width: 80%;
        padding: 8px;
        margin-right: 8px;
    }

    #send-button {
        padding: 8px;
        cursor: pointer;
    }
</style>
<div id="chat-container">
    <ul id="messages"></ul>
    <div>
        <input type="text" id="message-input" onkeypress="sendMessage()" placeholder="Type your message...">
        <button onclick="sendMessage()" id="send-button">Send</button>
    </div>
</div>

<script>
    var currentUser = 'user1';

    function toggleUser() {
        currentUser = currentUser === 'user1' ? 'user2' : 'user1';
    }

    var generating = false;

    async function sendMessage() {
        var event = window.event;
        if(generating) return;
        if(event.type == 'keypress' && event.keyCode != 13) {
            return;
        }

        var messageInput = document.getElementById('message-input');
        var message = messageInput.value.trim();
        await createBubble(message);
        toggleUser();
        messageInput.value = '';


        var newMessage = document.createElement('li');
        await createBubble(response, newMessage);
        var response = await generateResponse(message, newMessage);
        toggleUser();
    }

    var messagesContainer = document.getElementById('messages');
    async function createBubble(message, newMessage) {
        if (message !== '') {
            if(!newMessage) {
                newMessage = document.createElement('li');
            }

            newMessage.textContent = message;
            newMessage.className = 'message ' + currentUser;
            messagesContainer.appendChild(newMessage);

            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
    }

    var memory = [];

    async function generateResponse(prompt, messageBox) {
        generating = true;
        const url = 'http://127.0.0.1:11434/api/generate';

        const system = "talk in a heavy southern accent and uses the words like howdy and other old time stuff, you are still a bot.";
        const data = {
            model: 'mistral',
            context: memory,
            system,
            prompt,
        };

        const options = {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data),
        };

        var response = await fetch(url, options);
        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const reader = response.body.getReader();

        while(true) {
            let { done, value } = await reader.read();
            let values = new TextDecoder("utf-8").decode(value).trim().split("\n");

            for(let value of values) {
                if(!value) continue;
                let json = JSON.parse(value);
                if (json['done']) {
                    console.log('Streaming complete');
                    memory = json['context'];
                    break;
                }

                messageBox.innerText += json['response'];
            }

            if (done) {
                break;
            }
        }

        generating = false;
    }
</script>
