<template>
  <v-layout column justify-center>
    <div style="display: flex">
      <div class="sidearea">
        <v-subheader class="pl-0">拡大・縮小</v-subheader>
        <v-slider
          v-model="slider"
          thumb-label="always"
          max="125"
          min="75"
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
      <v-flex>
        <div
          ref="mandara"
          class="resize absolute"
          :style="{ zoom: slider + '%' }"
        >
          <table v-if="type === 1">
            <tr>
              <td><cell></cell></td>
              <td><cell></cell></td>
              <td><cell></cell></td>
            </tr>
            <tr>
              <td><cell></cell></td>
              <td><cell></cell></td>
              <td><cell></cell></td>
            </tr>
            <tr>
              <td><cell></cell></td>
              <td><cell></cell></td>
              <td><cell></cell></td>
            </tr>
          </table>

          <div v-else style="background-color: white">
            <cell class="simple-cell"></cell>
          </div>
        </div>
      </v-flex>
    </div>
  </v-layout>
</template>

<script>
import html2canvas from 'html2canvas'
import Cell from '@/components/Cell.vue'

export default {
  components: { Cell },
  data() {
    return {
      type: 0,
      slider: 100,
      focus: 0,
      row: [
        new Array(3).fill('★'),
        new Array(3).fill('★'),
        new Array(3).fill('★')
      ],
      isDragging: false,
      start: {
        x: 0,
        y: 0
      },
      diff: {
        x: 0,
        y: 0
      },
      end: {
        x: 0,
        y: 0
      }
    }
  },
  mounted() {
    const mandara = this.$refs.mandara
    const { start, end, diff } = this
    mandara.addEventListener('mousedown', (event) => {
      console.log('mousedown')
      this.isDragging = true
      start.x = event.clientX
      start.y = event.clientY
    })
    mandara.addEventListener('mousemove', (event) => {
      console.log('mousemove')
      if (this.isDragging) {
        diff.x = event.clientX - start.x + end.x
        diff.y = event.clientY - start.y + end.y
        mandara.style.left = diff.x + 'px'
        mandara.style.top = diff.y + 'px'
      }
    })
    mandara.addEventListener('mouseup', (_) => {
      console.log('mouseup')
      this.isDragging = false
      end.x = diff.x
      end.y = diff.y
    })
    mandara.addEventListener('mouseleave', (_) => {
      console.log('mouseleave')
      this.isDragging = false
      end.x = diff.x
      end.y = diff.y
    })

    const clientHeight = document.documentElement.clientHeight
    const clientWidth = document.documentElement.clientWidth
    start.y = clientHeight / 2 - mandara.offsetHeight / 2
    start.x = clientWidth / 2 - mandara.offsetWidth / 2
    end.y = start.y
    end.x = start.x
    mandara.style.top = start.y + 'px'
    mandara.style.left = start.x + 'px'
  },
  methods: {
    handleOnCellClick(i, index) {
      const cell = `${i}:${index}`
      this.focus = cell
      console.log(this)
      this.$nextTick(() => this.$refs[cell][0].focus())
    },
    handleOnSavaImage() {
      html2canvas(this.$refs.mandara, { scale: 2 }).then((canvas) => {
        const a = document.createElement('a')
        console.log(canvas)
        a.download = `test.png`
        a.href = canvas.toDataURL('image/png')
        a.click()
      })
    }
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
  /* resize: both; */
}
.absolute {
  position: absolute;
  left: 0;
  top: 0;
}
.simple-cell {
  border: 5px solid !important;
}
.section {
  font-size: 0.875rem;
  color: rgba(0, 0, 0, 0.6);
}
</style>
