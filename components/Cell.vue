<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <div class="resize">
        <table class="mandara-frame">
          <tr v-for="i in [0, 1, 2]" :key="i">
            <td
              v-for="(v, index) in row[i]"
              :key="index"
              class="cell"
              :class="{ center: `${i}:${index}` === '1:1' }"
              @dblclick="() => handleOnCellClick(i, index)"
            >
              <v-textarea
                v-show="focus === `${i}:${index}`"
                :ref="`${i}:${index}`"
                v-model="row[i][index]"
                :rows="1"
                solo
                @blur="() => (focus = null)"
              />
              <div v-show="focus !== `${i}:${index}`">
                {{ row[i][index] }}
              </div>
            </td>
          </tr>
        </table>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      focus: 0,
      row: [
        new Array(3).fill(' '),
        new Array(3).fill(' '),
        new Array(3).fill(' ')
      ]
    }
  },
  methods: {
    handleOnCellClick(i, index) {
      const cell = `${i}:${index}`
      this.focus = cell
      console.log(this)
      this.$nextTick(() => this.$refs[cell][0].focus())
    }
  }
}
</script>

<style lang="css" scoped>
table {
  border-collapse: collapse;
  border-spacing: 0;
}
input {
  white-space: normal;
}
textarea {
  resize: none;
  width: 100%;
  padding: 0 16px;
}
.mandara-frame {
  width: 100%;
  height: 100%;
  border: 1px solid #aaa;
}
.cell {
  text-align: center;
  border: 1px solid #aaa;
  width: 33%;
  position: relative;
}
.center {
  background-color: white;
  background-color: #cccccc;
}
.resize {
  width: 250px;
  height: 250px;
  overflow: auto;
  /* resize: both; */
  border-radius: 5px;
  font-size: 16px;
  /* padding: 16px; */
}
.open {
  position: absolute;
  top: 0;
  right: 0;
}
</style>
