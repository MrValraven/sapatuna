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
      <i class="fas fa-arrow-left" @click="currentStep = 'mainMenu'"></i>
      <h1>Jogadores</h1>
      <ul>
        <li v-for="(jogador, index) in jogadores" :key="index">
          <span>{{ jogador }}</span>
          <select name="orientacaoSexual" id="orientacaoSexual">
            <option value="assexual">Assexual</option>
            <option value="bissexual">Bissexual</option>
            <option selected value="heterossexual">Heterossexual</option>
            <option value="homossexual">Homossexual</option>
            <option value="lesbica">Lésbica</option>
            <option value="pansexual">Pansexual</option>
          </select>
          <select name="genero" id="genero">
            <option value="s">Homem</option>
            <option value="s">Mulher</option>
            <option value="s">Não-binário</option>
          </select>
          <i class="far fa-times-circle" @click="removeJogador(index)"></i>
        </li>
      </ul>
      <div class="input-container">
        <label for="jogador">Adicionar um jogador</label>
        <div class="input-container-inside">
          <input
            type="text"
            v-model="jogadorBeingAdded"
            @keyup.enter="addJogador"
          />
          <i
            alt="Adicionar Jogador"
            class="fas fa-plus-square"
            @click="addJogador"
          ></i>
        </div>
        <div class="input-container-buttons">
          <button @click="currentStep = 'cycleMenu'">Começar</button>
          <button @click="resetJogadores">Reset jogadores</button>
        </div>
      </div>
    </div>
    <div
      class="card-container cycle-container"
      v-else-if="currentStep === 'cycleMenu'"
    >
      <ul>
        <li v-for="jogador in jogadores" :key="jogador">
          {{ jogador }}
        </li>
      </ul>
      <button @click="iniciarJogo">Continuar</button>
    </div>
    <div
      class="challenges-container"
      v-else-if="currentStep === 'challengeMenu'"
    >
      <h1>{{ currentJogador }}</h1>
      <div class="challenges-container-cards">
        <TruthCard @click="getNewTruth" />
        <DareCard @click="getNewDare" />
      </div>
    </div>
    <div class="question-container" v-else-if="currentStep === 'questionMenu'">
      <h1>{{ currentJogador }}{{ currentChallenge }}</h1>
      <button @click="proceedToNextChallenge">Próximo desafio</button>
    </div>
  </section>
</template>

