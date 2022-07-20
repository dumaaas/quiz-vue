<template>
  <div>
    <p>{{currentQuestion}} / {{maxQuestionLength}}</p>
  </div>
</template>

<script>
export default {
  name: "QuestionCounter",
  props: {
    currentQuestion: Number,
    maxQuestionLength: Number
  },
};
</script>

<style lang="scss">

</style>
