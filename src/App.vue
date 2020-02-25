<template>
  <div id="app">
    <div style="padding-bottom:30px;">
      <h3>Canvas</h3>
      <label>Height</label>
      <input type="text" v-model="stage.height">
      <label>Width</label>
      <input type="text" v-model="stage.width">

      <button @click.prevent="print">Generate PDF</button>
      <a v-if="pdfDownload" :href="pdfDownload" class="button" id="btn-download" download="generated.pdf">Download</a>
      <button @click="logJson">Log JSON</button>
    </div>
    <div v-for="(item, index) in assets" :key="item.id" style="display: flex; justify-content: space-between">
      <div>
        <h3>Text</h3>
          <div v-for="(text, textIndex) in item.texts" :key="text.id">
            <label>Text</label>
            <input type="text" v-model="text.text">
            <label>Color</label>
            <input type="color" v-model="text.fill">
            <button @click.prevent="removeItem('texts', index, textIndex)">Remove</button>
          </div>
          <button @click.prevent="addText(index)">Add Text</button>

        <h3>Images</h3>
          <div v-for="(img, imgIndex) in item.images" :key="img.id">
            <label>{{img.name}}</label>
            <button @click.prevent="removeItem('images', index, imgIndex)">Remove</button>
          </div>
          <input ref="newImageUrl" type="text">
          <button @click.prevent="addImage(index)">Add Image</button>

          <h3>Rectangles</h3>
          <div v-for="(rect, rectIndex) in item.rectangles" :key="rect.id">
            <label>Color</label>
            <input type="color" v-model="rect.fill">
            <button @click.prevent="removeItem('rectangles', index, rectIndex)">Remove</button>
          </div>
          <button @click.prevent="addRect(index)">Add Rectangle</button>
      </div>


      <asset :stage="stage" :images="item.images" :texts="item.texts" :rectangles="item.rectangles" @itemChanged="itemChanged"></asset>
    </div>
    <button @click.prevent="addPage">Add Page</button>
    
  </div>
</template>

<script>
import Asset from './components/Asset'
import jsPDF from 'jspdf';
import toDataURL from 'canvas-background';

export default {
  name: 'App',
  components: {
    Asset
  },
  methods: {
    itemChanged(payload) {
      let item = this.assets.images.find(r => r.name === payload.name);

      if(!item) {
        item = this.assets.texts.find(r => r.name === payload.name);
      }

      if(!item) {
        item = this.assets.rectangles.find(r => r.name === payload.name);
      }

      item = payload;
    },
    addText(index){
      var ts = Math.round((new Date()).getTime() / 1000);
      let name = 'text'+ts;

      this.assets[index].texts.push(
        {
          rotation: 0,
          x: 10,
          y: 10,
          text: "New Text Field",
          fontSize: 12,
          fill: '#000000',
          draggable: true,
          name: name
      });
    },
    addImage(index) {
      let target = this.$refs.newImageUrl[index];
      let image = new window.Image();
      image.origin = 'anonymous';
      image.crossOrigin = 'anonymous';
      image.src = target.value;
      image.onload = () => {
        var ts = Math.round((new Date()).getTime() / 1000);
        let name = 'image'+ts;
        this.assets[index].images.push({
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
              draggable: true
            });

          target.value = '';
      }

    },
    removeItem(type, index, itemIndex) {
      this.assets[index][type].splice(itemIndex, 1);
    },
    addRect(index) {
      var ts = Math.round((new Date()).getTime() / 1000);
      let name = 'text'+ts;

      this.assets[index].rectangles.push(
        {
          rotation: 0,
          width: 100,
          height: 100,
          x: 10,
          y: 10,
          fill: '#cccccc',
          draggable: true,
          name: name
      });
    },
    addPage() {
      this.assets.push({
        texts: [],
        images: []
      })
    },
    print() {
      let canvas = document.querySelector('.konvajs-content canvas');

      const dpi = 96;
      const height = (this.stage.height * 25.4) / dpi;
      const width = (this.stage.width * 25.4) / dpi;
      console.log(height, width);
      var pdf = new jsPDF('l', 'mm', [this.stage.height, this.stage.width]);
      // var pdf = new jsPDF('l', 'mm', [height, width]);

      const data = toDataURL(canvas, '#ffffff', {
        type: 'image/png',
        encoderOptions: 1.0
      });

      pdf.addImage(data,"jpeg", 0, 0)
      let queryString = pdf.output("datauristring");
      console.log(queryString);
      this.pdfDownload = queryString;
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
          images: [
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
              draggable: true
            }
          ],
          texts: [
            {
              rotation: 0,
              x: 50,
              y: 50,
              text: "Welcome COREcreate",
              fontSize: 25,
              fill: '#000000',
              draggable: true,
              name: 'text1'
            }
          ],
          rectangles: [
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
              draggable: true
            }
          ]
        }
      ],
      
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
