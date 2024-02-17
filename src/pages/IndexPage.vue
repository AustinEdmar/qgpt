<template>
  <q-page padding>


    <div class="chat-container conversation">
        <q-form class="form" @submit="generateResponse">
    <div class="chat-header">Assistente Virtual</div>
    <div class="chat-messages" v-for="(item, index) in log" :key="index">
      <div class="message">
        <span class="row">
        <img src="https://images.unsplash.com/photo-1522529599102-193c0d76b5b6?q=80&w=1470&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D " class="rounded-circle foto "> Minha Pergunta:</span> <p class=" margin-sec ">{{ item.input }}</p> </div>
      <div class="message">
        <span class="row"> <img src="https://plus.unsplash.com/premium_photo-1677269465314-d5d2247a0b0c?q=80&w=1287&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" class="rounded-circle foto">Assistente:</span>
         <p class="margin-sec"> {{ item.response }}</p> </div>
    </div>
    <div class="chat-input row ">
      <q-input
        bg-color="white"
          v-model="input"
          class="btn-sub"
          style="width: 75%; color: white;
               padding-right: 5px;
           "
           filled
          label="Pergunte a inteligencia virtual"
        />  <q-btn type="submit"  color="secondary"  >
          <span v-if="spinner == false">
            Perguntar
          </span>
          <span  v-if="spinner">
 <q-spinner-cube

          color="orange"
          size="2em"
        />
          </span>

             </q-btn>
        </div>
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
      spinner: false,

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
       this.spinner = true
      await this.completeCall(this.input).then(() => {

        try {

          this.log.unshift({
          input: this.input,
          response: this.response,
        });

        } catch (error) {
          console.log(error)
        }
        finally{
            this.spinner = false
        }

        (this.input = ""), (this.response = "");
      });

    },
  },
});
</script>

<style>


 p {
 padding: 10px;
  background: white;
  word-wrap: break-word;
  border-radius: 0 10px 10px 10px;
}


.foto{
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
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

    .margin-sec{
      margin-top: 5px;
    }

    /* Estilo para as mensagens */
    .chat-messages {
      height: 300px;
      overflow-y: scroll;
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


.conversation {
  height: calc(100% - 12px);
  position: relative;
  background: url("https://i.ibb.co/3s1f9Jq/default-wallpaper.png") repeat;
  z-index: 0;
}

.conversation ::-webkit-scrollbar {
  transition: all .5s;
  width: 5px;
  height: 1px;
  z-index: 10;
}

.conversation ::-webkit-scrollbar-track {
  background: transparent;
}

.conversation ::-webkit-scrollbar-thumb {
  background: #b3ada7;
}

.conversation .conversation-container {
  height: calc(100% - 68px);
  box-shadow: inset 0 10px 10px -10px #000000;
  overflow-x: hidden;
  padding: 0 16px;
  margin-bottom: 19px;
}

.conversation .conversation-container:after {
  content: "";
  display: table;
  clear: both;
}


/* Messages */

.message {
  color: #000;
  clear: both;
  line-height: 18px;
  font-size: 15px;
  padding: 8px;

  position: relative;
  margin: 8px 0;
  max-width: 85%;
  word-wrap: break-word;
  z-index: -1;
}

@media  screen and (max-width: 800px) {
   .chat-container {
      width: 400px;
      margin: 20px auto;
      border: 1px solid #ccc;
      border-radius: 5px;
      padding: 10px;
    }

    .btn-sub{
width: 65%!important;

}
}


</style>
