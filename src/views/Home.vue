<template>
  <section class="container">
    <div class="card-container" v-if="currentStep === 'mainMenu'">
      <h1>Sapatuna</h1>
      <h2>O melhor party game forévora</h2>
      <div class="button-container">
        <button @click="currentStep = 'playerMenu'">Jogar</button>
        <a
          href="https://ko-fi.com/mrvalraven"
          target="_blank"
          rel="noreferrer"
          class="donateLink"
        >
          <button>Donate 💓</button>
        </a>
      </div>
    </div>
    <div class="card-container" v-else-if="currentStep === 'playerMenu'">
      <i
        class="fas fa-arrow-left backArrow"
        @click="currentStep = 'mainMenu'"
      ></i>
      <h1>Jogadores</h1>
      <ul>
        <li v-for="(jogador, index) in jogadores" :key="index">
          <span>{{ jogador.name }}</span>
          <select
            name="orientacaoSexual"
            id="orientacaoSexual"
            @change="updateSexuality($event, index)"
          >
            <option selected value="jogador.sexuality">
              {{ jogador.sexuality }}
            </option>
            <option value="Assexual" v-if="jogador.sexuality !== 'Assexual'">
              Assexual
            </option>
            <option value="Bissexual" v-if="jogador.sexuality !== 'Bissexual'">
              Bissexual
            </option>
            <option
              value="Heterossexual"
              v-if="jogador.sexuality !== 'Heterossexual'"
            >
              Heterossexual
            </option>
            <option
              value="Homossexual"
              v-if="jogador.sexuality !== 'Homossexual'"
            >
              Homossexual
            </option>
            <option value="Lesbica" v-if="jogador.sexuality !== 'Lésbica'">
              Lésbica
            </option>
            <option value="Pansexual" v-if="jogador.sexuality !== 'Panssexual'">
              Pansexual
            </option>
          </select>
          <select
            name="genero"
            id="genero"
            @change="updateGender($event, index)"
          >
            <option selected value="jogador.gender">
              {{ jogador.gender }}
            </option>
            <option value="Homem" v-if="jogador.gender !== 'Homem'">
              Homem
            </option>
            <option value="Mulher" v-if="jogador.gender !== 'Mulher'">
              Mulher
            </option>
            <option value="Non-binary" v-if="jogador.gender !== 'Non-binary'">
              Non-binary
            </option>
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
          <button @click="nextPhaseAfterPlayers">Começar</button>
          <button @click="resetJogadores">Reset jogadores</button>
        </div>
      </div>
    </div>
    <div
      class="card-container cycle-container"
      v-else-if="currentStep === 'cycleMenu'"
    >
      <i
        class="fas fa-arrow-left backArrow"
        @click="currentStep = 'playerMenu'"
      ></i>
      <ul>
        <li v-for="(jogador, index) in jogadores" :key="index">
          {{ jogador.name }}
        </li>
      </ul>
      <button @click="currentStep = 'modosMenu'">Continuar</button>
    </div>
    <div
      class="card-container modos-container"
      v-else-if="currentStep === 'modosMenu'"
    >
      <i
        class="fas fa-arrow-left backArrow"
        @click="currentStep = 'cycleMenu'"
      ></i>
      <h1>Modos de jogo</h1>
      <div class="fun">
        <button @click="setModoDeJogo(0)">Fun</button>
      </div>
      <div class="soft">
        <button @click="setModoDeJogo(1)">Soft</button>
      </div>
      <div class="hot">
        <button @click="setModoDeJogo(2)">Hot</button>
      </div>
      <div class="hard">
        <button @click="setModoDeJogo(3)">Hard</button>
      </div>
    </div>
    <div
      class="challenges-container"
      v-else-if="currentStep === 'challengeMenu'"
    >
      <i
        class="fas fa-arrow-left backArrow"
        @click="currentStep = 'modosMenu'"
      ></i>
      <h1>{{ currentJogador.name }}</h1>
      <div class="challenges-container-cards">
        <TruthCard class="truth-card" @click="getNewTruth" />
        <DareCard class="dare-card" @click="getNewDare" />
      </div>
    </div>
    <div class="question-container" v-else-if="currentStep === 'questionMenu'">
      <h1>{{ currentChallengeType }}</h1>
      <h2>{{ currentJogador.name }}{{ currentChallenge }}</h2>
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
      currentJogador: {
        name: "",
        sexuality: "",
        gender: "",
        compatibleWith: "",
      },
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
      currentModoDeJogo: "",
      currentChallengeType: "",
    };
  },
  created() {
    if (!localStorage.getItem("jogadores")) {
      return;
    } else if (localStorage.getItem("jogadores").length === 0) {
      return;
    }

    console.log(JSON.parse(localStorage.getItem("jogadores")));

    this.jogadores = JSON.parse(localStorage.getItem("jogadores"));
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

      let jogadorBeingAdded = {
        name: this.jogadorBeingAdded.trim(),
        sexuality: "Heterossexual",
        gender: "Homem",
        compatibleWith: "Mulher Non-binary",
      };

      this.jogadores.push(jogadorBeingAdded);
      localStorage.setItem("jogadores", JSON.stringify(this.jogadores));
      console.log(this.jogadores);
      this.jogadorBeingAdded = "";
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
    updateSexuality(event, index) {
      const sexualityValue = event.target.value;
      this.jogadores[index].sexuality = sexualityValue;
      if (sexualityValue === "Lesbica") {
        this.jogadores[index].gender = "Mulher";
      } else if (sexualityValue === "Homossexual") {
        this.jogadores[index].gender = "Homem";
      }
      this.jogadores[index].compatibleWith = this.getSexualCompatibility(
        sexualityValue,
        this.jogadores[index].gender
      );
      localStorage.setItem("jogadores", JSON.stringify(this.jogadores));
    },
    updateGender(event, index) {
      let genderValue = event.target.value;
      this.jogadores[index].gender = genderValue;
      if (this.jogadores[index].sexuality === "Lesbica") {
        this.jogadores[index].gender = "Mulher";
      } else if (this.jogadores[index].sexuality === "Homossexual") {
        this.jogadores[index].gender = "Homem";
      }
      this.jogadores[index].compatibleWith = this.getSexualCompatibility(
        this.jogadores[index].sexuality,
        this.jogadores[index].gender
      );
      localStorage.setItem("jogadores", JSON.stringify(this.jogadores));
    },
    getSexualCompatibility(sexuality, gender) {
      if (sexuality === "Heterossexual" && gender === "Homem") {
        return "Mulher";
      } else if (sexuality === "Heterossexual" && gender === "Mulher") {
        return "Homem";
      } else if (
        sexuality === "Bissexual" ||
        sexuality === "Pansexual" ||
        sexuality === "Assexual"
      ) {
        return "Homem Mulher Non-binary";
      } else if (sexuality === "Homossexual" && gender === "Homem") {
        return "Homem";
      } else if (sexuality === "Lesbica" && gender === "Mulher") {
        return "Mulher";
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
      this.currentChallengeType = "Consequência";
      this.currentChallenge = `, ${this.currentDare}`;
      this.filterQuestion();
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

      this.currentChallengeType = "Verdade";
      this.currentChallenge = `, ${this.currentTruth}`;
      this.filterQuestion();
      this.currentStep = "questionMenu";
    },
    proceedToNextChallenge() {
      (this.currentStep = "challengeMenu"), this.getNewJogador();
    },

    setModoDeJogo(modo) {
      this.dares = Object.values(dares)[modo];
      this.truths = Object.values(truths)[modo];
      this.iniciarJogo();
    },

    getPartner() {
      let index = Math.floor(Math.random() * this.jogadores.length);
      console.log("index ", index);
      return this.jogadores[index];
    },

    filterQuestion() {
      console.log("question: ", this.currentChallenge);

      if (this.currentChallenge.includes("-jogador-")) {
        let partner = this.getPartner();
        let partner2 = this.getPartner();
        let count = 0;
        let count2 = 0;

        while (
          this.currentJogador === partner ||
          !this.currentJogador.compatibleWith.includes(partner.gender) ||
          !partner.compatibleWith.includes(this.currentJogador.gender)
        ) {
          partner = this.getPartner();
          count++;

          if (count === 500) {
            while (this.currentJogador === partner) {
              partner = this.getPartner();
            }
            this.currentChallenge = this.currentChallenge.replaceAll(
              "-jogador-",
              partner.name
            );
            return;
          }
        }

        if (this.currentChallenge.includes("-jogador2-")) {
          while (
            partner2 === partner ||
            partner2 === this.currentJogador ||
            !this.currentJogador.compatibleWith.includes(partner2.gender) ||
            !partner2.compatibleWith.includes(this.currentJogador.gender)
          ) {
            partner2 = this.getPartner();
            count2++;

            if (count2 === 500) {
              while (partner2 === partner || partner2 === this.currentJogador) {
                partner2 = this.getPartner();
              }
              this.currentChallenge = this.currentChallenge.replaceAll(
                "-jogador2-",
                partner2.name
              );
              return;
            }
          }

          this.currentChallenge = this.currentChallenge.replaceAll(
            "-jogador2-",
            partner2.name
          );
        }
        this.currentChallenge = this.currentChallenge.replaceAll(
          "-jogador-",
          partner.name
        );

        if (this.currentChallenge.includes("-currentJogador-")) {
          this.currentChallenge = this.currentChallenge.replaceAll(
            "-currentJogador-",
            this.currentJogador.name
          );
        }
      }
    },

    nextPhaseAfterPlayers() {
      if (this.jogadores.length === 0) return;

      this.currentStep = "cycleMenu";
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

  .donateLink {
    opacity: 1;
  }

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

  .modos-container {
    display: flex;
    justify-content: space-evenly;
    align-items: center;

    button {
      width: 300px;
      padding: 15px 25px;
      outline: none;
      border: 1px solid black;
      border-radius: 20px;
      margin-bottom: 25px;

      &:hover {
        cursor: pointer;
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
    position: relative;

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
    align-items: center;

    h1 {
      font-size: 40px;
      font-weight: 600;
      text-align: center;
    }

    h2 {
      max-width: 70%;
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

  .backArrow {
    position: absolute;
    top: 28px;
    left: 20px;
    font-size: 20px;

    &:hover {
      cursor: pointer;
    }
  }
}

@media (max-width: 600px) {
  .container {
    .card-container {
      width: 100%;
      min-height: 100vh;
      height: auto;
      box-shadow: 0 0 0 0;
      padding: 30px;

      h2 {
        text-align: center;
      }
    }

    .challenges-container {
      width: 100%;
      min-height: 100vh;
      height: auto;
      box-shadow: 0 0 0 0;
      position: relative;

      h1 {
        font-size: 30px;
        position: absolute;
        min-width: 1200px;
        top: 43%;
        background-color: white;
        padding: 20px;
        transform: rotate(-45deg);
        z-index: 4;
      }

      .challenges-container-cards {
        display: block;
        height: 100vh;
        width: 100%;

        .truth-card {
          position: absolute;
          z-index: 2;
          transform: rotate(45deg);
          right: 100px;
          top: -30%;
        }

        .dare-card {
          position: absolute;
          z-index: 3;
          top: 30%;
          left: 30%;
          transform: rotate(45deg);
        }
      }
    }

    .question-container {
      box-shadow: none;
      min-width: 100%;
      max-width: 100%;
      height: 100vh;
      padding: 50px 30px;

      h2 {
        max-width: 100%;
      }
    }
  }

  .container .backArrow {
    left: 40px;
    top: 25px;
    z-index: 50;
  }
  .container .challenges-container .backArrow {
    left: 20px;
    top: 20px;
    z-index: 50;
    padding: 10px 12px;
    background: white;
    border-radius: 50%;
  }
}
</style>
