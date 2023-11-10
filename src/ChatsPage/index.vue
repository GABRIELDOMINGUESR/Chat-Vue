<template>
  <div style="height: 100vh">
    <!-- Botão flutuante para abrir a sala de bate-papo com ícone -->
    <label for="abrir">
      <img class="ce-new-chat-button" v-if="!chatVisible" src="../img/chat-icon.png" alt="">
    </label>

    <button @click="toggleChat" class="ce-new-chat-button" id="abrir" v-if="!chatVisible" hidden>
      <i class="fas fa-comments"></i> 
    </button>
    
    <!-- Sala de bate-papo visível somente se chatVisible for verdadeiro -->
    <PrettyChatWindow
      v-if="chatVisible"
      :projectId="projectId"
      :username="username"
      :secret="secret"
      @closeChat="handleChatClose"
    />

    <!-- Botão "Voltar" no canto inferior esquerdo -->
    <button @click="goBack" class="ce-back-button" v-if="chatVisible">Voltar</button>
  </div>
</template>

<style>
.ce-new-chat-button {
  width: 60px;
  position: fixed;
  bottom: 22px;
  right: 22px; 
  color: black;
  /* Posição flutuante no canto inferior direito */
}

.ce-back-button {
  width: 55px;
  position: fixed;
  bottom: 22px;
  left: 22px;
   /* Posição flutuante no canto inferior esquerdo */
}
</style>

<script>
import { PrettyChatWindow } from "react-chat-engine-pretty";
import { applyReactInVue } from "veaury";

export default {
  data() {
    return {
      projectId: import.meta.env.VITE_CHAT_ENGINE_PROJECT_ID,
      chatVisible: false, // Inicialmente oculto
    };
  },
  components: {
    PrettyChatWindow: applyReactInVue(PrettyChatWindow),
  },
  props: {
    username: {
      type: String,
      required: true,
    },
    secret: {
      type: String,
      required: true,
    },
  },
  methods: {
    toggleChat() {
      this.chatVisible = true;
    },
    goBack() {
      this.chatVisible = false; // Oculta a sala de bate-papo ao voltar
    },
    handleChatClose() {
      this.chatVisible = false;
    },
  },
};
</script>
