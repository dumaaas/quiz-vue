<template>
  <div>
    <p>
      Remaining time: <span :class="{ danger: timer <= 10 }">{{ timer }}s</span>
    </p>
  </div>
</template>

<script>
export default {
  name: "Timer",
  props: {
    timer: Number,
  },
};
</script>

<style lang="scss">
.danger {
  color: #ca0b00;
}
</style>
