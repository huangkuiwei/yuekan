<template>
  <view class="page-title">我的</view>

  <view class="banner"></view>

  <view class="my-index">
    <view class="my-nav">
      <view class="user-info">
        <button class="chooseAvatar" open-type="chooseAvatar" @chooseavatar="chooseAvatar" v-if="user.uid">
          <image
              style="height: 110rpx; width: 110rpx; border-radius: 50%"
              :src="user.avatar_url || `https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png`"
              mode="aspectFit"
          />
        </button>

        <image
            v-else
            style="height: 110rpx; width: 110rpx; border-radius: 50%"
            :src="user.avatar_url || `https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png`"
            mode="aspectFit"
        />

        <view class="my-status" style="gap: 10rpx; flex-grow: 1">
          <view style="font-weight: bold; font-size: 30rpx; color: #111111;">
            <view v-if="user.uid" style="display: flex; align-items: center; justify-content: flex-start; position: relative">
              <view style="max-width: 400rpx; overflow: auto; text-overflow: ellipsis; white-space: nowrap">{{ user.nickname || '微信用户' }}</view>
              <!--<image v-if="user.vip_type" style="width: 32rpx; padding-left: 20rpx" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/vip-icon.png"></image>-->
              <input style="position: absolute; left: 0; max-width: 400rpx; overflow: auto; text-overflow: ellipsis" class="username" type="nickname" :value="user.nickname || '微信用户'" @change="nicknameChange">
            </view>

            <view v-else @click="toRouter('/pages/login/index')">
              登录/注册
            </view>
          </view>

          <view style="font-size: 24rpx; color: #999999;" v-if="!user.uid" @click="toRouter('/pages/login/index')">登录/注册</view>
          <view style="font-size: 24rpx; color: #999999;" v-else>
            <text v-if="user.vip_type">会员用户</text>
            <text v-else>普通用户</text>
          </view>
        </view>

        <view class="setting" @click="jump">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/setting.png"></image>
        </view>
      </view>

      <view class="join-vip">
        <view>
          <view class="join-vip_name">开通会员 尽享特权</view>
          <!-- <view v-else class="join-vip_name">畅享所有权益</view> -->

          <!-- <view v-if="!user.vip_type" class="join-vip_tip">加入会员享受更多权益</view> -->
          <!-- <view class="join-vip_tip" v-if="user.vip_type > 0"> -->
          <!--   会员到期日期：{{ user.vip_end_time }} -->
          <!-- </view> -->
        </view>

        <view @click="toMember" class="join-bottom">
          <view v-if="!user.vip_type">立即开通</view>
          <view v-else>我的会员</view>

          <image class="arrow" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/arrow.png"/>
        </view>
      </view>
    </view>

    <view class="global-m_y">
      <view class="menu-box">
        <button class="contact-btn"  @click="openContact" style="padding: 0; border-radius: 0; margin-bottom: 15rpx; padding-bottom: 10rpx">
          <image class="my-cell_img" mode="widthFix" style="position: relative; top: 0" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/menu-icon1.png"></image>
          <text style="font-size: 30rpx; color: #111111;">联系客服</text>
          <view style="color: #ccccccaa;">
            <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon>
          </view>
        </button>

        <wd-cell clickable @click="permissionsSetting" :customStyle="{ marginBottom: '15rpx', paddingBottom: '15rpx' }">
          <template #title>
            <text style="font-size: 30rpx; color: #111111;">权限管理</text>
          </template>
          <template #icon>
            <image class="my-cell_img" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/menu-icon2.png"></image>
          </template>
          <view style="color: #ccccccaa;">
            <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon>
          </view>
        </wd-cell>

        <wd-cell clickable @click="toRouter(`/pages/vipProtocol/index`, `title=${encodeURIComponent('《隐私政策》')}`)" :customStyle="{ marginBottom: '15rpx', paddingBottom: '15rpx' }">
          <template #title>
            <text style="font-size: 30rpx; color: #111111;">隐私政策</text>
          </template>
          <template #icon>
            <image class="my-cell_img" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/menu-icon3.png"></image>
          </template>
          <view style="color: #ccccccaa;">
            <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon>
          </view>
        </wd-cell>

        <wd-cell clickable @click="toRouter(`/pages/vipProtocol/index`, `title=${encodeURIComponent('《用户协议》')}`)" :customStyle="{ marginBottom: '15rpx', paddingBottom: '15rpx' }">
          <template #title>
            <text style="font-size: 30rpx; color: #111111;">用户协议</text>
          </template>
          <template #icon>
            <image class="my-cell_img" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/menu-icon4.png"></image>
          </template>
          <view style="color: #ccccccaa;">
            <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon>
          </view>
        </wd-cell>

        <wd-cell clickable @click="toRouter(`/pages/opinion/index`)" :customStyle="{ marginBottom: '15rpx', paddingBottom: '15rpx' }">
          <template #title>
            <text style="font-size: 30rpx; color: #111111;">意见与反馈</text>
          </template>
          <template #icon>
            <image class="my-cell_img" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/menu-icon5.png"></image>
          </template>
          <view style="color: #ccccccaa;">
            <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon>
          </view>
        </wd-cell>

        <!-- <wd-cell clickable @click="goFuliCenter"> -->
        <!--   <template #title> -->
        <!--     <text style="font-size: 30rpx; color: #111111;">福利中心</text> -->
        <!--   </template> -->
        <!--   <template #icon> -->
        <!--     <image class="my-cell_img" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/my/menu-icon5.png"></image> -->
        <!--   </template> -->
        <!--   <view style="color: #ccccccaa;"> -->
        <!--     <wd-icon color="#7F7F7F" size="16" name="arrow-right"></wd-icon> -->
        <!--   </view> -->
        <!-- </wd-cell> -->
      </view>
    </view>

    <view class="logout" @click="loginOut" v-if="user.uid">退出登录</view>

    <wd-message-box selector="wd-contact-box-slot"></wd-message-box>
  </view>
  <!-- <towxml :nodes="xmlData"></towxml> -->
  <wd-message-box></wd-message-box>
  <Message ref="msgRef"></Message>
  <NavBar :index="4"></NavBar>
