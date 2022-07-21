<template>
  <div>
    <div v-if="!isQuizActive && newPlayer" class="newPlayer">
      <input
        placeholder="Enter username"
        type="text"
        v-model="newPlayerUsername"
      />
      <p v-if="usernameValidation">{{ usernameValidation }}</p>
      <button @click="startQuiz()" class="nextBtn">Start quiz</button>
    </div>
    <div v-else-if="isQuizActive && !newPlayer">
      <div class="quiz-header">
        <QuestionCounter
          :currentQuestion="currentQuestion"
          :maxQuestionLength="4"
        />
        <Timer :timer="timer" />
      </div>

      <h2>{{ data.data[activeQuestion].question }}</h2>
      <p
        class="answers"
        :style="{
          cursor: disableQuestion ? 'not-allowed' : 'pointer',
          pointerEvents: disableQuestion ? 'none' : 'auto',
        }"
        v-for="(answer, index) in data.data[activeQuestion].answers"
        :key="answer"
        @click="checkAnswer(index, answer, $event)"
      >
        {{ answer }}
      </p>

      <button
        :class="{ disabled: !disableQuestion }"
        :style="{ pointerEvents: !disableQuestion ? 'none' : 'auto' }"
        class="nextBtn"
        @click="nextQuestion()"
      >
        Next question
      </button>
    </div>
    <div v-else>
      <p class="game-msg">{{ this.msgForWrong() }}</p>
      <div class="results-block">
        <table>
          <thead>
            <tr>
              <th>Correct</th>
              <th>Wrong</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>
                {{ correctAnswers }}
              </td>
              <td>
                {{ wrongAnswers }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="button-footer">
        <button
          class="nextBtn"
          @click="
            $emit('checkShowHighscore', true);
            $emit('checkShouldWrapper', true);
          "
        >
          Show highscore
        </button>
        <button @click="anotherGame(true, false)" class="nextBtn">
          New game
        </button>
        <button class="nextBtn" @click="anotherGame(false, true)">
          Change username/quiz
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import QuestionCounter from "./QuestionCounter.vue";
import Timer from "./Timer.vue";
import highscore from "./../json/highscore.json";

export default {
  name: "Questions",
  props: {
    data: [],
  },
  components: {
    QuestionCounter,
    Timer,
  },

  data() {
    return {
      isQuizActive: false,
      newPlayer: true,
      currentQuestion: 1,
      activeQuestion: 0,
      answeredQuestion: [],
      disableQuestion: false,
      correctAnswers: 0,
      wrongAnswers: 0,
      timer: 60,
      timeUsed: 0,
      newPlayerUsername: "",
      highscore: highscore,
      usernameValidation: "",
    };
  },
  watch: {
    timer: {
      handler(value) {
        if (value <= 0) {
          this.isQuizActive = false;
          this.$emit("checkQuizActive", this.isQuizActive);
          this.$emit("checkNewPlayer", this.newPlayer);
        }
      },
      immediate: true,
    },
    newPlayerUsername() {
      this.usernameValidation = "";
    },
  },

  methods: {
    startQuiz() {
      if (this.data === "disabled") {
        this.$emit("checkQuizValidation", true);
        return;
      }
      if (!this.newPlayerUsername) {
        this.usernameValidation = "Username can not be empty";
        return;
      }
      if (this.checkIfUsernameExist() || this.checkIfUsernamePlayedQuiz()) {
        this.isQuizActive = true;
        this.newPlayer = false;
        this.$emit("checkQuizActive", this.isQuizActive);
        this.$emit("checkNewPlayer", this.newPlayer);
        this.$emit("checkNewPlayerUsername", this.newPlayerUsername);
        if (!this.answeredQuestion.length) {
          this.activeQuestion = this.randomQuestion();
          this.answeredQuestion.push(this.activeQuestion);
        }
        setInterval(() => this.timerCounting(), 1000);
      } else {
        this.usernameValidation =
          "This username already exist! Please try another one.";
      }
    },
    checkIfUsernameExist() {
      let tryFind = this.highscore.find(
        (username) => username.name === this.newPlayerUsername
      );
      if (tryFind) {
        return false;
      }
      return true;
    },
    checkIfUsernamePlayedQuiz() {
      let tryFindUser = this.highscore.find(
        (username) => username.name === this.newPlayerUsername
      );
      if (tryFindUser) {
        let tryFindQuizForUser = tryFindUser.quizesDone.find(
          (quiz) => quiz.title === this.data.title
        );
        if (tryFindQuizForUser) return false;
      }
      return true;
    },
    timerCounting() {
      this.timer--;
      this.timeUsed++;
    },
    msgForWrong() {
      var msg = "";
      switch (this.correctAnswers) {
        case 0:
          msg = this.newPlayerUsername + ", oh, you need to learn more! :(";
          break;
        case 1:
          msg = this.newPlayerUsername + ", keep going!";
          break;
        case 2:
          msg = this.newPlayerUsername + ", you are almost there!";
          break;
        case 3:
          msg = this.newPlayerUsername + ", great job, man!";
          break;
        default:
          msg = this.newPlayerUsername + ", you are Vue.js master!";
          break;
      }
      return msg;
    },
    randomQuestion() {
      var maxIndex = this.data.data.length;
      var questionIndex = Math.floor(Math.random() * maxIndex);
      return questionIndex;
    },
    anotherGame(shouldQuizBeActive, shouldNewPlayer) {
      this.answeredQuestion = [];
      this.activeQuestion = 0;
      this.correctAnswers = 0;
      this.wrongAnswers = 0;
      this.currentQuestion = 1;
      clearInterval(this.timerCounting());
      this.timer = 60;
      this.timeUsed = 0;
      this.disableQuestion = false;
      this.activeQuestion = this.randomQuestion();
      this.answeredQuestion.push(this.activeQuestion);
      this.isQuizActive = shouldQuizBeActive;
      this.$emit("checkQuizActive", this.isQuizActive);
      this.newPlayer = shouldNewPlayer;
      this.$emit("checkNewPlayer", this.newPlayer);
    },
    nextQuestion() {
      if (this.answeredQuestion.length != 4) {
        this.answeredQuestion.push(this.activeQuestion);
        if (this.activeQuestion == this.data.data.length - 1) {
          this.activeQuestion = 0;
        } else {
          this.activeQuestion++;
        }
        var answersClass = document.getElementsByClassName("answers");
        this.data.data.forEach((el, index) => {
          if (answersClass[index]) {
            answersClass[index].classList.remove("correct");
            answersClass[index].classList.remove("wrong");
          }
        });
        this.currentQuestion++;
        this.disableQuestion = false;
      } else {
        // add user to highscore
        let newPlayerObj = {
          name: this.newPlayerUsername,
          quizesDone: [
            {
              title: this.data.title,
              correct: this.correctAnswers,
              wrong: this.wrongAnswers,
              time: this.timeUsed,
            },
          ],
        };

        var doesPlayerExist = this.highscore.findIndex(
          (player) => player.name == newPlayerObj.name
        );

        if (doesPlayerExist != -1) {
          var doesQuizExist = this.highscore[
            doesPlayerExist
          ].quizesDone.findIndex((el) => el.title == this.data.title);
        }
        if (doesPlayerExist == -1) {
          this.highscore.push(newPlayerObj);
        } else if (
          doesQuizExist != -1 &&
          (this.highscore[doesPlayerExist].quizesDone[doesQuizExist].correct <=
            newPlayerObj.quizesDone[0].correct ||
            (this.highscore[doesPlayerExist].quizesDone[doesQuizExist]
              .correct <= newPlayerObj.quizesDone[0].correct &&
              this.highscore[doesPlayerExist].quizesDone[doesQuizExist].time >=
                newPlayerObj.quizesDone[0].time))
        ) {
          this.highscore[doesPlayerExist].quizesDone[doesQuizExist] =
            newPlayerObj.quizesDone[0];
        } else {
          console.log("ajdeee bree");
          this.highscore[doesPlayerExist].quizesDone.push(
            newPlayerObj.quizesDone[0]
          );
        }

        // set quiz state unactive
        this.isQuizActive = false;
        this.$emit("checkQuizActive", this.isQuizActive);
        this.$emit("checkNewPlayerUsername", this.newPlayerUsername);
      }
    },
    checkAnswer(index, answer, event) {
      var correctAnswer = this.data.data[this.activeQuestion].correctAnswer;

      if (answer === correctAnswer) {
        event.target.classList.add("correct");
        this.correctAnswers++;
      } else {
        event.target.classList.add("wrong");
        this.wrongAnswers++;
        const answersClass = document.getElementsByClassName("answers");
        var answers = this.data.data[this.activeQuestion].answers;
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

.dayMode {
  .answers {
    border: 1px solid #1a1a1a;

    &:hover {
      box-shadow: -20px -20px 30px -25px #42d392, 20px 20px 30px -25px #1a1a1a,
        -7px -7px 10px -5px #42d392, 7px 7px 10px -5px #1a1a1a,
        0 0 13px 4px rgba(255, 255, 255, 0.3),
        0 55px 35px -20px rgba(0, 0, 0, 0.5);
    }

    &.correct {
      background: #1a1a1a;
      border: 1px solid #42d392;
      color: #42d392;
    }
    &.wrong {
      background: #ca0b00;
      border: 1px solid #1a1a1a;
      color: #1a1a1a;
      transition: all 0.3s ease-in;
    }
  }
  .nextBtn {
    background: #1a1a1a;
    border: 1px solid #1a1a1a;
    color: #42d392;
    &.disabled {
      background: rgba(26, 26, 26, 0.3);
      color: #1a1a1a;
    }
    &:hover {
      background: transparent;
      color: #1a1a1a;
    }
  }
  .game-msg {
    background: #1a1a1a;
    color: #42d392;
    border: 1px solid #1a1a1a;
  }

  .results-block {
    background: #1a1a1a;
    color: #42d392;
    border: 1px solid #1a1a1a;
    table {
      th {
        border: 1px solid #42d392;
      }
      td {
        border: 1px solid #42d392;
      }
    }
  }
}

.game-msg {
  padding: 16px;
  background: #42d392;
  color: #1a1a1a;
  border: 1px solid #42d392;
  border-radius: 25px 25px 0 0;
  font-weight: 600;
}

.results-block {
  padding: 16px;
  background: #42d392;
  color: #1a1a1a;
  border: 1px solid #42d392;
  border-radius: 0 0 25px 25px;
  table {
    margin: 0 auto;
    padding: 16px;
    min-width: 310px;
    border-collapse: collapse;

    th {
      border: 1px solid #1a1a1a;
      padding: 8px;
      font-weight: 600;
    }
    td {
      border: 1px solid #1a1a1a;
      padding: 8px;
    }
  }
}

.newPlayer {
  display: flex;

  flex-direction: column;
  input {
    height: 20px;
    background: #42d392;
    border: 1px solid #1a1a1a;
    padding: 14px;
    border-radius: 10px 0 10px 0;
    &::placeholder {
      color: #1a1a1a;
      opacity: 1;
    }
    &:focus {
      outline: none;
    }
  }
  p {
    margin-top: 4px;
    margin-bottom: 0;
    font-size: 14px;
    font-weight: 500;
    text-align: left;
    color: #ca0b00;
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
