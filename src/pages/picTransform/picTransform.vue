<script setup>
import { ref } from 'vue'
import { toRouter } from '@/hooks/utils'

const transformList= ref([
  { name: '图片转PDF', icon: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/picTransform/icon01.png', background: '#F1F8FE' },
  { name: '图片转拼图', icon: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/picTransform/icon02.png', background: '#F0F9F8' },
  { name: '图片转Word', icon: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/picTransform/icon03.png', background: '#FEF9F3' },
  { name: '图片转PPT', icon: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/picTransform/icon04.png', background: '#F2F6FF' },
  { name: '图片转Excel', icon: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/picTransform/icon05.png', background: '#FDF5F3' },
])

const useTool = (index) => {
  if (index === 0) {
    toRouter('/pages/pic-transform/index', 'type=pic&channel=16')
  } else if (index === 1) {
    toRouter('/pages/camera/index?tab=13&from=tool')
  } else if (index === 2) {
    toRouter('/pages/pic-transform/index', 'type=pic&channel=14')
  } else if (index === 3) {
    toRouter('/pages/pic-transform/index', 'type=pic&channel=17')
  } else if (index === 4) {
    toRouter('/pages/pic-transform/index', 'type=pic&channel=15')
  }
}
</script>

<template>
  <view class="pic-transform">
    <view class="transform-list">
      <view class="transform-item" v-for="(item, index) of transformList" :key="item.name" :style="{ background: item.background }" @click="useTool(index)">
        <image mode="widthFix" :src="item.icon" />
        <text>{{ item.name }}</text>
      </view>
    </view>
  </view>
</template>

<style lang="scss">
page {
  height: 100%;
  background: #FAFAFA;
}
</style>

<style scoped lang="scss">
.pic-transform {
  padding: 52rpx 39rpx;

  .transform-list {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    gap: 40rpx;

    .transform-item {
      width: 196rpx;
      height: 215rpx;
      border-radius: 10rpx;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 47rpx;

      image {
        width: 50rpx;
      }

      text {
        font-weight: 500;
        font-size: 24rpx;
        color: #1F1F1F;
      }
    }
  }
}
</style>