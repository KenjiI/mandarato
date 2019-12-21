<template>
  <v-layout column justify-center>
    <div style="display: flex">
      <div class="sidearea">
        <v-subheader class="pl-0">拡大・縮小</v-subheader>
        <v-slider
          v-model="slider"
          thumb-label="always"
          :max="150"
          :min="75"
        ></v-slider>
        <v-subheader class="pl-0">フォントサイズ</v-subheader>
        <v-slider
          v-model="font"
          thumb-label="always"
          :max="125"
          :min="10"
        ></v-slider>
        <div>
          <div class="section">マスの大きさ</div>
          <v-radio-group v-model="type" :mandatory="false">
            <v-radio label="3 x 3 マス" :value="0"></v-radio>
            <v-radio label="9 x 9 マス" :value="1"></v-radio>
          </v-radio-group>
          <v-btn color="primary" @click="handleOnSavaImage"
            >ダウンロード!</v-btn
          >
        </div>
      </div>
      <v-flex style="padding: 16px 0 0 32px;">
        <div
          ref="mandara"
          class="resize absolute"
          :style="{ zoom: slider + '%', fontSize: font + '%' }"
        >
          <table v-if="type === 1">
            <tr>
              <td v-for="i in [0, 1, 2]" :key="`0:${i}`">
                <cell
                  :model="values[0][i]"
                  :center-color="bColors[0][i]"
                ></cell>
              </td>
            </tr>
            <tr>
              <td>
                <cell
                  :model="values[1][0]"
                  :center-color="bColors[1][0]"
                ></cell>
              </td>
              <td>
                <center-cell
                  v-model="values"
                  :background-colors="bColors"
                ></center-cell>
              </td>
              <td>
                <cell
                  :model="values[1][2]"
                  :center-color="bColors[1][2]"
                ></cell>
              </td>
            </tr>
            <tr>
              <td v-for="i in [0, 1, 2]" :key="`2:${i}`">
                <cell
                  :model="values[2][i]"
                  :center-color="bColors[2][i]"
                ></cell>
              </td>
            </tr>
          </table>

          <div v-else style="background-color: white">
            <center-cell
              v-model="values"
              :background-colors="[
                ['', '', ''],
                ['', '#cccccc', ''],
                ['', '', '']
              ]"
              class="simple-cell"
            ></center-cell>
          </div>
        </div>
      </v-flex>
    </div>
  </v-layout>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import html2canvas from 'html2canvas'
import Moveable from 'moveable'
import Cell from '@/components/Cell.vue'
import CenterCell from '@/components/CenterCell.vue'

@Component({
  components: { Cell, CenterCell }
})
export default class Mandarato extends Vue {
  // 3x3 or 9x9
  private type = 1
  // default zoom
  private slider = 100
  // font-size
  private font = 100
  // current focus
  private focus: string | null = null
  // backgroundcolors
  private bColors = [
    ['#ACB9E0', '#BDB3E1', '#E7A396'],
    ['#9ABEE0', '#CCCCCC', '#EDC097'],
    ['#ACCEBC', '#BCDC98', '#F1DC98']
  ]

  // store only cetervalued for each table
  private values = [
    new Array(3).fill(' '),
    new Array(3).fill(' '),
    new Array(3).fill(' ')
  ]

  private mounted() {
    const mandara: any = this.$refs.mandara

    const moveable = new Moveable(document.body, {
      target: mandara,
      container: document.body,
      draggable: true,
      resizable: false,
      scalable: false,
      rotatable: false,
      pinchable: true,
      origin: false,
      throttleDrag: 0
    })

    moveable.on('drag', ({ target, left, top }) => {
      target!.style.left = `${left}px`
      target!.style.top = `${top}px`
    })
  }

  private handleOnCellClick(i: number, index: number) {
    const cell = `${i}:${index}`
    this.focus = cell
    // @ts-ignore
    this.$nextTick(() => this.$refs[cell][0].focus())
  }

  private handleOnSavaImage() {
    // @ts-ignore
    html2canvas(this.$refs.mandara, { scale: 2 }).then((canvas: any) => {
      const a = document.createElement('a')
      a.download = `your_mandarato.png`
      a.href = canvas.toDataURL('image/png')
      a.click()
    })
  }
}
</script>

<style lang="css" scoped>
.sidearea {
  min-width: 200px;
  height: calc(100vh - 64px);
  border-right: 1px solid #ddd;
}
table {
  border-collapse: collapse;
  border-spacing: 0;
  border-top: 5px solid !important;
  border-bottom: 5px solid !important;
  border-left: 5px solid !important;
  background-color: white;
}
tr {
  border-bottom: 5px solid !important;
}
td {
  border-right: 5px solid !important;
}
.resize {
  overflow: auto;
}
.absolute {
  position: absolute;
}
.simple-cell {
  border: 5px solid !important;
}
.section {
  font-size: 0.875rem;
  color: rgba(0, 0, 0, 0.6);
}
</style>

<style type="css">
.moveable-line {
  background: none !important;
}
</style>
