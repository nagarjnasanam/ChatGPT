

<template>
  <main>
  <div id="chat_container">
    <div
        v-for="(chat, i) in wrapper"
        :key="i"
        class="wrapper"
        :class="{ ai: chat.isAi }"
      >
        <Chat :chat="chat" :key="i" />
      </div>
  </div>
  <form @submit.prevent="fetchAnswer()">
    <textarea
      rows="1"
      cols="1"
      placeholder="Ask whatever you want"
      v-model="question"
    ></textarea>
    <button class="btn" type="submit">Send</button>
  </form>
  </main>
</template>
<script setup>
import { ref } from "vue";
import Chat from "@/components/Chat.vue";
const question = ref("");
const wrapper = ref([])
const loading = ref(false)
const fetchAnswer = async ()=>{
  try {
    loading.value=true
    wrapper.value.push({
      isAi:false,
      value:question.value
    })
    wrapper.value.push({
      isAi:true,
      value:"loading"
    })
    const res = await fetch('http://localhost:8000',{
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        question: question.value,
      }),
    })
    const data = await res.json();
    console.log(data);
    const parsedData = data.bot
    console.log(parsedData)
    wrapper.value.pop()
    wrapper.value.push({
      isAi:true,
      value:parsedData
    })
  
  } catch (error) {
    console.log("error",error)
    
  }
  finally{
    loading.value=false
    question.value=null
  }
// console.log(question.value)
}
</script>
<style scoped>
.btn{
  color:white;
  border: 1 px solid;
  box-sizing: border-box;
}
</style>
