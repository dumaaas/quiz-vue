<template>
  <div>
    <p>{{currentQuestion}} / {{questionLength}}</p>
  </div>
</template>

<script>
export default {
  name: "QuestionCounter",
  props: {
    currentQuestion: Number,
    questionLength: Number
  },
};
</script>

<style lang="scss">

</style>
