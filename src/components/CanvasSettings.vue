<template>
  <div>
      <label>Height</label>
      <input type="text" v-model="stage.height">
      <label>Width</label>
      <input type="text" v-model="stage.width">
    </div>
</template>

<script>
export default {
  props: ['stage']
}
</script>