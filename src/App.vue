<template>
  <div id="app">
    <h1>{{ quizTitle }}</h1>
    <div class="quiz">
      <Questions :data="data" />
      <AddQuestion @addQuestion="addQuestionToData" />
    </div>
  </div>
</template>

<script>
import Questions from "./components/Questions.vue";
import AddQuestion from "./components/AddQuestion.vue";
// import quizData from "./json/quizData.json";
import quizData from "./helpers/quizData.js"

export default {
  name: "app",
  components: {
    Questions,
    AddQuestion,
  },
  data() {
    return {
      quizTitle: "Vue Quiz",
      data: quizData,
    };
  },
  methods: {
    addQuestionToData(value) {
      this.data.push(value);
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
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  height: 100vh;
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
body {
  min-height: 100vh;
  margin: 0;
  background: #1a1a1a;
}
h1 {
  color: #42d392;
  font-size: 60px;
  line-height: 60px;
}
</style>
