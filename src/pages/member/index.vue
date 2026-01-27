<template>
  <view class="member-page">
    <view class="page-title">
      <text>会员中心</text>

      <view class="back" @click="toBack">
        <uni-icons class="back" color="#1A1A1A" type="left" size="22"></uni-icons>
      </view>
    </view>

    <view class="banner"></view>

    <view class="member-top">
      <view style="width: calc(100% - 60rpx); display: flex; align-items: center; padding: 0 30rpx">
        <view style="display: flex; flex-direction: column; justify-content: center; gap: 10rpx; flex-grow: 1; margin-right: 10rpx">
          <view style="font-weight: bold; font-size: 30rpx; color: #B47643;">
            <view v-if="!user.uid">未登录</view>
            <view v-else>{{user.nickname || '微信用户'}}</view>
          </view>

          <view style="align-self: self-start; font-size: 24rpx; color: #B47643;">
            <view v-if="!user.uid && platform !== 'ios'">
              低至每天<text style="">0.35</text>元，畅享全部权益
            </view>
            <view
                v-if="user.uid"
                style="display: flex; align-items: center; align-self: self-start; font-size: 24rpx; color: #B47643;"
            >
              <wd-icon v-if="user.vip_type" custom-class="iconfont" class-prefix="icon" name="Silver" color="#BF932A" size="14"></wd-icon>

              <view>
                <text v-if="user.vip_type === 0">免费用户</text>
                <text v-else>会员到期还剩{{ countDay }}天</text>
              </view>
            </view>
          </view>
        </view>

        <button v-if="!user.uid" @click="login" size="small" class="padding-x_medium">登录</button>
        <!--<template v-else>-->
        <!--  <button v-if="platform !== 'ios'" @click="renewal" size="small" class="padding-x_medium">开启自动续费</button>-->
        <!--</template>-->
      </view>
    </view>

    <view class="member-content-wrap">
      <view class="member-content">
        <view class="member-grid">
          <view @click="memberSelect(item)" class="member-grid_li" :class="{'active': item.select}" v-for="(item,index) in lists" :key="index">
            <!-- <image v-if="item.recommend" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/member/hot.png" class="recommend"/> -->
            <view class="recommend" v-if="item.recommend">推荐</view>

            <view style="padding: 0 0 10rpx 0; font-size: 28rpx; color: #111111;">{{item.name}}</view>
            <view class="member-price">
              <view class="tip">¥</view>
              <view class="price">{{ item.forever }}</view>
              <!--<view class="tip">{{item.unit}}</view>-->
            </view>

            <view class="forever">
              每天仅需￥{{item.price}}
            </view>
          </view>
        </view>

        <view class="shop-detail">
          <view class="quanyi">
            <view class="title">VIP尊享 18项权益</view>

            <view class="table">
              <view class="table-item">
                <text v-for="item of rules.map(x => x[0])">{{ item }}</text>
              </view>

              <view class="table-item">
                <text v-for="item of rules.map(x => x[1])">{{ item }}</text>
              </view>

              <view class="table-item">
                <text v-for="item of rules.map(x => x[2])">{{ item }}</text>
              </view>
            </view>
          </view>
        </view>

        <view class="buy">
          <view class="submit" v-if="platform !== 'ios'" @click="onPay(price, openid, agree, user)">
            {{ user.vip_type === 0 ? '开通会员' : '续费会员' }}
          </view>

          <view v-else>
            <view class="buy-tip">由于相关规定，iOS版小程序暂不支持购买</view>
            <button class="submit" open-type="contact">联系客服</button>
          </view>

          <view class="agree" v-if="platform !== 'ios'">
            <checkbox-group :value="agree" @change="agree = $event.detail.value">
              <label style="display: flex; align-items: center">
                <checkbox style="transform: scale(0.55)" value="1"></checkbox>
                <text>我已阅读并同意</text>
              </label>
            </checkbox-group>

            <text style="color: #E25946; margin-left: 3px;" @click="toRouter(`/pages/vipProtocol/index`, `title=${encodeURIComponent('会员协议')}`)">《会员服务协议》</text>
            <!--及-->
            <!--<text style="color: #ED0000; margin-left: 3px;" @click="toRouter(`/pages/vipProtocol/index`, `title=${encodeURIComponent('会员协议')}`)">自动续费规则</text>-->
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, computed } from 'vue'
import { onPay } from './member'
import { onLoad, onShareAppMessage } from '@dcloudio/uni-app'
import $http from '@/hooks/http'
import { toRouter } from '@/hooks/utils'

