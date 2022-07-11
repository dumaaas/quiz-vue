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

export default {
  name: "app",
  components: {
    Questions,
    AddQuestion,
  },
  data() {
    return {
      quizTitle: "Vue Quiz",
      data: [
        {
          question: "What are the advantages of using Vue.js?",
          answers: [
            "Small in size — The size is 18 to 21KB. It requires no time for the user to download and apply it.",
            "Easily understandable — One of the reasons for the popularity of this framework is that it is quite easy to understand. Users can easily add Vue.js to their web project due to its simple structure.",
            "Virtual DOM — It is capable of using the virtual DOM like other existing frameworks such as ReactJS, Ember, and so on. Virtual DOM is considered a lightweight in-memory tree representation of the original HTML DOM and updated without impacting the origina",
          ],
          correctAnswer:
            "Virtual DOM — It is capable of using the virtual DOM like other existing frameworks such as ReactJS, Ember, and so on. Virtual DOM is considered a lightweight in-memory tree representation of the original HTML DOM and updated without impacting the origina",
        },
        {
          question:
            "If you use .passive and .prevent together, .prevent will be ignored. Probably, it will show you a warning.",
          answers: ["True", "False"],
          correctAnswer: "True",
        },
        {
          question: "Which of the directives below provide a two-way binding?",
          answers: ["v-inline", "v-model", "v-on"],
          correctAnswer: "v-model",
        },
        {
          question:
            "Which of the following directives is to used for attaching event listeners that invoke methods?",
          answers: ["v-for", "v-on", "v-model", "v-bind"],
          correctAnswer: "v-on",
        },
      ],
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
  padding: 0 16px;
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
