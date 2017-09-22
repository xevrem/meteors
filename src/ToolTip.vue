<template lang="html">
  <div class="tool-tip">
    <p>name: {{data.properties?data.properties.name:''}}</p>
    <p>mass: {{data.properties?data.properties.mass:''}}</p>
    <p></p>
  </div>
</template>

<script>
export default {
  name:'ToolTip',
  props:['data'],
}
</script>

<style lang="sass">
  .tool-tip
    display: none
    position: absolute
    background-color: #fff

  .tool-tip p
    margin: 0
    padding: 0

</style>
