<template>
  <view class="message-my">
    <wd-message-box selector="wd-message-box-slot">
      <view class="message-content">
        <view class="title">版本升级</view>
        <view style="margin-top: 330rpx; font-size: 32rpx">已是最新版本</view>
      </view>
    </wd-message-box>
  </view>
</template>

<script setup>
import { ref } from 'vue'
import { useMessage } from '/node_modules/wot-design-uni'

const message = useMessage('wd-message-box-slot')
const openMessage = () => {
  message.alert({})
}

const toMsg = (e) => {
  message.close();
}

defineExpose({
  openMessage
})
</script>

<style scoped lang="scss">
.mc-title{
  font-size: 1.1rem;
  color: #222;
  margin-top: 1rem;
}
.message-li{
  margin-top: 0.5rem;
  text-align: left;
  font-size: 0.85rem;
  color: #222;
}
.message-img{
  margin-top: -80px;
}
.m-dot{
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(35, 156, 247, 1);
  margin-right: 0.4rem;
}

.message-content {
  .title {
    position: absolute;
    top: 100rpx;
    left: 60rpx;
    font-weight: 600;
    font-size: 54rpx;
    color: #FFFFFF;
  }
}
</style>