<template>
  <div id="app">
    <div class="container">
      <h1>Historical Recorded Meteor Landings</h1>
      <svg class="chart" height="480" width="854"></svg>
    </div>
    <tool-tip :data="tt_data"/>
  </div>
</template>

<script>
import axios from 'axios';
import * as d3 from 'd3';

import ToolTip from './ToolTip.vue';

export default {
  name: 'app',
  data () {
    return {
      data: null,
      geo: null,
      map: null,
      circles: null,
      mass: null,
      tt_data: {}
    }
  },
  components:{
    'tool-tip': ToolTip
  },
  async mounted(){
    try{
      let response = await axios.get('https://raw.githubusercontent.com/FreeCodeCamp/ProjectReferenceData/master/meteorite-strike-data.json');
      console.log('DATA:', response.data);
      this.data = response.data;

      response = await axios('src/world.json');
      this.geo = response.data;

      this.do_d3();
    }
    catch(error){
      console.error('ERROR:', error);
    }
  },
  methods:{
    do_d3(){
      let data = this.data;

      let chart = d3.select('.chart'),
        width = +chart.attr('width'),
        height = +chart.attr('height');

      let scale = width / (2 * Math.PI)

      let proj = d3.geoMercator()
        .scale(scale)
        .translate([width/2, height/2]);

      let path = d3.geoPath()
        .projection(proj);

      // let url = 'http://enjalot.github.io/wwsd/data/world/world-110m.geojson';

      this.map = chart.append('g')
        .append('path')
        .attr('d', path(this.geo))
        .style('stroke', '#f88')
        .style('fill', '#8f8');

      this.circles = chart.selectAll('.circles')
        .data(data.features)
        .enter()
        .append('g')
        .attr('class','circles');

      let mass = d3.scaleLog()
        .domain([d3.min(data.features,d=>d.properties?d.properties.mass:0),
        d3.max(data.features,d=>d.properties?d.properties.mass:0)])
        .range([0,5.0]);

      this.mass = mass;

      let color = d3.scaleOrdinal(d3.schemeCategory10);

      let tooltip = d3.select('.tool-tip');

      this.circles.append('circle')
        .attr('cx', d => this.coords(proj,d,0))
        .attr('cy', d => this.coords(proj,d,1))
        .attr('r', d => d.properties? mass(d.properties.mass):0)
        .style('fill', d=> color(d.properties?d.properties.mass:0))
        .style('stroke', '#ddd')
        .on('mouseover', d =>{
          tooltip
            .style('display','inline-block')
            .style('left', d3.event.pageX + 'px')
            .style('top', (d3.event.pageY - 80 )+ 'px');

          this.tt_data = d;
        })
        .on('mouseout', d =>{
          tooltip
            .style('display', 'none')

          this.tt_data = {};
        })

      let zoom = d3.zoom()
        .scaleExtent([1, 5])
        .on('zoom', this.do_zoom);

      chart.call(zoom);
    },
    coords(p, d, i){
      return d.geometry? p(d.geometry.coordinates)[i]:0
    },
    do_zoom(){
      let trans = d3.event.transform
      //console.log(trans);
      this.map.attr('transform', trans);
      this.circles.attr('transform', trans);
      this.circles
        .selectAll('circle')
        .attr('r', d => d.properties? this.mass(d.properties.mass)/trans.k:0)
        .style('stroke-width',1/trans.k);
    }
  }
}
</script>

<style lang="sass">
#app
  font-family: 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center

svg
  border: 1px solid black
  background-color: #88f

</style>
