<template>
  <div :style="container">
    <v-stage ref="stage" :config="stage" @mousedown="handleStageMouseDown" @touchstart="handleStageMouseDown">
      <v-layer ref="layer">
        <template v-for="item in items">
          <v-rect v-if="item.type == 'rect'" :key="item.id" :config="item" @transformend="handleTransformEnd"/>
          <v-image v-if="item.type == 'image'" :key="item.id" :config="item" @transformend="handleTransformEnd"/>
          <v-text v-if="item.type == 'text'" :key="item.id" :config="item" @transformend="handleTransformEnd"/>
        </template>
        
        <v-transformer ref="transformer" />
      </v-layer>
    </v-stage>
  </div>
</template>

<script>


export default {
  data() {
    return {
      selectedShapeName: ''
    };
  },
  computed: {
    container() {
      return `border: 1px solid; width:${this.stage.width}px; height: ${this.stage.height}px; margin-right:100px`
    }
  },
  props: ['items', 'stage'],
  methods: {
    handleTransformEnd(e) {
      // shape is transformed, let us save new attrs back to the node
      // find element in our state
      let item = this.items.find(r => r.name === this.selectedShapeName);

      if(item){
        // update the state
        item.x = e.target.x();
        item.y = e.target.y();
        item.rotation = e.target.rotation();
        item.scaleX = e.target.scaleX();
        item.scaleY = e.target.scaleY();
        return
      }
      
    },
    handleStageMouseDown(e) {
      // clicked on stage - clear selection
      if (e.target === e.target.getStage()) {
        this.selectedShapeName = '';
        this.updateTransformer();
        return;
      }

      // clicked on transformer - do nothing
      const clickedOnTransformer =
        e.target.getParent().className === 'Transformer';
      if (clickedOnTransformer) {
        return;
      }

      // find clicked rect by its name
      const name = e.target.name();
      const item = this.items.find(r => r.name === name);
      if (item) {
        this.selectedShapeName = name;
      } else {
        this.selectedShapeName = '';
      }
      this.updateTransformer();
    },
    updateTransformer() {
      // here we need to manually attach or detach Transformer node
      const transformerNode = this.$refs.transformer.getNode();
      const stage = transformerNode.getStage();
      const { selectedShapeName } = this;

      const selectedNode = stage.findOne('.' + selectedShapeName);
      // do nothing if selected node is already attached
      if (selectedNode === transformerNode.node()) {
        return;
      }

      if (selectedNode) {
        // attach to another node
        transformerNode.attachTo(selectedNode);
      } else {
        // remove transformer
        transformerNode.detach();
      }
      transformerNode.getLayer().batchDraw();
    }
  },
  created() {
    this.items.forEach(v => {
      if(v.type == 'image'){
        let image = new window.Image();
        image.origin = 'anonymous';
        image.crossOrigin = 'anonymous';
        image.src = v.src;
        image.onload = () => {
          v.image = image;
        }
      }
    });
  }
};
</script>