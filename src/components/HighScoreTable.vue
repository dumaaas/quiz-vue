<template>
  <div class="highscore" v-if="showHighscore">
    <div class="highscore__header">
      <h3>Highscore</h3>
      <span @click="$emit('checkShowHighscore', false)">X</span>
    </div>
    <table>
      <thead>
        <tr>
          <th>Rank</th>
          <th>Player name</th>
          <th>Correct answers</th>
          <th>Wrong answers</th>
          <th>Time</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(u, index) in highscore" :key="index">
          <td>
            {{ index + 1 }}
          </td>
          <td>
            {{ u.name }}
          </td>
          <td>
            {{ u.correct }}
          </td>
          <td>
            {{ u.wrong }}
          </td>
          <td>
            {{ u.time }} s
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    highscore: Array,
    showHighscore: Boolean,
  },
};
</script>

<style lang="scss" scoped>
.highscore {
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
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 16px;
  background: #42d392;
  color: #1a1a1a;
  border: 4px solid #424242;
  border-radius: 25px 0 25px 0;
  table {
    margin: 32px 0 16px 0;
    width: 600px;
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
.dayMode {
  .highscore {
    h1 {
      color: #42d392;
    }

    background: #1a1a1a;
    color: #42d392;
    border: 4px solid #424242;
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
</style>