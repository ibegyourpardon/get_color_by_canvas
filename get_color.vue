<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" ref="logo" id="logo">
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
  import HelloWorld from './components/HelloWorld.vue'
  import html2canvas from 'html2canvas'


  export default {
    name: 'App',
    components: {
      HelloWorld
    },
    data() {
      return {
        canvas: ''
      }
    },
    methods: {
      xxx() {
        this.real(this.$refs.logo.src)
      },
      real(param) {
        console.log(param)
        let querySelector = document.getElementById('logo');
        let h = querySelector.clientHeight;
        let w = querySelector.clientWidth;
        console.log(h)
        console.log(12345)
        let canvas = document.createElement("canvas");
        let scale = 1;
        canvas.width = w * scale;
        canvas.height = h * scale;
        html2canvas(this.$refs.logo, {
          scale: scale,
          dpi: window.devicePixelRatio * scale,
          canvas: canvas,
          width: canvas.width,
          height: canvas.height
        }).then(canvas => {
          console.log(canvas) // 这一句尼玛奇了怪了，不加不行，加了canvas就有用，不加 canvas 就是width为0
          let context = canvas.getContext("2d");
          context.imageSmoothingEnabled = false;
          context.webkitImageSmoothingEnabled = false;
          context.msImageSmoothingEnabled = false;
          context.imageSmoothingEnabled = false;

          let imgData = context.getImageData(0, 0, canvas.width, canvas.height)
          let pixelData = imgData.data;
          let colorList = {}
          let rgba = []
          let rgbaSt = ''

          // 分组循环
          for (let i = 0; i < pixelData.length; i += 4) {
            rgba[0] = pixelData[i];
            rgba[1] = pixelData[i + 1];
            rgba[2] = pixelData[i + 2];
            // 如果主色只要r,g,b不要a,请注释掉这句
            rgba[3] = pixelData[i + 3];

            // 避免undefined数据和alpha为0的数据
            if (rgba.indexOf(undefined) !== -1 || pixelData[i + 3] === 0) {
              continue;
            }
            // 转为字符串
            rgbaSt = rgba.join(',');

            if (rgbaSt in colorList) {
              ++colorList[rgbaSt];
            } else {
              colorList[rgbaSt] = 1;
            }
          }
          ////

          let arr = [];

          for (let prop in colorList) {
            arr.push({
              // 如果只获取rgb,则为`rgb(${prop})`
              color: `rgba(${prop})`,
              count: colorList[prop]
            });
          }

          // 数组排序
          arr.sort((a, b) => {
            return b.count - a.count;
          });

          console.log(arr)


        })
      }
    },
    mounted() {
      this.xxx()
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>