<script>
import DareCard from "@/components/DareCard.vue";
import TruthCard from "@/components/TruthCard.vue";
import truths from "@/static/truths.json";
import dares from "@/static/dares.json";
export default {
  components: {
    TruthCard,
    DareCard,
  },
  name: "Home",
  data() {
    return {
      currentStep: "mainMenu",
      jogadorBeingAdded: "",
      jogadores: [],
      currentJogadores: [],
      currentJogador: "",
      currentIndex: 0,
      truths: truths.soft,
      currentTruths: [],
      currentTruth: "",
      currentTruthIndex: 0,
      dares: dares.fun,
      currentDares: [],
      currentDare: "",
      currentDareIndex: 0,
      currentChallenge: "",
    };
  },
  created() {
    if (localStorage.getItem("jogadores").length === 0) {
      return;
    }
    this.jogadores = localStorage.getItem("jogadores").split(",");
  },
  methods: {
    iniciarJogo() {
      this.currentJogadores = this.jogadores.slice();
      this.currentTruths = this.truths.slice();
      this.currentDares = this.dares.slice();
      this.getNewJogador();
      this.currentStep = "challengeMenu";
    },
    addJogador() {
      if (this.jogadorBeingAdded === "") {
        return;
      }

      this.jogadores.push(this.jogadorBeingAdded);
      this.jogadorBeingAdded = "";
      localStorage.setItem("jogadores", this.jogadores);
    },

    removeJogador(indice) {
      console.log(indice);
      if (this.jogadores.length <= 1) {
        this.resetJogadores();
      }

      this.jogadores.splice(indice, 1);

      localStorage.setItem("jogadores", this.jogadores);
    },

    resetJogadores() {
      this.jogadores = [];
      localStorage.setItem("jogadores", this.jogadores);
    },

    getNewJogador() {
      if (!this.currentJogador) {
        this.currentIndex = Math.floor(
          Math.random() * this.currentJogadores.length
        );
        this.currentJogador = this.currentJogadores[this.currentIndex];
      } else if (this.currentJogadores.length === 1) {
        this.currentJogadores = this.jogadores.slice();
        this.currentIndex = Math.floor(
          Math.random() * this.currentJogadores.length
        );
        this.currentJogador = this.currentJogadores[this.currentIndex];
      } else {
        this.currentJogadores.splice(this.currentIndex, 1);
        this.currentIndex = Math.floor(
          Math.random() * this.currentJogadores.length
        );
        this.currentJogador = this.currentJogadores[this.currentIndex];
      }
    },
    getNewDare() {
      if (!this.currentDare) {
        this.currentDareIndex = Math.floor(
          Math.random() * this.currentDares.length
        );
        this.currentDare = this.currentDares[this.currentDareIndex];
      } else if (this.currentDares.length === 1) {
        this.currentDares = this.dares.slice();
        this.currentDareIndex = Math.floor(
          Math.random() * this.currentDares.length
        );
        this.currentDare = this.currentDares[this.currentDareIndex];
      } else {
        this.currentDares.splice(this.currentDareIndex, 1);
        this.currentDareIndex = Math.floor(
          Math.random() * this.currentDares.length
        );
        this.currentDare = this.currentDares[this.currentDareIndex];
      }
      this.currentChallenge = `, ${this.currentDare}`;
      this.currentStep = "questionMenu";
    },
    getNewTruth() {
      if (!this.currentTruth) {
        this.currentTruthIndex = Math.floor(
          Math.random() * this.currentTruths.length
        );
        this.currentTruth = this.currentTruths[this.currentTruthIndex];
      } else if (this.currentTruths.length === 1) {
        this.currentTruths = this.truths.slice();
        this.currentTruthIndex = Math.floor(
          Math.random() * this.currentTruths.length
        );
        this.currentTruth = this.currentTruths[this.currentTruthIndex];
      } else {
        this.currentTruths.splice(this.currentTruthIndex, 1);
        this.currentTruthIndex = Math.floor(
          Math.random() * this.currentTruths.length
        );
        this.currentTruth = this.currentTruths[this.currentTruthIndex];
      }

      this.currentChallenge = `, ${this.currentTruth}`;
      this.currentStep = "questionMenu";
    },
    proceedToNextChallenge() {
      (this.currentStep = "challengeMenu"), this.getNewJogador();
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
};
</script>

<style scoped lang="scss">
.container {
  margin: 0;
  min-height: 100vh;
  background-color: #ffffff;
  color: black;
  display: flex;
  align-items: center;
  justify-content: center;

  .card-container {
    width: auto;
    min-width: 400px;
    height: 700px;
    max-width: 900px;
    padding: 20px 30px;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.4);
    box-shadow: 0 10px 10px 10px rgba(0, 0, 0, 0.2);
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    h1 {
      font-size: 40px;
      font-weight: 400;
      text-align: center;
    }

    ul {
      li {
        display: flex;
        justify-content: space-between;
        align-items: center;
        border: 1px solid grey;
        padding: 10px 15px;
      }
    }

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

    .fa-arrow-left {
      position: absolute;
      top: 28px;
      left: 20px;
      font-size: 20px;

      &:hover {
        cursor: pointer;
      }
    }

    .fa-times-circle {
      color: rgb(165, 19, 19);

      &:hover {
        cursor: pointer;
      }
    }

    .input-container {
      display: flex;
      text-align: left;
      flex-direction: column;

      .input-container-inside {
        width: 100%;
        display: flex;
        align-items: flex-end;

        i {
          color: blue;
          width: 5%;
          font-size: 25px;
          margin-left: 10px;

          &:hover {
            cursor: pointer;
          }
        }
      }

      label {
        font-weight: bold;
      }

      input {
        width: 95%;
        padding: 10px 10px 0px 5px;
        border: none;
        border-bottom: 1px solid black;
        font-size: 20px;

        &:active {
          border: none;
          outline: none;
        }
        &:focus {
          outline: none;
          border-bottom: 1px solid black;
          font-size: 20px;
        }
      }

      .input-container-buttons {
        margin-top: 20px;
        button {
          outline: none;
          border: 1px solid black;
          padding: 15px 25px;
          border-radius: 25px;
          width: 200px;

          &:hover {
            cursor: pointer;
          }

          &:first-child {
            margin-right: 20px;
          }
        }
      }
    }
  }

  .challenges-container {
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    flex-direction: column;
    width: 1200px;
    height: 800px;
    border: 2px solid grey;

    h1 {
      max-width: 70%;
      text-align: center;
    }

    .challenges-container-cards {
      display: flex;
      justify-content: space-evenly;
      align-items: center;
    }
  }

  .question-container {
    min-width: 400px;
    height: 400px;
    max-width: 900px;
    padding: 20px 30px;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.4);
    box-shadow: 0 10px 10px 10px rgba(0, 0, 0, 0.2);
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    h1 {
      font-size: 40px;
      font-weight: 400;
      text-align: center;
    }

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
}
</style>
