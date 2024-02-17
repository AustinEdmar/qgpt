<template>
  <q-page padding>


    <div class="chat-container">
        <q-form class="form" @submit="generateResponse">
    <div class="chat-header">Assistente Virtual</div>
    <div class="chat-messages" v-for="(item, index) in log" :key="index">
      <div class="message"> <span class="message-sender text-white">John:</span> <span class="message-text text-white">{{ item.input }}</span> </div>
      <div class="message"> <span class="message-sender text-white">Jane:</span> <span class="message-text text-white"> {{ item.response }}</span> </div>
    </div>
    <div class="chat-input row "> <q-input
        bg-color="white"
          v-model="input"
          style="width: 80%; color: white;
               padding-right: 5px;
           "
           filled
          label="Pergunte ao Assistente"
        />  <q-btn type="submit" color="secondary" label="Perguntar" /> </div>
              </q-form>
  </div>


  </q-page>
</template>



<script>
import { defineComponent } from "vue";
import { OpenAI } from "openai";

export default defineComponent({
  name: "IndexPage",
  data() {
    return {
      OPENAI_API_KEY: "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",
      input: "",
      response: "",
      log: [],
      messages: [],
    };
  },

  methods: {
    async completeCall(input) {
      this.messages.push({ role: "user", content: input });
      const openai = new OpenAI({
        apiKey: this.OPENAI_API_KEY,
        dangerouslyAllowBrowser: true,
      });

      const completion = await openai.chat.completions.create({
        messages: this.messages,
        model: "gpt-3.5-turbo",
      });

      //console.log(completion.choices[0].message.content);

      this.messages.push({
        role: completion.choices[0].message.role,
        content: completion.choices[0].message.content,
      });
      this.response = completion.choices[0].message.content;
    },
    async generateResponse() {
      await this.completeCall(this.input).then(() => {
        console.log('enviei')
        this.log.unshift({
          input: this.input,
          response: this.response,
        });

        (this.input = ""), (this.response = "");
      });
      console.log(this.input, "ola dombaxe");
    },
  },
});
</script>

<style>
/* .main {


}

.form {
  display: flex;

}
.bg-gray{
    background-color: rgba(250, 250, 244, 0.534);
}

.bg-success{
  background-color: rgb(108, 235, 135);
}
 */

 .chat-container {
      width: 600px;
      margin: 20px auto;
      border: 1px solid #ccc;
      border-radius: 5px;
      padding: 10px;
    }

    /* Estilo para o cabeçalho */
    .chat-header {
      background-color: #075e54;
      color: white;
      padding: 10px;
      text-align: center;
      font-weight: bold;
    }

    /* Estilo para as mensagens */
    .chat-messages {
      height: 300px;
      overflow-y: scroll;
    }

    .message {
      margin-bottom: 10px;
    }

    .message-sender {
      font-weight: bold;
      margin-right: 5px;
    }

    /* Estilo para a entrada de texto e botão */
    .chat-input {
      margin-top: 10px;
    }

    .chat-input input[type="text"] {
      width: 80%;
      padding: 5px;
    }

    .chat-input button {
      padding: 5px 10px;
    }


</style>
