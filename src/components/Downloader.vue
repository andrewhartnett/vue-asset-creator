<template>
  <div>
    <button @click.prevent="print">Generate Download</button>
      <a v-if="pdfDownload" :href="pdfDownload" class="button" id="btn-download" download="generated.png">Download</a>
      <button @click="logJson">Log JSON</button>
  </div>
</template>

<script>
/* eslint-disable no-unused-vars */
import toDataURL from 'canvas-background';
import jsPDF from 'jspdf';

export default {
  data() {
    return {
      pdfDownload: null,
      jsonToLoad: '',
    }
  },
  methods: {
    logJson() {
      console.log(JSON.stringify(this.assets));
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
    }
  }
}
</script>