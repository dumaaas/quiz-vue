<template>
  <div>
    <div v-if="isQuizActive">
      <div class="quiz-header">
        <QuestionCounter
          :currentQuestion="currentQuestion"
          :questionLength="data.length"
        />
        <Timer :timer="timer" />
      </div>

      <h2>{{ data[activeQuestion].question }}</h2>
      <p
        class="answers"
        :style="{
          cursor: disableQuestion ? 'not-allowed' : 'pointer',
          pointerEvents: disableQuestion ? 'none' : 'auto',
        }"
        v-for="(answer, index) in data[activeQuestion].answers"
        :key="answer"
        @click="checkAnswer(index, answer, $event)"
      >
        {{ answer }}
      </p>

      <button
        :class="{ disabled: !disableQuestion ? 'disabled' : '' }"
        :style="{ pointerEvents: !disableQuestion ? 'none' : 'auto' }"
        class="nextBtn"
        @click="nextQuestion()"
      >
        Sledece pitanje
      </button>
    </div>
    <div v-else>
      <p>Thanks for your game!</p>
      <p>Correct answers: {{ correctAnswers }}</p>
      <p>Wrong answers: {{ wrongAnswers }}</p>
      <p>
        {{ this.msgForWrong() }}
      </p>
      <button @click="anotherGame()" class="nextBtn">New game</button>
    </div>
  </div>
</template>

<script>
import QuestionCounter from "./QuestionCounter.vue";
import Timer from "./Timer.vue";

export default {
  name: "Questions",
  props: {
    data: Array,
  },
  components: {
    QuestionCounter,
    Timer,
  },

  data() {
    return {
      isQuizActive: true,
      currentQuestion: 1,
      activeQuestion: 0,
      answeredQuestion: [],
      disableQuestion: false,
      correctAnswers: 0,
      wrongAnswers: 0,
      timer: 60,
    };
  },
  mounted() {
    this.activeQuestion = this.randomQuestion();
    this.answeredQuestion.push(this.activeQuestion);
    this.timerCounting();
  },
  watch: {
    timer: {
      handler(value) {
        if (value <= 0) {
          this.isQuizActive = false;
        }
      },
      immediate: true, 
    },
  },
  methods: {
    timerCounting() {
      setInterval(() => {
        this.timer--;
      }, 1000);
    },
    msgForWrong() {
      var msg = "";
      switch (this.correctAnswers) {
        case 0:
          msg = "Oh, no luck :(";
          break;
        case 1:
          msg = "Keep going!";
          break;
        case 2:
          msg = "You are almost there!";
          break;
        case 3:
          msg = "Great job, man!";
          break;
        default:
          msg = "You are Vue.js master!";
          break;
      }
      return msg;
    },
    randomQuestion() {
      var maxIndex = this.data.length;
      var questionIndex = Math.floor(Math.random() * maxIndex);
      return questionIndex;
    },
    anotherGame() {
      this.isQuizActive = true;
      this.answeredQuestion = [];
      this.activeQuestion = 0;
      this.correctAnswers = 0;
      this.wrongAnswers = 0;
      this.currentQuestion = 1;
      this.timer = 60;
      this.disableQuestion = false;
      this.activeQuestion = this.randomQuestion();
      this.answeredQuestion.push(this.activeQuestion);
    },
    nextQuestion() {
      if (this.answeredQuestion.length != this.data.length) {
        this.answeredQuestion.push(this.activeQuestion);
        if (this.activeQuestion == this.data.length - 1) {
          this.activeQuestion = 0;
        } else {
          this.activeQuestion++;
        }
        var answersClass = document.getElementsByClassName("answers");
        this.data.forEach((el, index) => {
          if (answersClass[index]) {
            answersClass[index].classList.remove("correct");
            answersClass[index].classList.remove("wrong");
          }
        });
        this.currentQuestion++;
        this.disableQuestion = false;
      } else {
        this.isQuizActive = false;
      }
    },
    checkAnswer(index, answer, event) {
      var correctAnswer = this.data[this.activeQuestion].correctAnswer;

      if (answer === correctAnswer) {
        event.target.classList.add("correct");
        this.correctAnswers++;
      } else {
        event.target.classList.add("wrong");
        this.wrongAnswers++;
        const answersClass = document.getElementsByClassName("answers");
        var answers = this.data[this.activeQuestion].answers;
        const index = answers.findIndex((answer) => answer === correctAnswer);
        answersClass[index].classList.add("correct");
      }

      this.disableQuestion = true;
    },
  },
};
</script>

<style lang="scss">
.quiz-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.nextBtn {
  background: #42d392;
  border: 1px solid #42d392;
  padding: 16px;
  border-radius: 8px;
  margin-top: 35px;
  color: #1a1a1a;
  font-weight: 500;
  transition: all 0.3s ease-out;
  cursor: pointer;
  &.disabled {
    background: rgba(66, 211, 146, 0.3);
    color: #42d392;
  }
  &:hover {
    background: transparent;
    color: #42d392;
    transition: all 0.3s ease-in;
  }
}

.answers {
  border: 1px solid #42d392;
  border-radius: 25px 0 25px 0;
  padding: 18px;
  max-width: 500px;
  margin: 0 auto;
  margin-top: 15px;
  transition: all 0.3s ease-out;
  position: relative;
  &:before,
  &:after {
    content: "";
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    background-repeat: no-repeat;
    opacity: 0.5;
    mix-blend-mode: color-dodge;
    transition: all 0.33s ease;
  }

  &:before {
    background-position: 50% 50%;
    background-size: 300% 300%;
    background-image: linear-gradient(
      115deg,
      transparent 0%,
      var(--color1) 25%,
      transparent 47%,
      transparent 53%,
      var(--color2) 75%,
      transparent 100%
    );
    opacity: 0.5;
    filter: brightness(0.5) contrast(1);
    z-index: 1;
  }

  &:after {
    opacity: 1;
    background-image: url("https://assets.codepen.io/13471/sparkles.gif"),
      url(https://assets.codepen.io/13471/holo.png),
      linear-gradient(
        125deg,
        #ff008450 15%,
        #fca40040 30%,
        #ffff0030 40%,
        #00ff8a20 60%,
        #00cfff40 70%,
        #cc4cfa50 85%
      );
    background-position: 50% 50%;
    background-size: 160%;
    background-blend-mode: overlay;
    z-index: 2;
    filter: brightness(1.5) contrast(1);
    transition: all 0.33s ease;
    mix-blend-mode: color-dodge;
    opacity: 0.75;
  }
  &:hover {
    box-shadow: -20px -20px 30px -25px #1a1a1a, 20px 20px 30px -25px #42d392,
      -7px -7px 10px -5px #1a1a1a, 7px 7px 10px -5px #42d392,
      0 0 13px 4px rgba(255, 255, 255, 0.3),
      0 55px 35px -20px rgba(0, 0, 0, 0.5);
  }

  &.correct {
    background: #42d392;
    border: 1px solid #1a1a1a;
    color: #1a1a1a;
    transition: all 0.3s ease-in;
  }
  &.wrong {
    background: #ca0b00;
    border: 1px solid #1a1a1a;
    color: #1a1a1a;
    transition: all 0.3s ease-in;
  }
}
</style>
