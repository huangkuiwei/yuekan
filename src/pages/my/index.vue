<template>
 <view class="my-index">
   <view class="my-nav">
     <view style="width: calc(100% - 30rpx); padding-left: 30rpx; display: flex; align-items: center;">
       <button style="height: 150rpx;" class="chooseAvatar" open-type="chooseAvatar" @chooseavatar="chooseAvatar" v-if="user.uid">
         <image
             style="height: 150rpx; border-radius: 50%"
             :src="user.avatar_url || `https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png`"
             mode="heightFix"
         />
       </button>

       <image
           v-else
           style="height: 150rpx; border-radius: 50%"
           :src="user.avatar_url || `https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png`"
           mode="heightFix"
       />

       <view class="my-status" style="gap: 10rpx; flex-grow: 1; font-size: 22rpx; color: #333333;">
         <view style="font-weight: 400;font-size: 40rpx; color: #000000;">
           <view v-if="user.uid" style="display: flex; align-items: center; justify-content: flex-start; position: relative;">
             <view style="max-width: 400rpx; overflow: auto; text-overflow: ellipsis; white-space: nowrap">{{ user.nickname || '微信用户' }}</view>
             <image v-if="user.vip_type" style="width: 45rpx; padding-left: 15rpx" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/icon03.png"></image>
             <image v-else style="width: 45rpx; padding-left: 15rpx" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/icon02.png"></image>
             <!-- <input style="position: absolute; font-size: 40rpx;left: 0; max-width: 400rpx; overflow: auto; text-overflow: ellipsis" class="username" type="nickname" :value="user.nickname || '微信用户'" @change="nicknameChange"> -->
           </view>
           <view v-else @click="toRouter('/pages/login/index')">
             未登录
           </view>
         </view>

         <view class="tip" v-if="!user.uid" @click="toRouter('/pages/login/index')">您还未登录 请先登录</view>
         <view class="tip" v-else>ID:{{user.uid}}</view>
       </view>
     </view>

     <view class="join-vip">
       <image @click="toRouter('/pages/login/index')" v-if="!user.uid" class="vip-bg" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/bg2.png" />
       <image @click="toMember" v-else-if="!user.vip_type" class="vip-bg" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/bg2.png" />
       <image @click="toMember" v-else class="vip-bg" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/bg2.png" />

       <view class="content">
         <view class="left">
           <view class="top">
             <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/icon1.png"/>
             <text>{{ user.vip_type ? '尊贵的vip会员' : '开通vip会员' }}</text>
           </view>

           <view class="bottom">
             {{ user.vip_type ? `会员有效期至：${user.vip_end_time}` : '开通VIP解锁更多功能，尊享全部权益' }}
           </view>
         </view>

         <view class="right" @click="user.uid ? toMember() : toRouter('/pages/login/index')">
           <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/btn.png"/>
         </view>
       </view>

       <view class="tip" @click="user.uid ? toMember() : toRouter('/pages/login/index')">
         <text>PDF转Word、PDF工具、一键擦除手写、文字提取等享19+特权</text>
         <text>＞</text>
       </view>
     </view>
   </view>

  <view class="menu-box-wrapper">
    <view class="menu-box">
      <view class="title">其他功能</view>

      <view class="menu-list">
        <view class="menu-item" @click="jump">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu01.png" />
          <text>账号管理</text>
        </view>

        <view class="menu-item" @click="toRouter('/pages/message/index')">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu02.png" />
          <text>消息与通知</text>
        </view>

        <view class="menu-item" @click="permissionsSetting">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu03.png" />
          <text>权限设置</text>
        </view>

        <button class="menu-item" open-type="contact">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu04.png" />
          <text>客服中心</text>
        </button>

        <view class="menu-item" @click="toRouter('/pages/help/index')">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu05.png" />
          <text>帮助</text>
        </view>

        <view class="menu-item" @click="toRouter('/pages/opinion/index')">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu06.png" />
          <text>意见与反馈</text>
        </view>

        <view class="menu-item" @click="toMessage">
          <image mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/menu07.png" />
          <text>检查更新</text>
        </view>
      </view>
    </view>
  </view>

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

