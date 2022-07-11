<template>
  <div class="addQuestion">
    <div class="addQuestion-wrapper">
      <button @click="newQuestionModal = true">Add question</button>
    </div>
    <div class="addQuestion-modal" v-if="newQuestionModal">
      <div class="addQuestion-modal__header">
        <h3>Add new question</h3>
        <span @click="newQuestionModal = false">X</span>
      </div>
      <div class="addQuestion-modal__body">
        <div class="form-input">
          <label>Question</label>
          <input
            @keydown="questionValidation = ''"
            v-model="questionsObj.question"
            type="text"
          />
          <p>{{ questionValidation }}</p>
        </div>
        <div class="form-input" v-for="n in answersArray" :key="n">
          <label>Answer {{ n }}</label>
          <div class="form-input__flex">
            <input
              @keydown="answersValidation = ''"
              v-model="questionsObj.answers[n - 1]"
              type="text"
            />
            <input
              type="checkbox"
              @change="answersValidation = ''"
              v-model="questionsObj.correctAnswers[n - 1]"
            />
            <span @click="removeAnswerInput(n)">X</span>
          </div>
        </div>
        <div class="form-input__validation">
          <p>{{ answersValidation }}</p>
        </div>
        <div class="newQuestionBtn">
          <button @click="addAnswer()">+</button>
        </div>
      </div>
      <div class="addQuestion-modal__footer">
        <button @click="addNewQuestionToData()">Add question</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AddQuestion",
  components: {},
  data() {
    return {
      newQuestionModal: false,
      answersArray: 1,
      questionValidation: "",
      answersValidation: "",
      questionsObj: {
        question: "",
        answers: [""],
        correctAnswers: [""],
      },
    };
  },

  methods: {
    addAnswer() {
      this.questionsObj.answers.push("");
      this.questionsObj.correctAnswers.push("");
      this.answersArray++;
    },
    removeAnswerInput(n) {
      this.questionsObj.answers.splice(n - 1, 1);
      this.questionsObj.correctAnswers.splice(n - 1, 1);

      this.answersArray--;
    },
    clearQuestionValidation() {
      this.questionValidation = "";
    },
    addNewQuestionToData() {
      var errorFlag = false;
      if (this.questionsObj.question === "") {
        this.questionValidation = "Question can not be empty!";
        errorFlag = true;
      }
      if (this.questionsObj.answers.length <= 1) {
        this.answersValidation = "You have to add at least 2 answers";
        errorFlag = true;
      } else {
        this.questionsObj.answers.forEach((el) => {
          if (el === "") {
            this.answersValidation = "Answers can not be empty";
            errorFlag = true;
          }
        });
        const countOccurrences = (arr, val) =>
          arr.reduce((a, v) => (v === val ? a + 1 : a), 0);
        var trueCounter = countOccurrences(
          this.questionsObj.correctAnswers,
          true
        );
        if (trueCounter !== 1) {
          this.answersValidation = "You have to select one correct answer";
          errorFlag = true;
        }
      }
      if (errorFlag) return;

      const correctAnswerIndex = this.questionsObj.correctAnswers.findIndex(
        (el) => el === true
      );

      var objForData = {
        question: this.questionsObj.question,
        answers: this.questionsObj.answers,
        correctAnswer: this.questionsObj.answers[correctAnswerIndex],
      };

      this.newQuestionModal = false;
      this.$emit("addQuestion", objForData);
    },
  },
};
</script>

<style lang="scss">
.addQuestion-modal {
  position: absolute;
  z-index: 1000;
  transform: translate(50%, -30%);
  top: 50%;
  left: 0%;
  max-width: 400px;
  width: 400px;
  min-height: 400px;
  border: 1px solid #1a1a1a;
  background: #42d392;
  border-radius: 0 35px 0 35px;
  padding: 16px 24px;
  transition: all 0.15s ease-out;
  .newQuestionBtn {
    margin-left: auto;
    button {
      background: #1a1a1a;
      border: 1px solid #42d392;
      color: #42d392;
      font-size: 22px;
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-top: 2px;
      cursor: pointer;
      transition: all 0.15s ease-out;

      border-radius: 50%;
      &:hover {
        border: 1px solid #1a1a1a;
        background: #42d392;
        color: #1a1a1a;
        transition: all 0.15s ease-in;
      }
    }
  }
  &__footer {
    border-top: 1px solid #1a1a1a;
    margin: 0 auto;

    button {
      background: #1a1a1a;
      border: 1px solid #1a1a1a;
      padding: 16px;
      color: #42d392 !important;
      border-radius: 8px;
      margin-top: 20px;
      color: #1a1a1a;
      font-weight: 500;
      transition: all 0.3s ease-out;
      cursor: pointer;
      &:hover {
        background: #42d392;
        color: #1a1a1a !important;
        transition: all 0.3s ease-in;
      }
    }
  }
  &__body {
    display: flex;
    flex-direction: column;
    gap: 20px;
    margin: 20px 0;

    .form-input {
      display: flex;
      flex-direction: column;
      gap: 6px;
      &__flex {
        display: flex;
        gap: 15px;
        align-items: center;
        span {
          background: #1a1a1a;
          border: 1px solid #42d392;
          color: #42d392;
          font-size: 12px;
          width: 25px;
          height: 25px;
          display: flex;
          align-items: center;
          justify-content: center;
          margin-top: 2px;
          cursor: pointer;
          transition: all 0.15s ease-out;

          border-radius: 50%;
          &:hover {
            border: 1px solid #1a1a1a;
            background: #42d392;
            color: #1a1a1a;
            transition: all 0.15s ease-in;
          }
        }
      }
      &__validation {
        p {
          margin-top: 2px;
          font-size: 14px;
          font-weight: 500;
          text-align: left;
          color: #ca0b00;
        }
      }
      input[type="text"] {
        width: 75%;
      }
      input {
        height: 20px;
        background: #42d392;
        border: 1px solid #1a1a1a;
        padding: 14px;
        border-radius: 10px 0 10px 0;
        &:focus {
          outline: none;
        }
      }
      label {
        text-align: left;
        color: #1a1a1a;
      }
      p {
        margin-top: 2px;
        font-size: 14px;
        font-weight: 500;
        text-align: left;
        color: #ca0b00;
      }
    }
  }
  &__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    color: #1a1a1a;
    border-bottom: 1px solid #1a1a1a;

    span {
      cursor: pointer;
      font-weight: 500;
      transition: all 0.15s ease-out;
      &:hover {
        color: rgba(0, 0, 0, 0.4);
        transition: all 0.15s ease-in;
      }
    }
  }
}
.addQuestion-wrapper {
  position: absolute;
  top: 0;
  right: 20px;
  button {
    background: #42d392;
    border: 1px solid #42d392;
    padding: 16px;
    border-radius: 8px;
    margin-top: 20px;
    color: #1a1a1a;
    font-weight: 500;
    transition: all 0.3s ease-out;
    cursor: pointer;
    &:hover {
      background: transparent;
      color: #42d392;
      transition: all 0.3s ease-in;
    }
  }
}
</style>
