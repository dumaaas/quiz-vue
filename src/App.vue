<template>
  <div id="app" :class="{ dayMode: !dayNight }">
    <h1>{{ quizTitle }}</h1>
    <div class="quiz">
      <Questions :data="data" />
      <AddQuestion @addQuestion="addQuestionToData" />
      <DayNightToggler @dayNightToggler="dayNightToggler" />
    </div>
  </div>
</template>

<script>
import Questions from "./components/Questions.vue";
import AddQuestion from "./components/AddQuestion.vue";
// import quizData from "./json/quizData.json";
import quizData from "./helpers/quizData.js";
import DayNightToggler from "./components/DayNightToggler.vue";

export default {
  name: "app",
  components: {
    Questions,
    AddQuestion,
    DayNightToggler,
  },
  data() {
    return {
      quizTitle: "Vue Quiz",
      data: quizData,
      dayNight: true,
    };
  },
  methods: {
    addQuestionToData(value) {
      this.data.push(value);
    },
    dayNightToggler(value) {
      this.dayNight = value;
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
  position: relative;
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