</template>

<script setup>
import { ref, getCurrentInstance } from 'vue'
import { onLoad, onShareAppMessage, onShow } from '@dcloudio/uni-app'
import { toRouter } from '@/hooks/utils'
import NavBar from '@/section/a-navbar.vue'
import { useMessage } from '/node_modules/wot-design-uni'
import Message from './message.vue'
import $http from '@/hooks/http'
const instance = getCurrentInstance();
const appContext = instance?.appContext
// const xmlData = ref(appContext?.config.globalProperties.$towxml(``, 'markdown'))
// console.log(appContext)


// const xmlData = ref(getApp().globalData.$towxml('111', 'markdown'))


const msgRef = ref(null), user = ref({})
const message = useMessage()
const platform = uni.getDeviceInfo().platform
const contactMessage = useMessage('wd-contact-box-slot')

const permissionsSetting = () => {
  uni.openSetting({
    success (res) {
      console.log(res.authSetting)
    }
  })
}

const openContact = () => {
  contactMessage.confirm({
    title: '即将跳转至“微信”进入客服聊天窗口',
    // confirmButtonProps: {
    //   openType: 'contact'
    // }
  }).then((type) => {
    if (type.action == 'confirm') {
      uni.openCustomerServiceChat({
        corpId: 'ww256d3515b9dce4dd',
        extInfo: {
          url: 'https://work.weixin.qq.com/kfid/kfc3e3bde8ca6a0f861'
        }
      })
    }
  })
}

const loginOut = () => {
  message.confirm({
    title: '退出',
    msg: '您要退出吗'
  }).then( () => {
    uni.showLoading({
      title: '正在退出'
    })

    // uni.removeStorageSync('username')
    $http.post('api/user/auth/userauth/logout').then( () => {
      uni.removeStorageSync('toolsToken')
      toRouter('/pages/login/index')
    }).finally(() => {
      uni.hideLoading()
    })
  } ).catch( () => {

  } )
}

const goFuliCenter = () => {
  uni.navigateTo({
    url: `/pages/webview/webview?src=${encodeURIComponent('https://xyhy.xingyhy.cn')}`,
  });
}

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/logo2.jpg',
    path: '/pages/index/index',
  }
})


const chooseAvatar = async (event) => {
  uni.showLoading({
    title: '正在处理'
  })

  let res = await $http.upload('api/global/fileupload/upload', event.detail.avatarUrl).catch(() => {})

  if (res?.data) {
    uni.login({
      success: (res1) => {
        $http.post('api/user/auth/userauth/get_openid', {
          code: res1.code,
          micro_appid: uni.getAccountInfoSync().miniProgram.appId
        }).then((res2) => {
          $http.post('api/user/auth/userauth/update_wx_info', {
            openid: res2.data,
            avatarUrl: res.data,
            nickName: user.value.nickname
          }).then(() => {
            user.value.avatar_url = res.data
          }).finally(() => {
            uni.hideLoading()
          })
        }).catch(() => {
          uni.hideLoading()
        })
      }
    })
  } else {
    uni.hideLoading()

    uni.showToast({
      title: '上传失败',
      icon: 'none'
    })
  }
}

