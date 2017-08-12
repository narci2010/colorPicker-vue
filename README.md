# colorPicker-vue  
一个颜色选择器，vue 组件
# install
npm i colorpicker-vue -S  

# import
import colorPicker from 'colorPicker-vue'  

#register  
components: { colorPiker }

# use  
&lt;colorPiker value="#000000" @change="change"&gt;&lt;/colorPiker&gt;

change(v){  
     console.log(v) // #000000  
}
