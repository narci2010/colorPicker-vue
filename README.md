# colorPicker-vue
一个颜色选择器，vue 组件
//1
npm i -S
//2
import colorPicker from 'colorPicker-vue'
//3
components: { colorPiker }

//4
&lt;colorPiker value="#000000" @change="change"&gt;&lt;/colorPiker&gt;
//5
change(v){
  console.log(v) // #000000
}