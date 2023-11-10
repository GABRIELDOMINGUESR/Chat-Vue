<template>
  <div class="login-page">
    <div class="card">
      <form @submit.prevent="login">
        <div class="title">Login</div>
        <input placeholder="Username" type="text" v-model="username" />
        <br />
        <input placeholder="Password" type="password" v-model="password" />
        <br />
        <button type="submit">Login</button>
      </form>
    </div>

    <!-- Exibição da mensagem de erro no topo -->
    <div v-if="errorMessage" class="error-message-top">
      <div class="error-content">
        {{ errorMessage }}
        <div class="error-bar" :style="{ width: errorBarWidth }"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { loginRest } from "./api";

export default {
  data() {
    return {
      username: "",
      password: "",
      errorMessage: "", // Mensagem de erro inicialmente vazia
      errorBarWidth: "100%", // Largura inicial da barra de erro
    };
  },
  methods: {
    login() {
      loginRest(this.username, this.password)
        .then((response) => {
          // Se o login for bem-sucedido, emita o evento onAuth
          this.$emit("onAuth", { ...response.data, secret: this.password });
        })
        .catch((error) => {
          console.log("Login error", error);
          // Defina a mensagem de erro com base no status do erro
          if (error.response.status === 403) {
            this.errorMessage = "Nome de Usuário ou Senha Incorretos.";
          } else {
            this.errorMessage = "Erro no servidor. Tente novamente mais tarde.";
          }

          // Configura a barra de erro para 100% da largura inicialmente
          this.errorBarWidth = "100%";

          // Adiciona a classe temporária para o estilo temporário
          setTimeout(() => {
            this.errorMessage = "";
          }, 5000); // Remove a mensagem após 5 segundos

          // Inicia a contagem regressiva para diminuir a barra de erro
          this.startErrorBarCountdown();
        });
    },
    startErrorBarCountdown() {
      const duration = 5000; // Duração total da animação (em milissegundos)
      const interval = 100; // Intervalo de atualização da barra (em milissegundos)

      let elapsed = 0;

      const updateWidth = () => {
        elapsed += interval;
        const percentage = (duration - elapsed) / duration * 100;

        // Atualiza a largura da barra de erro
        this.errorBarWidth = `${percentage}%`;

        // Se a contagem regressiva terminar, reinicia a mensagem de erro
        if (elapsed >= duration) {
          this.errorMessage = "";
        } else {
          // Agende a próxima atualização
          setTimeout(updateWidth, interval);
        }
      };

      // Inicia a contagem regressiva
      updateWidth();
    },
  },
};
</script>

<style>
.error-message-top {
  position: fixed;
  top: 15px; 
  left: 50%;
  transform: translateX(-50%);
  width: 80%;
  max-height: 400px;
  max-width: 400px;
  background-color: #ffc228ea; 
  color: white;
  text-align: center;
  padding: 10px;
  box-sizing: border-box;
  z-index: 1000;
}

.error-content {
  position: relative;
}

.error-bar {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 3px;
  background-color: white;
  transition: width 0.1s linear;
}
</style>
