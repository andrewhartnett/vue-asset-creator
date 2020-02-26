<template>
  <div id="app">
    <div style="padding-bottom:30px;">
      <h3>Canvas</h3>
      <label>Height</label>
      <input type="text" v-model="stage.height">
      <label>Width</label>
      <input type="text" v-model="stage.width">

      <button @click.prevent="print">Generate Download</button>
      <a v-if="pdfDownload" :href="pdfDownload" class="button" id="btn-download" download="generated.png">Download</a>
      <button @click="logJson">Log JSON</button>
    </div>
    <div style="display: flex; justify-content: space-between">
      <div>
        <h3>Assets</h3>
        <div v-for="(item, index) in assets" :key="item.id">
            <label>{{item.type}}</label>
            <template v-if="item.type == 'text'">
              <input type="text" v-model="item.text">
              <input type="color" v-model="item.fill">
            </template>
            <template v-if="item.type == 'image'">
              {{item.name}}
            </template>
            <template v-if="item.type == 'rect'">
              <input type="color" v-model="item.fill">
            </template>
            <button @click.prevent="removeItem(index)">X</button>
            <button v-if="index > 0" @click.prevent="moveUp(index)">&uarr;</button>
            <button v-if="index < assets.length-1" @click.prevent="moveDown(index)">&darr;</button>
        </div>
          <button @click.prevent="addText()">Add Text</button>
          <button @click.prevent="addRect()">Add Rect</button>
          <input ref="newImageUrl" type="text" placeholder="http://">
          <button @click.prevent="addImage()">Add Image</button>
          
      </div>
      <asset :stage="stage" :items="assets"></asset>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-unused-vars */
import Asset from './components/Asset'
import jsPDF from 'jspdf';
import toDataURL from 'canvas-background';

export default {
  name: 'App',
  components: {
    Asset
  },
  methods: {
    moveUp(index) {
      const item = this.assets.splice(index, 1);
      this.assets.splice(index -1, 0, item[0]);
    },
    moveDown(index) {
      const item = this.assets.splice(index, 1);
      this.assets.splice(index + 1, 0, item[0]);
    },
    addText(){
      var ts = Math.round((new Date()).getTime() / 1000);
      let name = 'text'+ts;

      this.assets.push(
        {
          rotation: 0,
          x: 10,
          y: 10,
          text: "New Text Field",
          fontSize: 12,
          fill: '#000000',
          draggable: true,
          name: name,
          type: 'text'
      });
    },
    addImage() {
      let target = this.$refs.newImageUrl;
      let image = new window.Image();
      image.origin = 'anonymous';
      image.crossOrigin = 'anonymous';
      image.src = target.value;
      image.onload = () => {
        var ts = Math.round((new Date()).getTime() / 1000);
        let name = 'image'+ts;
        this.assets.push({
              rotation: 0,
              x: 20,
              y: 20,
              width: 100,
              height: 100,
              scaleX: 1,
              scaleY: 1,
              src: target.value,
              image: image,
              name: name,
              draggable: true,
              type: 'image'
            });

          target.value = '';
      }

    },
    removeItem(index) {
      this.assets.splice(index, 1);
    },
    addRect(index) {
      var ts = Math.round((new Date()).getTime() / 1000);
      let name = 'text'+ts;

      this.assets.push(
        {
          rotation: 0,
          width: 100,
          height: 100,
          x: 10,
          y: 10,
          fill: '#cccccc',
          draggable: true,
          name: name,
          type: 'rect'
      });
    },
    print() {
      let canvas = document.querySelector('.konvajs-content canvas');
      const data = toDataURL(canvas, '#ffffff', {
        type: 'image/png',
        encoderOptions: 1.0
      });
      this.pdfDownload = data;
      // const height = Math.floor(this.stage.height * 0.264583)
      // const width = Math.floor(this.stage.width * 0.264583)
      // console.log(height, width);
      // var pdf = new jsPDF('l', 'mm', [this.stage.height, this.stage.width]);
      // var pdf = new jsPDF('l', 'mm', [height, width]);

      

      // pdf.addImage(canvas.toDataURL(),"jpeg", 0, 0)
      // let queryString = pdf.output("datauristring");
      // this.pdfDownload = queryString;
      // let preview = this.$refs.imgPreview;
      // preview.setAttribute('src', canvas.toDataURL());
      // console.log(preview);
      // let img = this.$refs.imagePreview;
      // img.src = queryString;
      // img.height = height+'mm';
      // img.width = width+'mm';

    },
    logJson() {
      console.log(JSON.stringify(this.assets));
    }
  },
  data() {
    return {
      stage: {
        width: 500,
        height: 500
      },
      pdfDownload: null,
      jsonToLoad: '',
      assets: [
            {
              rotation: 0,
              x: 150,
              y: 150,
              width: 150,
              height: 150,
              scaleX: 1,
              scaleY: 1,
              src: 'https://images.adsttc.com/media/images/5de3/1ca6/3312/fda8/2a00/00b3/newsletter/001.jpg?1575165037',
              image: null,
              name: 'image1',
              draggable: true,
              type: 'image'
            },
            {
              rotation: 0,
              x: 50,
              y: 50,
              text: "Welcome COREcreate",
              fontSize: 25,
              fill: '#000000',
              draggable: true,
              name: 'text1',
              type: 'text'
            },
            {
              rotation: 0,
              x: 80,
              y: 80,
              width: 150,
              height: 150,
              scaleX: 1,
              scaleY: 1,
              name: 'rect1',
              fill: '#cccccc',
              draggable: true,
              type: 'rect'
            }
          ]
    }
  }

}
</script>

<style>
html {
  background: #fff !important;
}
#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */
}
</style>
