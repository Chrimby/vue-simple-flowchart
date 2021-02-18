<template>
  <div class="flowchart-node" :style="nodeStyle" 
    @mousedown="handleMousedown"    
    @mouseup="detectClick($event, id)"
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
    v-bind:class="{selected: options.selected === id}"
    >
    <div class="node-port node-input"
       @mousedown="inputMouseDown"
       @mouseup="inputMouseUp">
    </div>
    <div class="node-main">
      <div v-text="$attrs.configuration.name" class="node-type"></div>
    <!--div v-text="label" class="node-label"></div-->
    </div>
    <div class="node-port node-output" 
      @mousedown="outputMouseDown">
    </div>
    <div v-show="show.delete" class="node-delete">&times;</div>
  </div>
 
</template>

<script>
export default {
  name: 'FlowchartNode',
  props: {
    id: {
      type: String,
      default: '61bab7c.d722d55',
      validator(val) {
        return typeof val === 'string'
      }
    },
    x: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },    
    y: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },
    type: {
      type: String,
      default: 'Default'
    },
    label: {
      type: String,
      default: 'input name'
    },
    options: {
      type: Object,
      default() {
        return {
          centerX: 1024,
          scale: 1,
          centerY: 140,
        }
      }
    }
  },
  data() {
    return {
      show: {
        delete: false,
      },
      detectMovement: true,
      mouseX: 1,
      mouseY: 1
    }
  },
  mounted() {
  },
  computed: {
    nodeStyle() {
      return {
        top: this.options.centerY + this.y * this.options.scale + 'px', // remove: this.options.offsetTop + 
        left: this.options.centerX + this.x * this.options.scale + 'px', // remove: this.options.offsetLeft + 
        transform: `scale(${this.options.scale})`,
      }
    }
  },
  methods: {
   
    handleMousedown(e) {
      this.mouseX = e.clientX;
      this.mouseY = e.clientY;
      const target = e.target || e.srcElement;
      // console.log(target);
      if (target.className.indexOf('node-input') < 0 && target.className.indexOf('node-output') < 0) {
        this.$emit('nodeSelected', e);
      }
      e.preventDefault();
    },
    detectClick(e){
      if (this.mouseX === e.clientX && this.mouseY === e.clientY){
        this.detectMovement = false;
        this.$emit('nodeSingleClick', e);
      }
      else {
        this.detectMovement = true;
       }
      e.preventDefault();
    },
    handleMouseOver() {
    
      this.show.delete = true;
    },
    handleMouseLeave() {
      this.show.delete = false;
    },
    outputMouseDown(e) {
      this.$emit('linkingStart')
      e.preventDefault();
    },
    inputMouseDown(e) {
      e.preventDefault();
    },
    inputMouseUp(e) {
      this.$emit('linkingStop')
      e.preventDefault();
    },
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$themeColor: rgb(54,155,199);
$portSize: 12;

.flowchart-node {
  margin: 0;
  width: 150px;
  height: 60px;
  text-align: center;
  color: white;
  position: absolute;
  box-sizing: border-box;
  border: none;
  background: #369bc7;;
  z-index: 1;
  opacity: .9;
  border-radius:8px;
  cursor: move;
  transform-origin: top left;
  .node-main {
    text-align: center;
    .node-type {
      color: white;
      font-size: 13px;
      padding: 6px;
      vertical-align: middle;
      line-height: 50px;
    }
    .node-label {
      font-size: 13px;
    }
  }
  .node-port {
    position: absolute;
    width: #{$portSize}px;
    height: #{$portSize}px;
    transform: translate(-50%);
    border: 1px solid #ccc;
    top: 39%;
    border-radius: 100px;
    background: white;
    &:hover {
      background: $themeColor;
      border: 1px solid $themeColor;
    }
  }
  .node-input {
    left: 0%;
  }
  .node-output {
    bottom: #{-2+$portSize/-2}px;
    left: 100%;
  }
  .node-delete {
    position: absolute;
    right: -6px;
    top: -6px;
    font-size: 12px;
    width: 12px;
    height: 12px;
    color: $themeColor;
    cursor: pointer;
    background: white;
    border: 1px solid $themeColor;
    border-radius: 100px;
    text-align: center;
    &:hover{
      background: $themeColor;
      color: white;
    }
  }
}
.selected {
  box-shadow: 0 0 0 2px #5996c5;
}
</style>