const nicknameChange = (event) => {
  user.value.nickname = event.detail.value

  uni.login({
    success: (res) => {
      $http.post('api/user/auth/userauth/get_openid', {
        code: res.code,
        micro_appid: uni.getAccountInfoSync().miniProgram.appId
      }).then((res) => {
        $http.post('api/user/auth/userauth/update_wx_info', {
          openid: res.data,
          avatarUrl: user.value.avatar_url,
          nickName: user.value.nickname
        })
      })
    }
  })
}

const toMessage = () => {
  msgRef.value.openMessage()
}

onShow(() => {
  $http.get('api/user/auth/userauth/info?referch=1').then(res => {
    let vip_info = res.data.vip_info

    if (vip_info.vip_end_time) {
      vip_info.vip_end_time = `${vip_info.vip_end_time.slice(0, 4)}年${vip_info.vip_end_time.slice(5, 7)}月${vip_info.vip_end_time.slice(8, 10)}日`
    }

    user.value = {
      ...res.data,
      ...vip_info,
    };
  }).catch(() => {
    user.value = {}
  })
})

const toMember = () => {
  if (!user.value.uid) {
    uni.showModal({
      title: '提示',
      content: '您当前未登录或登录已失效，为了您有更好的体验，星跃FUN需要您进行登录',
      showCancel: true,
      success: (res) => {
        if (res.confirm) {
          toRouter('/pages/login/index')
        } else if (res.cancel) {
          console.log('用户点击取消')
        }
      }
    })

    return
  }

  toRouter('/pages/member/index')
}

const jump = () => {
  if (!user.value.uid) {
    toRouter('/pages/login/index')
    return
  }

  toRouter('/pages/account/index')
}
</script>

<style>
page {
  height: 100%;
}
</style>

<style lang="scss">
page {
  background: #F6F7F9;
  padding-bottom: 80rpx;
}

.page-title {
  color: #111111;
}

.banner {
  padding: calc(var(--page-title-height)) 0 59rpx;
}

.my-index {
  display: flex;
  flex-direction: column;
  --wot-message-box-width: 650rpx;
}

.my-nav{
  flex-shrink: 0;
  position: relative;
  padding: 0 30rpx;
  margin-bottom: 40rpx;

  .user-info {
    display: flex;
    align-items: center;
    margin-bottom: 50rpx;

    .my-status {
      margin-left: 19rpx;
    }

    .setting {
      image {
        width: 44rpx;
      }
    }
  }
}
.my-cell{
  --wot-color-white: none;
}
.my-status{
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 0.6rem;
  color: #fff;
}
.join-vip{
  background: url('https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/my/vip-bg.png');
  background-size: 100% auto;
  height: 130rpx;
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: #FFFFFF;
  padding-left: 117rpx;
  padding-right: 20rpx;

  button {
    align-self: flex-start;
    margin-top: 16rpx;
  }
}
.join-vip_name{
  font-weight: bold;
  font-size: 28rpx;
  color: #703807;
  // margin-bottom: 10rpx;
}
.join-vip_tip{
  font-size: 22rpx;
}

.join-bottom {
  font-weight: 500;
  font-size: 28rpx;
  color: #703807;
  white-space: nowrap;
  position: relative;
  display: flex;
  align-items: center;
  gap: 20rpx;

  .arrow {
    width: 42rpx;
  }
}
.global-m_y{
  flex-grow: 1;

  .menu-box {
    padding: 10rpx 20rpx 0;
    background: #FFFFFF;
    border-radius: 20rpx;
    --wot-cell-padding: 0;
  }
}
.my-cell_img{
  width: 22px;
  margin-right: 32rpx;
  position: relative;
  top: 6rpx;
}

.chooseAvatar {
  padding: 0;
  margin: 0;
  background: transparent;
  height: 124rpx;

  &:after {
    border: none;
    outline: 0;
  }
}

.contact-btn {
  background: transparent;
  display: flex;
  align-items: center;
  justify-content: flex-start;

  &:after {
    border: none;
    outline: 0;
  }

  text {
    font-size: var(--wot-cell-title-fs, 14px);
    color: var(--wot-cell-title-color, rgba(0, 0, 0, 0.85));
    padding-left: 2rpx;
    flex-grow: 1;
    text-align: left;
  }
}

.logout {
  width: 650rpx;
  height: 100rpx;
  margin: 100rpx auto;
  background: #448BFF;
  border-radius: 50rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  font-size: 32rpx;
  color: #FFFFFF;
}
</style>