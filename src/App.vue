<template>
  <div id="app">
    <div class="flex" style="padding-bottom:30px;">
      <canvas-settings :stage="stage"></canvas-settings>
      <downloader></downloader>
    </div>
    <div class="flex">
      <editor :assets="assets" @assetsChanged="assetsChanged" :images="images"></editor>
      <asset :stage="stage" :items="assets"></asset>
    </div>
  </div>
</template>

<script>
import Asset from './components/Asset'
import Editor from './components/Editor'
import Downloader from './components/Downloader'
import CanvasSettings from './components/CanvasSettings'

export default {
  name: 'App',
  components: {
    Asset,
    Editor,
    Downloader,
    CanvasSettings
  },
  methods: {
    assetsChanged(v) {
      this.assets = v;
    }
  },
  data() {
    return {
      stage: {
        width: 500,
        height: 500
      },
      images: [

      ],
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
              text: "CORE",
              fontSize: 25,
              fill: '#000000',
              draggable: true,
              name: 'text1',
              type: 'text'
            },
            {
              rotation: 0,
              x: 150,
              y: 50,
              text: "CREATE",
              fontSize: 25,
              fill: '#000000',
              draggable: true,
              name: 'text2',
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
  },
  created () {
    const urlParams = new URLSearchParams(window.location.search);
    const images = urlParams.getAll('image');
    this.images = images;
  }

}
</script>

<style>
  html {
    background: #fff !important;
  }
  
  .flex {
    display: flex; 
    justify-content: space-between
  }
</style>
