<template>
    <h1> AI Chatbot</h1>
    <div class="container">
        <div class="messages">
            <div v-for="message in messages">
                {{ message.role }} : {{ message.content }}
            </div>

        </div>
        <div>
            <input v-model="userMessage"  @keyup.enter="sendMessage" type="text" placeholder="Type your questiom">
            <button @click="sendMessage">Submit</button>
        </div>
    </div>
</template>
<script setup>
import {ref} from 'vue'
import axios from 'axios'
const userMessage = ref('');

const openAIKey = process.env.VUE_APP_OPENai_API_KEY
const messages = ref([{role:"system", content: "Welcome to the AI Chatbot"}])

async function sendMessage(){
    messages.value.push({role: 'user', content: userMessage.value})

   try {
    const response = await axios.post('https://api.openai.com/v1/chat/completions', {
      model: 'gpt-4',
      messages: messages.value.map(msg => ({ role: msg.role, content: msg.content })),
    }, {
      headers: {
                'Authorization': `Bearer ${openAIKey}`,
                "Content-Type": "application/json"
            }
    });

    const assitantMessage= response.data.choices[0].message.content
    messages.value.push({ role: "assistant", content: assitantMessage})

    console.log("api response: " , response)

  } catch (error) {
    console.log(error);
    
  }
}


</script>