const show = ref(true)
const radio = ref(1),user = ref({})
const openid = ref(null)
const agree = ref([])

const rules = [
  ['热门权益', '白金会员', '普通用户'],
  ['证件扫描', '无限次', '3次/月'],
  ['文件扫描', '无限次', '10次/月'],
  ['文字提取', '无限次', '10次/月'],
  ['拍照计数', '无限次', '1次/月'],
  ['文字翻译', '无限次', '3次/月'],
  ['去手写', '无限次', '/'],
  ['公式识别', '无限次', '/'],
  ['图片转PDF', '无限次', '10次/月'],
  ['图片转PPT', '无限次', '/'],
  ['图片转WORD', '无限次', '免费试用1次'],
  ['图片转表格', '无限次', '免费试用1次'],
  ['加水印', '无限次', '/'],
  ['去水印', '无限次', '/'],
  ['拼图', '无限次', '3次/月'],
  ['PDF转图片', '无限次', '/'],
  ['PDF转表格', '无限次', '/'],
  ['PDF转PPT', '无限次', '/'],
  ['PDF转WORD', '无限次', '/'],
]

const platform = uni.getDeviceInfo().platform

onLoad(() => {
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

  // const username = uni.getStorageSync('username')
  // uni.request({
  //   url: '/api/user/username?username=' + username,
  //   method: 'GET',
  //   success: (res) => {
  //     user.value = res.data
  //   },
  //   fail: (res) => {}
  // })
  uni.login({
    success: (res) => {
      console.log('res.code', res.code)

      $http.post('api/user/auth/userauth/get_openid', {
        code: res.code,
        micro_appid: uni.getAccountInfoSync().miniProgram.appId
      }).then((res) => {
        openid.value = res.data
      })
    }
  })

  getProductList()
})

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/logo2.jpg',
    path: '/pages/index/index',
  }
})

let countDay = computed(() => {
  let nowDate = Date.now()
  let endTime = new Date(user.value.vip_end_time.replace(/-/g, '/'))

  return Math.ceil((endTime - nowDate) / (1000 * 24 * 60 * 60))
})

// TODO 客服
const openContact = () => {
  uni.openCustomerServiceChat({
    corpId: 'ww256d3515b9dce4dd',
    extInfo: {
      url: 'https://work.weixin.qq.com/kfid/kfc3e3bde8ca6a0f861'
    }
  })
}

const lists = ref([])
// price.value = lists.value[0]

const price = computed(() => {
  return lists.value.find(item => item.select)
})

const toBack = () => {
  uni.navigateBack();
}

const getProductList = async () => {
  $http.get('api/global/product/get').then(res => {
    // 日会员暂时不做显示
    let index = res.data.findIndex(item => item.product_name.includes('日会员'))

    res.data.forEach(item => {
      item.recommend = item.id === 10000;
    })

    if (index !== -1) {
      res.data.splice(index, 1)
    }

    // 连续包月
    res.data.splice(1, 0, {
      price: 2990,
      product_name: '爱悦看连续包月',
      id: 10009,
      recommend: false
    })

    res.data.forEach((item, index) => {
      item.forever = Number((item.price / 100).toFixed(2))

      let unit = ''

      if (item.product_name.includes('月')){
        unit = '/月'
        item.price = (item.forever / 30).toFixed(2)
      } else if (item.product_name.includes('年')){
        unit = '/年'
        item.price = (item.forever / 366).toFixed(2)
      } else if (item.product_name.includes('季度')){
        unit = '/季度'
        item.price = (item.forever / 90).toFixed(2)
      } else if (item.product_name.includes('终身')){
        unit = '/终身'
      }

      item.name = item.product_name
      item.proto = item.forever
      item.unit = unit
      item.select = (index === 0)
    })

    lists.value = res.data
  })
}