// TODO 客服
const openContact = () => {
  contactMessage.confirm({
    title: '将跳转至“微信”打开客服聊天窗口',
    // confirmButtonProps: {
    //   openType: 'contact'
    // }
  }).then((type) => {
    if (type.action == 'confirm') {
      uni.openCustomerServiceChat({
        corpId: 'wwd8a1200c46e63718',
        extInfo: {
          url: 'https://work.weixin.qq.com/kfid/kfc571cd6bb4349e984'
        }
      })
    }
  })
}

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/share.png',
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

<style scoped lang="scss">
.my-index {
  height: 100%;
  background: url("https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/my/bg.png") left top/100% 100% no-repeat;
  overflow: auto;
  display: flex;
  flex-direction: column;
  --wot-message-box-width: 600rpx;
}

.my-nav{
  flex-shrink: 0;
  padding-top: 200rpx;
}
.my-cell{
  --wot-color-white: none;
}
.my-status{
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 30rpx;
  color: #333333;

  .tip {
    align-self: self-start;
    height: 46rpx;
    padding: 0 16rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 22rpx;
    color: #000000;
    background: #FFFFFF45;
    border-radius: 23rpx;
  }
}
.join-vip{
  margin-top: 42rpx;
  margin-bottom: 46rpx;
  padding: 0 34rpx;
  position: relative;

  .vip-bg {
    width: 100%;
  }

  .content {
    position: absolute;
    top: 30rpx;
    right: 68rpx;
    left: 68rpx;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .left {
      display: flex;
      flex-direction: column;
      gap: 21rpx;

      .top {
        display: flex;
        align-items: center;
        gap: 16rpx;

        image {
          width: 26rpx;
        }

        text {
          font-weight: 500;
          font-size: 28rpx;
          color: #FFFFFF;
        }
      }

      .bottom {
        font-size: 19rpx;
        color: #FFFFFF;
      }
    }

    .right {
      image {
        width: 129rpx;
      }
    }
  }

  .tip {
    position: absolute;
    bottom: 20rpx;
    left: 72rpx;
    right: 72rpx;
    font-size: 16rpx;
    color: #4B0202;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
}
.join-vip_name{
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 4rpx;
}
.join-vip_tip{
  font-size: 22rpx;
  color: #9A9A9A;
  position: absolute;
  top: 172rpx;
  left: 93rpx;
}

.join-bottom {
  position: absolute;
  top: 138rpx;
  right: 88rpx;
  width: 134rpx;
  height: 52rpx;
  background: #448BFF;
  border-radius: 10rpx;
  font-weight: 600;
  font-size: 24rpx;
  color: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  margin: 0;
}

.menu-box-wrapper {
  padding: 0 30rpx;

  .menu-box {
    background: #ffffff;
    border-radius: 30rpx;
    padding: 37rpx 0 0;

    .title {
      padding: 0 43rpx;
      font-weight: 500;
      font-size: 28rpx;
      color: #000000;
      margin-bottom: 44rpx;
    }

    .menu-list {
      display: flex;
      flex-wrap: wrap;

      button {
        background: transparent;
        padding: 0;
        margin: 0;

        image {
          position: relative;
          top: 10rpx;
        }

        &:after {
          border: none;
          outline: 0;
        }
      }

      .menu-item {
        width: 25%;
        margin-bottom: 46rpx;
        flex-shrink: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap: 24rpx;

        image {
          height: 40rpx;
        }

        text {
          font-weight: 500;
          font-size: 22rpx;
          color: #1F1F1F;
        }
      }
    }
  }
}

.global-m_y{
  // --wot-card-padding: 0.4rem 0.6rem;
  // --wot-card-margin: 0;
  // --wot-cell-title-fs: 0.9rem;
  flex-grow: 1;
  background: #FFFFFF;
  padding: 40rpx 0 0 0;
  border-radius: 30rpx;
}
.my-cell_img{
  height: 18px;
  width: 18rpx;
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
  padding-left: 30rpx;
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
</style>