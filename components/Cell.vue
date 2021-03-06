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
              :class="{ center: isCenter(i, index) }"
              :style="computedStyle(i, index)"
              @click="() => handleOnCellClick(i, index)"
            >
              <template v-if="isCenter(i, index)">
                {{ model }}
              </template>
              <template v-else>
                <v-textarea
                  v-show="focus === `${i}:${index}`"
                  :ref="`${i}:${index}`"
                  v-model="row[i][index]"
                  :rows="2"
                  solo
                  @blur="() => handleOnBlur(i, index)"
                />
                <div v-show="focus !== `${i}:${index}`">
                  {{ row[i][index] }}
                </div>
              </template>
            </td>
          </tr>
        </table>
      </div>
    </v-flex>
  </v-layout>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator'

@Component
export default class Cell extends Vue {
  @Prop({ default: '#cccccc' }) private centerColor!: string
  @Prop({ default: '' }) private model!: string

  private focus: string | null = null
  private row: string[][] = [
    new Array(3).fill(' '),
    new Array(3).fill(' '),
    new Array(3).fill(' ')
  ]

  private handleOnCellClick(i: number, index: number) {
    if (this.isCenter(i, index)) {
      return
    }
    const cell = `${i}:${index}`
    this.focus = cell
    // @ts-ignore
    this.$nextTick(() => this.$refs[cell][0].focus())
  }

  private handleOnBlur() {
    this.focus = null
  }

  private get isCenter() {
    return (i: number, index: number) => `${i}:${index}` === '1:1'
  }

  private get computedStyle() {
    return (i: number, index: number) => {
      // in case of centerColor
      if (!this.isCenter(i, index)) {
        return {}
      } else {
        return {
          backgroundColor: this.centerColor
        }
      }
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
}
.resize {
  width: 250px;
  height: 250px;
  overflow: auto;
  /* resize: both; */
  /* font-size: 16px; */
  /* padding: 16px; */
}
.open {
  position: absolute;
  top: 0;
  right: 0;
}
</style>
