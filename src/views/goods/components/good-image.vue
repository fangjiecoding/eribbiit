<template>
  <div class="goods-image">
    <div v-show="show"
         class="large"
         :style="[{backgroundImage:`url(${images[currIndex]})`},largerPosition]"></div>
    <div class="middle"
         ref="target">
      <img :src="images[currIndex]"
           alt="">
      <div v-show="show"
           class="layer"
           :style="layerPosition "></div>
    </div>
    <ul class="small">
      <li v-for="(img,i) in images"
          :key="img"
          :class="{active:i===currIndex}">
        <img @mouseenter="currIndex=i"
             :src="img"
             alt="">
      </li>
    </ul>
  </div>
</template>
<script>
import { ref, reactive, watch } from 'vue'
import { useMouseInElement } from '@vueuse/core'
export default {
  name: 'GoodsImage',
  props: {
    images: {
      type: Array,
      default: () => []
    }
  },
  setup (props) {
    const currIndex = ref(0)
    const show = ref(false)
    const target = ref(null)
    const { elementX, elementY, isOutside } = useMouseInElement(target)
    const layerPosition = reactive({ left: 0, top: 0 })
    const largerPosition = reactive({ backgroundPositionX: 0, backgroundPositionY: 0 })
    watch([elementX, elementY, isOutside], () => {
      // 控制X轴方向的定位 0-200 之间
      if (elementX.value < 100) layerPosition.left = 0
      else if (elementX.value > 300) layerPosition.left = 200
      else layerPosition.left = elementX.value - 100
      // 控制Y轴方向的定位 0-200 之间
      if (elementY.value < 100) layerPosition.top = 0
      else if (elementY.value > 300) layerPosition.top = 200
      else layerPosition.top = elementY.value - 100
      // 设置大背景的定位
      largerPosition.backgroundPositionX = -layerPosition.left * 2 + 'px'
      largerPosition.backgroundPositionY = -layerPosition.top * 2 + 'px'
      // 设置遮罩容器的定位
      layerPosition.left = layerPosition.left + 'px'
      layerPosition.top = layerPosition.top + 'px'
      // 设置是否显示预览大图
      show.value = !isOutside.value
    })
    return { currIndex, show, target, layerPosition, largerPosition }
  }
}

</script>
<style scoped lang="less">
.goods-image {
  width: 480px;
  height: 400px;
  position: relative;
  display: flex;
  z-index: 500;
  .large {
    position: absolute;
    top: 0;
    left: 412px;
    width: 400px;
    height: 400px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    background-repeat: no-repeat;
    background-size: 800px 800px;
    background-color: #f8f8f8;
  }
  .middle {
    width: 400px;
    height: 400px;
    background: #f5f5f5;
    position: relative;
    cursor: move;
    .layer {
      width: 200px;
      height: 200px;
      background: rgba(0, 0, 0, 0.2);
      left: 0;
      top: 0;
      position: absolute;
    }
  }
  .small {
    width: 80px;
    li {
      width: 68px;
      height: 68px;
      margin-left: 12px;
      margin-bottom: 15px;
      cursor: pointer;
      &:hover,
      &.active {
        border: 2px solid @xtxColor;
      }
      &:last-child {
        margin-bottom: 0px;
      }
    }
  }
}
</style>
