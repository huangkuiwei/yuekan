<template>
  <view class="login-page">
    <view class="logo">
      <uni-icons type="left" size="30" @click="toBack" />
    </view>

    <view class="logo-img">
      <wd-img
          width="183rpx"
          height="183rpx"
          radius="13"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/logo.png"
      ></wd-img>
    </view>

    <view class="banner"> </view>

    <view class="container">
      <view class="input-box">
        <text>{{ phone.slice(0, 3) }}****{{ phone.slice(-4) }}</text>
      </view>

      <view class="login" @click="login">一键登录</view>
      <view class="other-login" @click="toBack">其他手机号码登录</view>

      <view class="login-agree" style="padding: 0 32rpx">
        <wd-checkbox checked-color="#448BFF" size="large" v-model="radio"></wd-checkbox>
        <view class="login-agree_text">
          我已阅读并同意
          <view @click="toRouter(`/pages/vipProtocol/index`, `title=${encodeURIComponent('《用户服务及隐私协议》')}`)">《用户协议》及《隐私政策》</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import { toRouter, toSwich } from '../../hooks/utils'

export default {
  name: 'login',

  data() {
    return {
      radio: false,
      phone: '',
      token: '',
    };
  },

  onLoad(options) {
    this.phone = options.phone;
    this.token = options.token;
  },

  onShareAppMessage() {
    return {
      title: '高清电子文档一键转换',
      imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/share.jpg',
      path: '/pages/index/index',
    };
  },

  methods: {
    toRouter,

    toBack() {
      uni.navigateBack();
    },

    /**
     * 登录
     */
    login() {
      if(!this.radio){
        return uni.showToast({
          icon: 'none',
          title: '请选同意服务及隐私协议'
        })
      }

      let token = this.token;
      uni.setStorageSync('toolsToken', token)
      toSwich('/pages/index/index')
    },
  },
};
</script>

<style lang="scss">
page {
  height: 100%;
  background: #F6F7F9;
}
</style>

<style scoped lang="scss">
.login-page {
  height: 100%;
  display: flex;
  flex-direction: column;

  .logo{
    padding: 105rpx 0 128rpx 20rpx;
    display: flex;
    flex-direction: column;
  }

  .logo-img {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 52rpx;
    margin-bottom: 60rpx;
  }

  .banner {
    flex-shrink: 0;
    padding: calc(var(--page-title-height)) 0 0;
  }

  .container {
    flex-grow: 1;
    padding-bottom: 180rpx;
    display: flex;
    flex-direction: column;
    align-items: center;

    .input-box {
      font-weight: 600;
      font-size: 44rpx;
      color: #010101;
      margin-bottom: 60rpx;
    }

    .login {
      width: 478rpx;
      font-weight: 500;
      font-size: 32rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 29rpx;
      border-radius: 50rpx;
      background: #448BFF;
      color: #ffffff;
      height: 100rpx;
      line-height: 100rpx;
    }

    .other-login {
      font-weight: 500;
      font-size: 24rpx;
      color: #666666;
    }
  }
}

.login-agree{
  // position: fixed;
  // bottom: 100rpx;
  // left: 0;
  // right: 0;
  margin-top: 74rpx;
  display: flex;
  align-items: center;
  justify-content: center;

  .login-agree_text{
    color: #999999;
    font-size: 22rpx;
    display: flex;
  }
}
</style>
