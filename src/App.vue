<template>
  <div id="app" :class="{ dayMode: !dayNight }">
    <h1>{{ checkTitle() }}</h1>
    <select
      class="classic"
      v-if="checkTitle() === 'New player'"
      v-model="quizData"
      placeholder="Odaberite opciju"
    >
      <option value="" disabled selected hidden>--Select quiz--</option>

      <option v-for="q in quizes" :key="q" :value="q.data">{{q.name}}</option>
    </select>
    <div class="quiz">
      <Questions
        :data="quizData"
        @checkQuizActive="checkQuizActive"
        @checkShowHighscore="checkShowHighscore"
        @checkNewPlayer="checkNewPlayer"
        @checkNewPlayerUsername="checkNewPlayerUsername"
      />
      <AddQuestion @addQuestion="addQuestionToData" />
      <DayNightToggler @dayNightToggler="dayNightToggler" />
      <HighScoreTable
        :highscore="highscore"
        :showHighscore="showHighscore"
        @checkShowHighscore="checkShowHighscore"
      />
    </div>
  </div>
</template>

<script>
import Questions from "./components/Questions.vue";
import AddQuestion from "./components/AddQuestion.vue";
import highscore from "./json/highscore.json";
import vueQuizData from "./helpers/quizData.js";
import sportQuizData from "./helpers/sportQuizData.js";
import DayNightToggler from "./components/DayNightToggler.vue";
import HighScoreTable from "./components/HighScoreTable.vue";

export default {
  name: "app",
  components: {
    Questions,
    AddQuestion,
    DayNightToggler,
    HighScoreTable,
  },
  data() {
    return {
      quizData: null,
      highscore: highscore,
      dayNight: true,
      isNewPlayer: true,
      isQuizActive: true,
      showHighscore: false,
      newPlayerUsername: "",
      quizes: [{name: 'Vue Quiz', data: vueQuizData}, {name: 'Sport Quiz', data: sportQuizData}]
    };
  },
  methods: {
    checkTitle() {
      if (this.isNewPlayer) {
        return "New player";
      }
      if (this.isQuizActive) {
        return "Vue Quiz";
      }
      return "Result";
    },
    addQuestionToData(value) {
      this.data.push(value);
    },
    dayNightToggler(value) {
      this.dayNight = value;
    },
    checkQuizActive(value) {
      this.isQuizActive = value;
    },
    checkNewPlayer(value) {
      this.isNewPlayer = value;
    },
    checkShowHighscore(value) {
      this.showHighscore = value;
      // when showing highscore, sort results
      if (value) {
        this.highscore.sort((a, b) => b.correct - a.correct || a.time - b.time);
      }
    },
    checkNewPlayerUsername(value) {
      this.newPlayerUsername = value;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #42d392;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  height: 100vh;
  background: #1a1a1a;
  width: 100%;
  &.dayMode {
    background: #42d392;
    color: #1a1a1a;
  }
}
.quiz {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  border: 1px solid #42d392;
  border-radius: 0 50px 0 50px;
  padding: 50px;
}
.dayMode {
  .quiz {
    border: 1px solid #1a1a1a;
  }
  h1 {
    color: #1a1a1a;
  }
  select {
    /* styling */
    background-color: #42d392;
    border: thin solid #191919;
    color: #191919;
  }

  /* arrows */

  select.classic {
    background-image: linear-gradient(45deg, transparent 50%, #42d392 50%),
      linear-gradient(135deg, #42d392 50%, transparent 50%),
      linear-gradient(to right, #191919, #191919);
  }
}
body {
  min-height: 100vh;
  margin: 0;
  transition: all 0.3s ease-in-out;
}
h1 {
  color: #42d392;
  font-size: 60px;
  line-height: 60px;
}
select {
  /* styling */
  background-color: #191919;
  border: thin solid #42d392;
  border-radius: 4px;
  display: inline-block;
  font: inherit;
  line-height: 1.5em;
  color: #42d392;
  padding: 0.5em 3.5em 0.5em 1em;
  margin-bottom: 20px !important;
  /* reset */

  margin: 0;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  -webkit-appearance: none;
  -moz-appearance: none;
}

/* arrows */

select.classic {
  background-image: linear-gradient(45deg, transparent 50%, #191919 50%),
    linear-gradient(135deg, #191919 50%, transparent 50%),
    linear-gradient(to right, #42d392, #42d392);
  background-position: calc(100% - 20px) calc(1em + 2px),
    calc(100% - 15px) calc(1em + 2px), 100% 0;
  background-size: 5px 5px, 5px 5px, 2.5em 2.5em;
  background-repeat: no-repeat;
}

select.classic:focus {
  background-image: linear-gradient(45deg, white 50%, transparent 50%),
    linear-gradient(135deg, transparent 50%, white 50%),
    linear-gradient(to right, gray, gray);
  background-position: calc(100% - 15px) 1em, calc(100% - 20px) 1em, 100% 0;
  background-size: 5px 5px, 5px 5px, 2.5em 2.5em;
  background-repeat: no-repeat;
  border-color: grey;
  outline: 0;
}

select:-moz-focusring {
  color: transparent;
  text-shadow: 0 0 0 #000;
}
</style>