const icons = ref([
  {
    name: '证件扫描',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m1.png'
  },
  {
    name: '文字提取',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m2.png'
  },
  {
    name: '文字翻译',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m3.png'
  },
  {
    name: '去手写',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m4.png'
  },
  {
    name: '图片加水印',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m5.png'
  },
  {
    name: '图片转Word',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m6.png'
  },
  {
    name: '图片转PPT',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m7.png'
  },
  {
    name: '拼图',
    src: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/m8.png'
  }
])

const memberSelect = (item) => {
  lists.value.forEach(item => {
    item.select = false
  })
  price.value = item
  console.log(item);
  item.select = !item.select;
}

const login = () => {
  toRouter('/pages/login/index')
}

const renewal = () => {
  let product = lists.value.find(item => item.product_name.includes('连续包月'))

  if (product) {
    onPay(product, openid.value, agree.value, user.value)
  }
}
</script>

<style lang="scss">
page{
  --wot-button-error-bg-color: rgba(229, 22, 22, 1);
  --wot-button-large-height: 3rem;
  --wot-button-large-fs: 1.4rem;
  background: #ffffff;
  padding-bottom: 240rpx;
}

.member-page {
  background: url("https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/banner.png") left top/100% 380rpx no-repeat;
}

.page-title {
  color: #111111;
}

.banner {
  padding: calc(var(--page-title-height)) 0 48rpx;
}

.member-top{
  margin-bottom: 50rpx;
}

.padding-x_medium {
  flex-shrink: 0;
  width: 183rpx;
  height: 50rpx;
  background: #FBCEA2;
  border-radius: 25rpx;
  font-weight: 500;
  font-size: 24rpx;
  color: #B47643;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 !important;
  margin: 0 !important;

  &::after {
    border: none;
  }
}

