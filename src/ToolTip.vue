<template lang="html">
  <div class="tool-tip">
    <p class="meteor-name">{{data.properties?data.properties.name:''}}</p>
    <p>Mass: {{data.properties?data.properties.mass/1000:''}} kg</p>
    <p>Year: {{data.properties?new Date(data.properties.year).getFullYear():''}}</p>
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
    box-shadow: 0 0 4px rgba(0,0,0,.14), 0 4px 8px rgba(0,0,0,.28)

  .meteor-name
    background-color: #88f
    color: #fff
    box-shadow: 0 4px 8px rgba(0,0,0,.28)
    font-weight: bold

  .tool-tip p
    margin: 0
    padding: 5px 15px 0px 15px

</style>
