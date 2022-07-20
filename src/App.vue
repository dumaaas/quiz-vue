<template>
  <div id="app" :class="{ dayMode: !dayNight }">
    <h1>{{ checkTitle() }}</h1>
    <div class="quiz">
      <Questions :data="data" @checkQuizActive="checkQuizActive" @checkShowHighscore="checkShowHighscore" @checkNewPlayer="checkNewPlayer" @checkNewPlayerUsername="checkNewPlayerUsername"/>
      <AddQuestion @addQuestion="addQuestionToData" />
      <DayNightToggler @dayNightToggler="dayNightToggler" />
      <HighScoreTable :highscore="highscore" :showHighscore="showHighscore" @checkShowHighscore="checkShowHighscore"/>
    </div>
  </div>
</template>

<script>
import Questions from "./components/Questions.vue";
import AddQuestion from "./components/AddQuestion.vue";
import highscore from "./json/highscore.json";
import quizData from "./helpers/quizData.js";
import DayNightToggler from "./components/DayNightToggler.vue";
import HighScoreTable from "./components/HighScoreTable.vue";

export default {
  name: "app",
  components: {
    Questions,
    AddQuestion,
    DayNightToggler,
    HighScoreTable
  },
  data() {
    return {
      data: quizData,
      highscore: highscore,
      dayNight: true,
      isNewPlayer: true,
      isQuizActive: true,
      showHighscore: false,
      newPlayerUsername: '',
    };
  },
  methods: {
    checkTitle() {
      if(this.isNewPlayer) {
        return 'New player';
      }
      if(this.isQuizActive) {
        return 'Vue Quiz';
      }
      return 'Result';
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
      if(value) {
        this.highscore.sort((a, b) => b.correct - a.correct)
      }
    },
    checkNewPlayerUsername(value) {
      this.newPlayerUsername = value;
    }
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
</style>