.member-content-wrap{
  background: #FFFFFF;

  .member-content {
    padding: 60rpx 40rpx;
  }
}
.member-grid{
  display: grid;
  grid-gap: 10px;
  grid-template-columns: repeat(3, 1fr);

  .price{
    color: #2A1601;
  }
  .tip{
    color: #2A1601;
  }

  .forever{
    width: 100%;
    height: 50rpx;
    background: #F5F5F5;
    border-radius: 0 0 20rpx 20rpx;
    font-size: 24rpx;
    color: #97948F;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .active{
    background: linear-gradient(0deg, #FFF7EC 0%, #FBFFFF 100%);
    border: 2rpx solid #DDDBCE;

    .forever{
      background: linear-gradient(90deg, #FCE6C1 0%, #F6D19A 100%);
      font-size: 24rpx;
      color: #6F4C14;
    }
  }
}

.member-grid_li{
  background: #FFFFFF;
  border: 2rpx solid #BFBFBF;
  border-radius: 20rpx;
  text-align: center;
  padding: 30rpx 0 0;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .recommend {
    position: absolute;
    top: -16rpx;
    left: -1px;
    width: 70rpx;
    height: 35rpx;
    background: linear-gradient(90deg, #73C1FF 0%, #2D85FE 100%);
    border-radius: 18rpx 18rpx 18rpx 0rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 500;
    font-size: 24rpx;
    color: #FFFFFF;
  }
}
.member-price{
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 22rpx;
  margin-bottom: 30rpx;

  .price{
    font-size: 52rpx;
    font-weight: bold;
    padding: 0;
  }
  .tip{
    display: flex;
    height: 32rpx;
    align-items: flex-end;
    position: relative;
    top: 10rpx;
  }
}

.shop-detail {
  margin-top: 50rpx;

  image {
    width: 100%;
  }

  .quanyi {
    .title {
      font-weight: 500;
      font-size: 30rpx;
      color: #111111;
      margin-bottom: 47rpx;
    }

    .table {
      display: flex;
      align-items: center;
      background: #FFFDFC;
      border-radius: 20rpx;
      border: 1px solid #F2C48D;

      .table-item {
        width: 33%;
        flex-grow: 1;
        display: flex;
        flex-direction: column;

        &:nth-child(1) {
          font-size: 24rpx;
          color: #111111;
          border-right: 2rpx solid #F2C48D;

          text {
            &:nth-child(1) {
              border-radius: 20rpx 0 0 0;
            }

            &:nth-child(3) {
              border-radius: 0 20rpx 0 0;
            }
          }
        }

        &:nth-child(2), &:nth-child(3) {
          font-size: 24rpx;
          color: #A15506;

          &:nth-child(2) {
            border-right: 2rpx solid #F2C48D;
          }
        }

        &:nth-child(3) {
          text {
            &:nth-child(1) {
              border-radius: 0 20rpx 0 0;
            }
          }
        }

        text {
          display: flex;
          align-items: center;
          justify-content: center;
          height: 80rpx;

          &:not(:last-child) {
            border-bottom: 2rpx solid #F2C48D;
          }

          &:nth-child(2n-1) {
            background: #FFFDF5;
          }

          &:nth-child(1) {
            font-weight: 500;
            font-size: 28rpx;
            color: #87755F;
            background: linear-gradient(90deg, #FFF5DF 0%, #FFE8C0 100%);
          }
        }
      }
    }
  }
}

.buy {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  height: 220rpx;
  background: #ffffff;
  box-shadow: 0 -4px 4px #eee;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .submit {
    width: 650rpx;
    height: 100rpx;
    background: linear-gradient(90deg, #FCE6C1 0%, #F6D19A 100%);
    border-radius: 50rpx;
    font-weight: 500;
    font-size: 34rpx;
    color: #6F4C14;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 30rpx;
  }

  .agree {
    font-size: 20rpx;
    color: #999999;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}

.member-t{
  display: flex;
  align-items: center;
  padding: 1rem 0 0.2rem 0;
}
.member-xieyi{
  margin-top: 0.2rem;
  font-size: 0.85rem;
  color: rgba(153, 153, 153, 1);
}

.vip-detail {
  .title {
    padding: 20rpx 0;
    font-weight: bold;

    text {
      &:nth-child(1) {
        color: #333333;
      }

      &:nth-child(2) {
        color: #FD502C;
      }
    }
  }

  .table {
    display: flex;
    position: relative;
    box-shadow: 0 3px 3px #eee;

    .line1, .line2, .line3, .line4 {
      width: 33%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      color: #555555;

      &.line1 {
        view {
          &:nth-child(1) {
            border-top-left-radius: 16rpx;
          }

          &:last-child {
            border-bottom-left-radius: 16rpx;
          }

          &:nth-child(2n - 1) {
            background: #F6F6F6;
          }
        }
      }

      &.line2 {
        view {
          &:nth-child(2n - 1) {
            background: #F6F6F6;
          }
        }
      }

      &.line3 {
        view {
          &:nth-child(1) {
            border-top-right-radius: 16rpx;
          }

          &:last-child {
            border-bottom-right-radius: 16rpx;
          }

          &:nth-child(2n - 1) {
            background: #F6F6F6;
          }
        }
      }

      &.line4 {
        position: absolute;
        width: 33%;
        left: 33%;
        top: -20rpx;
        bottom: -20rpx;
        background: linear-gradient(180deg, #FFECC190 0%, #FECF7390 100%) !important;
        border-radius: 16rpx;
        z-index: 9;

        view {
          display: flex;
          align-items: center;
          justify-content: center;

          &:nth-child(1) {
            font-weight: bold;
            font-size: 32rpx;
            height: 60rpx;
            border-top-left-radius: 16rpx;
            border-top-right-radius: 16rpx;
            color: #A85305;
          }

          &:last-child {
            height: 60rpx;
            border-bottom-left-radius: 16rpx;
            border-bottom-right-radius: 16rpx;
          }

          image {
            width: 26rpx;
          }
        }
      }

      view {
        font-size: 28rpx;
        text-align: center;
        height: 40rpx;
        padding: 10rpx 0;

        &:nth-child(1) {
          font-weight: bold;
          color: #111111;
        }
      }
    }
  }
}

.buy-tip {
  text-align: center;
  font-size: 24rpx;
  margin-bottom: 10rpx;
}

.contact-btn-m {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding-top: 100rpx;

  .contact-btn {
    width: 100%;
    color: #ffffff;
    background: #FA4350;
    border-radius: 999px;
  }
}
</style>