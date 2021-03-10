<template>
  <div class="flowchart-node" :style="nodeStyle" 
    @mousedown="handleMousedown"    
    @mouseup="detectClick($event, id)"
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
    v-bind:class="{selected: options.selected === id}"
    >
    <div v-if="$attrs.status != 'ok' " class="status_icon">
      <img :src="'/img/' + $attrs.status +'.png'" width="20px" />
    </div>
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
    <el-popover
      style="margin: 10px 0 0 0;"
      placement="bottom-end"
      :width="width"
      trigger="manual"
      v-model="popover">
      <el-row :gutter="20" type="flex" align="middle">
        <el-col :span="6">
          <el-badge :value="count" class="item">
             <i v-if="state === 'loading'" class=" custom-icon el-icon-loading"></i>
              <i v-if="state === 'ok'" class="custom-icon el-icon-check"></i>
             <i v-if="state === 'warning'" class=" custom-icon el-icon-warning"></i>
          </el-badge>
        </el-col>
        <el-col :span="16">
            <template v-if="content && content.type == 'string'">{{content.value}}</template>
            <template v-if="content && content.type == 'tags'" v-for="val in content.value" :key="val.name">
              <div> <span>{{val.name}}</span>: <span style="float:right; ">{{val.value}}</span></div>
            </template>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <template  v-if="content && content.type == 'table'">
            <el-table :data="content.value" size="mini">
                <el-table-column property="date" label="Date" ></el-table-column>
                <el-table-column property="temp" label="Temp" ></el-table-column>
                <el-table-column property="humidity" label="Humidity"></el-table-column>
                <el-table-column property="status" label="Status" ></el-table-column>
            </el-table>
        </template>
      </el-row>

    </el-popover>
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
    },
    popover: {
      type: Boolean,
      default: false
    },
    state: {
      type: String,
      default: "loading"
    },
    content: {
      type: Object,
      default: {}
    }

  },
  watch: {
    content(newV, oldV){
      this.count++;
      if(newV.type == "table") {
        this.width = 320;
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
      mouseY: 1,
      loading: false,
      count: 0,
      width: 200
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
        "--theme-color": this.$attrs.color || "rgb(54,155,199)"
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
$themeColor: var(--theme-color);
$portSize: 12;

.item {
  margin-top: 10px;
  margin-right: 40px;
}

.custom-icon {
   font-size: 1.5rem;
}

.flowchart-node {
  margin: 0;
  width: 150px;
  height: 60px;
  text-align: center;
  color: white;
  position: absolute;
  box-sizing: border-box;
  border: none;
  background: $themeColor;;
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
  box-shadow: 0 0 0 2px $themeColor;
}

.status_icon {
  top: -13px;
  position: absolute;
  right: 10px;
}
</style>
