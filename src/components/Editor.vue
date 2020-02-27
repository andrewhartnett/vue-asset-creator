<template>
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
</template>

<script>
export default {
  props: ['assets'],
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
    addRect() {
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
  }
}
</script>