/*!
* create by chuchur /chuchur@qq.com
* date 2017年6月07日 19:45:37
* author ：chuchur
*/

<template>
  <div class="m-colorPicker" ref="colorPicker" @click="event => { event.stopPropagation() }">
    <!-- 颜色显示小方块 -->
    <div class="colorBtn" ref="colorBtn" v-show="showView" :style="{backgroundColor:showColor}" @click="show = !show"></div>
    <!-- 用以激活HTML5颜色面板 -->
    <input type="color" ref="html5Color" v-model="html5Color" @change="updataValue(html5Color)">
    <!-- 颜色色盘 -->
    <div class="box" :class="{ open: show }" ref="colorBox">
      <div class="hd">
        <div class="colorView" :style="{ backgroundColor:showPanelColor }"></div>
        <div class="defaultColor" @click="handleDefaultColor" @mouseover="hoveColor = defaultColor" @mouseout="hoveColor = null">默认颜色</div>
      </div>
      <div class="bd">
        <h3>主题颜色</h3>
        <ul class="tColor">
          <li v-for="(color,i) of tColor" :key="i" :style="{ backgroundColor: color }" @mouseover="hoveColor = color" @mouseout="hoveColor = null" @click="updataValue(color)"></li>
        </ul>
        <ul class="bColor">
          <li v-for="(item,i) of colorPanel" :key="i">
            <ul>
              <li v-for="(color,i) of item" :key="i" :style="{ backgroundColor: color }" @mouseover="hoveColor = color" @mouseout="hoveColor = null" @click="updataValue(color)"></li>
            </ul>
          </li>
        </ul>
        <h3>标准颜色</h3>
        <ul class="tColor">
          <li v-for="(color,i) of bColor" :key="i" :style="{ backgroundColor: color }" @mouseover="hoveColor = color" @mouseout="hoveColor = null" @click="updataValue(color)"></li>
        </ul>
        <h3 @click="triggerHtml5Color">更多颜色...</h3>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'colorPicker',
  props: {
    // 默认展示面板
    isShow:{
      default: false,
      type: Boolean
    },
    showView: {
      default: true,
      type: Boolean
    },
    // 当前颜色值
    value: {
      type: String,
      default: '#000000',
      required: false
    },
    // 默认颜色
    defaultColor: {
      type: String,
      default: '#000'
    },
    // 禁用状态
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      // 面板打开状态
      show: this.isShow==true,
      // 鼠标经过的颜色块
      hoveColor: null,
      // 主题颜色
      tColor: ['#000', '#fff', '#eeece1', '#1e497b', '#4e81bb', '#e2534d', '#9aba60', '#8165a0', '#47acc5', '#f9974c'],
      // 颜色面板
      colorConfig: [
        ['#7f7f7f', '#f2f2f2'],
        ['#0d0d0d', '#808080'],
        ['#1c1a10', '#ddd8c3'],
        ['#0e243d', '#c6d9f0'],
        ['#233f5e', '#dae5f0'],
        ['#632623', '#f2dbdb'],
        ['#4d602c', '#eaf1de'],
        ['#3f3150', '#e6e0ec'],
        ['#1e5867', '#d9eef3'],
        ['#99490f', '#fee9da']
      ],
      // 标准颜色
      bColor: ['#c21401', '#ff1e02', '#ffc12a', '#ffff3a', '#90cf5b', '#00af57', '#00afee', '#0071be', '#00215f', '#72349d'],
      html5Color: this.value
    }
  },
  computed: {
    // 显示面板颜色
    showPanelColor() {
      return this.hoveColor ? this.hoveColor : this.showColor
    },
    // 显示颜色
    showColor() {
      return this.value ? this.value : this.defaultColor
    },
    // 颜色面板
    colorPanel() {
      let colorArr = []
      for (let color of this.colorConfig) {
        colorArr.push(this.gradient(color[1], color[0], 5))
      }
      return colorArr
    }
  },
  methods: {
    getElementPos(element) {
      var pos = { x: 0, y: 0 }
      pos.x = element.offsetLeft;
      pos.y = element.offsetTop;
      var current = element.offsetParent;
      while (current !== null) {
        pos.x += current.offsetLeft;
        pos.y += current.offsetTop;
        current = current.offsetParent;
      }
      return pos;
    },
    triggerHtml5Color() {
      this.$refs.html5Color.click()
    },
    // 更新组件的值 value
    updataValue(value) {
      this.$emit('input', value)
      this.$emit('change', value)
      this.show = false
    },
    // 设置默认颜色
    handleDefaultColor() {
      this.updataValue(this.defaultColor)
    },
    // 格式化 hex 颜色值
    parseColor(hexStr) {
      if (hexStr.length === 4) {
        hexStr = '#' + hexStr[1] + hexStr[1] + hexStr[2] + hexStr[2] + hexStr[3] + hexStr[3]
      } else {
        return hexStr
      }
    },
    // RGB 颜色 转 HEX 颜色
    rgbToHex(r, g, b) {
      let hex = ((r << 16) | (g << 8) | b).toString(16)
      return '#' + new Array(Math.abs(hex.length - 7)).join('0') + hex
    },
    // HEX 转 RGB 颜色
    hexToRgb(hex) {
      hex = this.parseColor(hex)
      let rgb = []
      for (let i = 1; i < 7; i += 2) {
        rgb.push(parseInt('0x' + hex.slice(i, i + 2)))
      }
      return rgb
    },
    // 计算渐变过渡颜色
    gradient(startColor, endColor, step) {
      // 讲 hex 转换为 rgb
      let sColor = this.hexToRgb(startColor)
      let eColor = this.hexToRgb(endColor)
      // 计算R\G\B每一步的差值
      let rStep = (eColor[0] - sColor[0]) / step
      let gStep = (eColor[1] - sColor[1]) / step
      let bStep = (eColor[2] - sColor[2]) / step
      let gradientColorArr = []
      // 计算每一步的hex值
      for (let i = 0; i < step; i++) {
        gradientColorArr.push(this.rgbToHex(parseInt(rStep * i + sColor[0]), parseInt(gStep * i + sColor[1]), parseInt(bStep * i + sColor[2])))
      }
      return gradientColorArr
    }
  },
  created() {
    this.show = this.isShow
    document.addEventListener('click', () => {
      //  document.onclick = (e) => {
      this.show = false
      // }
    })
  },
  // mounted() {
  //   // 点击页面上其他地方，关闭弹窗
  //   document.onclick = (e) => {
  //     this.show = false
  //   }

  // },

  updated() {

    //获取元素的位置
    // console.log(this.$refs.colorBox.style)
    let obj = this.$refs.colorBtn
    var pos = this.getElementPos(obj)

    // console.log(y, document.body.clientHeight)
    if (pos.x > document.body.clientWidth - 215) {
      this.$refs.colorBox.style.left = '-165px'
    }
    if (pos.y > document.body.clientHeight - 250) {
      this.$refs.colorBox.style.top = '-250px'
    }
  }
}
</script>

<style lang="less" scoped>
@import 'index.less';
</style>