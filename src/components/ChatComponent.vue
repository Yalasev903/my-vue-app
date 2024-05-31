<template>
     <div class="chat-container">
       <h1>Chat with AI</h1>
       <div class="chat-box">
         <div v-for="(message, index) in chatHistory" :key="index" :class="['message', message.type]">
           <h2>{{ message.type === 'user-message' ? 'Your message:' : 'AI response:' }}</h2>
           <p>{{ message.text }}</p>
         </div>
       </div>
       <textarea v-model="userInput" placeholder="Type your message here..."></textarea>
       <button @click="sendMessage">Send</button>
     </div>
   </template>
   
   <script>
   import axios from 'axios';
   
   export default {
     data() {
       return {
         userInput: '',
         chatHistory: [],
       };
     },
     methods: {
       async sendMessage() {
         if (this.userInput.trim() === '') return;
   
         // Add user message to chat history
         this.chatHistory.push({ type: 'user-message', text: this.userInput });
   
         try {
           const result = await axios.post('https://api-inference.huggingface.co/models/EleutherAI/gpt-neo-2.7B', {
             inputs: this.userInput,
           }, {
             headers: {
               'Authorization': `Bearer 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`,
               'Content-Type': 'application/json',
             },
           });
   
           // Add AI response to chat history
           this.chatHistory.push({ type: 'ai-message', text: result.data[0].generated_text });
           this.userInput = ''; // Clear input field after sending message
         } catch (error) {
           console.error('Error communicating with AI:', error);
           this.chatHistory.push({ type: 'ai-message', text: `Error communicating with AI: ${error.response ? error.response.data : error.message}` });
         }
       },
     },
   };
   </script>
   
   <style scoped>
   .chat-container {
     max-width: 600px;
     margin: 0 auto;
     padding: 20px;
     border: 1px solid #ccc;
     border-radius: 10px;
     background: linear-gradient(135deg, #00c6ff, #0072ff);
     color: white;
     box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
   }
   
   .chat-box {
     max-height: 400px;
     overflow-y: auto;
     margin-bottom: 20px;
     background-color: rgba(255, 255, 255, 0.1);
     padding: 10px;
     border-radius: 10px;
     box-shadow: inset 0 4px 8px rgba(0, 0, 0, 0.1);
   }
   
   .message {
     padding: 10px;
     margin-bottom: 10px;
     border-radius: 5px;
     box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   
   .user-message {
     background-color: rgba(224, 247, 250, 0.8);
     text-align: right;
     color: black;
   }
   
   .ai-message {
     background-color: rgba(232, 234, 246, 0.8);
     text-align: left;
     color: black;
   }
   
   textarea {
     width: 100%;
     height: 100px;
     margin-bottom: 10px;
     padding: 10px;
     border: 1px solid #ccc;
     border-radius: 5px;
     box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   
   button {
     display: block;
     width: 100%;
     padding: 10px;
     background-color: #007bff;
     color: white;
     border: none;
     border-radius: 5px;
     cursor: pointer;
     box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
   }
   
   button:hover {
     background-color: #0056b3;
   }
   </style>
   