<template>
  <section class="container">
    <div class="card-container" v-if="currentStep === 'mainMenu'">
      <h1>Sapatuna</h1>
      <h2>O melhor party game forévora</h2>
      <div class="button-container">
        <button @click="currentStep = 'playerMenu'">Jogar</button>
        <button>Donate 💓</button>
      </div>
    </div>
    <div class="card-container" v-else-if="currentStep === 'playerMenu'">
      <h1>Jogadores</h1>
      <ul>
        <li v-for="jogador in jogadores" :key="jogador">{{ jogador }}</li>
      </ul>
      <div class="input-container">
        <label for="jogador">Adicionar novo jogador</label>
        <input type="text" v-model="addingJogador" />
        <button @click="addJogador">Adicionar</button>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      currentStep: "playerMenu",
      jogadores: [],
      addingJogador: "",
    };
  },
  created() {
    const currentJogadores = localStorage.getItem("theme");
    this.checkJogadores(currentJogadores);
  },
  methods: {
    addJogador() {
      if (this.addingJogador === "") {
        return;
      }

      this.jogadores.push(this.addingJogador);
      this.addingJogador = "";
      localStorage.setItem("jogadores", this.jogadores);
      console.log(this.jogadores);
    },
    checkJogadores(jogadores) {
      localStorage.setItem("jogadores", this.jogadores);
    },
    checkTheme(theme) {
      if (theme === null) {
        this.theme = "light";
      } else {
        this.theme = theme;
      }

      if (this.theme === "light") {
        this.isDarkMode = false;
      } else {
        this.isDarkMode = true;
      }
    },

    toggleDarkMode() {
      this.theme === "light" ? (this.theme = "dark") : (this.theme = "light");
      this.checkTheme(this.theme);
      // Finally, let's save the current preference to localStorage to keep using it
      localStorage.setItem("theme", this.theme);
    },
  },
  components: {},
};
</script>

<style scoped lang="scss">
.container {
  margin: 0;
  min-height: 100vh;
  background-color: #ffffff;
  color: black;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;

  .card-container {
    width: auto;
    max-width: 900px;
    padding: 20px 30px;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.4);
    box-shadow: 0 10px 10px 10px rgba(0, 0, 0, 0.2);

    .button-container {
      display: flex;
      justify-content: space-evenly;
      margin-top: 20px;

      button {
        outline: none;
        border: 1px solid black;
        border-radius: 12px;
        padding: 10px 20px;
        width: 100px;

        &:hover {
          cursor: pointer;
        }
      }
    }

    .input-container {
      display: flex;
      flex-direction: column;
    }
  }
}
</style>
