<template>
  <div>
        <h3>Assets</h3>
        <draggable v-model="computedAssets" @start="drag=true" @end="drag=false">
          <div v-for="(item, index) in assets" :key="item.id" class="flex" style="padding: 10px 5px; margin:10px 0; border: 1px dashed #ccc">
          <div style="width: 70%">
            <template v-if="item.type == 'text'">
              <input type="text" v-model="item.text" style="display:block; margin-bottom: 5px;">
              <input type="color" v-model="item.fill" style="display:block;">
            </template>
            <template v-if="item.type == 'image'">
              <i class="fa fa-image"></i>
            </template>
            <template v-if="item.type == 'rect'">
              <input type="color" v-model="item.fill">
            </template>
          </div>
          <div>
            <button @click.prevent="removeItem(index)">X</button>
          </div>
        </div>
        </draggable>

        <div>
          <button @click.prevent="addText()">Add Text</button>
          <button @click.prevent="addRect()">Add Rect</button>
        </div>
          
        <div>
          <button @click.prevent="showImageAdd = true">Add Image</button>
          <div v-if="showImageAdd">
            <h4>Select an Image</h4>
              <div class="imgContainer" v-for="image in images" :key="image.id" @click="newImageUrl = image">
                <img :src="image" crossorigin="anonymous" origin="anonymous" alt="">
              </div>
            <h4>Or Enter Url</h4>
            <input ref="newImageUrl" type="text" placeholder="http://" v-model="newImageUrl">
            <button v-if="newImageUrl" @click="addImage">Add</button>
          </div>
          
        </div>

      </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  data() {
    return {
      computedAssets: [],
      showImageAdd: true,
      selectedableImages: [],
      newImageUrl: ''
    }
  },
  props: ['assets', 'images'],
  components: {
    draggable
  },
  methods: {
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
  },
  created() {
    this.computedAssets = this.assets;
  },
  watch: {
    computedAssets(v) {
      this.$emit('assetsChanged', v);
    }
  }
  
}
</script>

<style scoped>

.imgContainer {
  height: 100px;
  width: 100px;
  border:1px solid black;
}

.imgContainer > img {
  height:100%;
  width:100%;
}

</style>>
