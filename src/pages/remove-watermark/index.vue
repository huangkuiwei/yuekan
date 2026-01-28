<template>
  <view class="pic-transform">
    <view class="transform-title">选择图片</view>

    <view @click="chooseImage" class="select-box">
      <!--<image-->
      <!--    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/my/trans.png"-->
      <!--    mode="heightFix"-->
      <!--/>-->
      <view class="select-title" style="margin-left: 0.5rem">本地图片文件</view>
      <wd-icon name="arrow-right" size="20" color="#999"></wd-icon>
    </view>

    <view class="tip">温馨提示：请在图片中擦除你要去除的水印部分，再点击开始处理</view>

    <view class="water-mark-box">
      <image v-if="removeWatermarkUrl" :src="removeWatermarkUrl" mode="aspectFit"/>

      <template v-else-if="previewImg">
        <image id="paintImage" :src="previewImg" mode="aspectFit"/>

        <view class="my-canvas">
          <canvas
              type="2d"
              id="paintCanvas"
              @touchstart="onTouchStart"
              @touchmove="onTouchMove"
              @touchend="onTouchEnd"
              :style="canvasStyle"
          />
        </view>
      </template>
    </view>

    <view class="bottom-options">
      <wd-button :disabled="!(previewImg && !removeWatermarkUrl)" @click="handler">开始处理</wd-button>
      <wd-button :disabled="!removeWatermarkUrl" @click="save">保存</wd-button>
    </view>
  </view>

  <wd-popup
      :closable="false"
      :modelValue="countTipDialog"
      custom-style="width: 750rpx; background: #F7F7F7; border-radius: 30rpx 30rpx 0rpx 0rpx;"
      position="bottom"
      :z-index="99999"
  >
    <view class="count-tip-container">
      <view class="close">
        <image @click="countTipDialog = false" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/close2.png" />
      </view>

      <view class="tip1">免费次数已用尽，成为会员享受完整权益</view>
      <view class="tip2">开通会员解锁全部特权</view>
      <view class="icon-list">
        <view class="icon-item">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/icon01.png"/>
        </view>

        <view class="icon-item">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/icon02.png"/>
        </view>

        <view class="icon-item">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/icon03.png"/>
        </view>

        <view class="icon-item">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/icon04.png"/>
        </view>

        <view class="icon-item">
          <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/vip/icon05.png"/>
        </view>
      </view>
      <view class="vip-list">
        <view class="title">
          <text>会员套餐</text>
          <text @click="toRouter('/pages/member/index')">更多详情</text>
        </view>

        <view class="list-detail">
          <view class="detail" @click="memberSelect(item)" :class="{ active: price.id === item.id }" v-for="item of lists" :key="item.id">
            <view class="left">
              <text>{{ item.product_name }}</text>
              <text>{{ item.tip }}</text>
            </view>

            <view class="right">
              <text>￥</text>
              <text>{{ item.forever }}</text>
            </view>
          </view>
        </view>

        <view class="buy" @click="onPay(price, openid, ['1'], user)">￥{{ price.forever }}立即开通</view>
      </view>
    </view>
  </wd-popup>
</template>

<script setup>
import { computed, ref } from 'vue'
import $http from '@/hooks/http'
import { onLoad, onShareAppMessage, onShow } from '@dcloudio/uni-app'
import { toRouter } from '@/hooks/utils'
import { onPay } from '@/pages/member/member'

const previewImg = ref('')
let paintCanvas = null
let ctx = null
let isDrawing = ref(false);
let hasDrawing = ref(false);
let lastX = ref(0);
let lastY = ref(0);
let lineColor = ref('#ffffff');
let lineWidth = ref(8);
let canvasStyle = ref({})
let removeWatermarkUrl = ref('');
const originalImgInfo = ref({})

const countTipDialog = ref(false);
const count = ref(0);

const lists = ref([])
const user = ref({})
const openid = ref(null)

const getProductList = () => {
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
      price: 1990,
      product_name: '爱悦看连续包月',
      id: 10009,
      recommend: false
    })

    res.data.forEach((item, index) => {
      item.forever = Number((item.price / 100).toFixed(2))

      if (item.id === 10009) {
        item.tip = '次月自动续费  可随时取消'
      } else if (item.id === 10000) {
        item.tip = '每日低至0.93元'
      } else if (item.id === 10001) {
        item.tip = '限时特惠，先到先得'
      }

      let unit = ''

      if (item.product_name.includes('月')){
        unit = '/月'
        item.price = (item.forever / 30).toFixed(2)
      } else if (item.product_name.includes('年')){
        unit = '/年'
        item.price = (item.forever / 366).toFixed(2)
      } else if (item.product_name.includes('季')){
        unit = '/季'
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

const price = computed(() => {
  return lists.value.find(item => item.select)
})

const memberSelect = (item) => {
  lists.value.forEach(item => {
    item.select = false
  })
  price.value = item
  console.log(item);
  item.select = !item.select;
}

const getCount = () => {
  return $http.get('api/user/tools/pic/tools_left/RemoveWatermark', {}, {
    showLoginModal: true
  }).then((res) => {
    if (res.data.left < 0) {
      res.data.left = 0
    }

    count.value = res.data.left
  })
}

onLoad((options) => {
  if (options.url) {
    picHandler([options.url])
  }

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

onShow(async () => {
  await getCount()

  if (count.value <= 0) {
    countTipDialog.value = true
  }
})

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/share.jpg',
    path: '/pages/index/index',
  }
})


const exit = () => {
  if (count.value === 0) {
    uni.navigateBack()
  } else {
    countTipDialog.value = false
  }
}

const chooseImage = async () => {
  await getCount()

  if (count.value === 0) {
    countTipDialog.value = true
    return
  }

  uni.chooseImage({
    count: 1,
    sizeType: ['original', 'compressed'],
    sourceType: ['album'],
    success: async (response) => {
      picHandler(response.tempFilePaths)
    },
    fail: (err) => {
      console.log(err);
    }
  });
}

const picHandler = async (tempFilePaths) => {
  removeWatermarkUrl.value = ''
  hasDrawing.value = false

  originalImgInfo.value = await getImageInfo(tempFilePaths[0])
  previewImg.value = tempFilePaths[0]
  let screenWidth = uni.getWindowInfo().screenWidth

  console.log((700 / 750) * screenWidth)

  setTimeout(() => {
    wx.createSelectorQuery()
        .select('#paintImage') // 在 WXML 中填入的 id
        .boundingClientRect()
        .exec(nodes => {
          let imageNode = nodes[0]

          console.log('imageNode', imageNode)

          if (imageNode.width / imageNode.height < originalImgInfo.value.width / originalImgInfo.value.height) {
            canvasStyle.value.width = imageNode.width + 'px'
            canvasStyle.value.height = (originalImgInfo.value.height / originalImgInfo.value.width) * imageNode.width + 'px'
          } else {
            canvasStyle.value.height = imageNode.height + 'px'
            canvasStyle.value.width = (originalImgInfo.value.width / originalImgInfo.value.height) * imageNode.height + 'px'
          }

          wx.createSelectorQuery()
              .select('#paintCanvas') // 在 WXML 中填入的 id
              .node(async ({ node: canvas }) => {
                paintCanvas = canvas

                if (imageNode.width / imageNode.height < originalImgInfo.value.width / originalImgInfo.value.height) {
                  canvas.width = imageNode.width;
                  canvas.height = (originalImgInfo.value.height / originalImgInfo.value.width) * canvas.width;
                } else {
                  canvas.height = imageNode.height;
                  canvas.width = (originalImgInfo.value.width / originalImgInfo.value.height) * canvas.height;
                }

                ctx = canvas.getContext('2d')
                ctx.clearRect(0, 0, canvas.width, canvas.height);
              })
              .exec()
        })
  }, 100)
}

const getImageInfo = (path) => {
  return new Promise((resolve, reject) => {
    uni.getImageInfo({
      src: path,
      success: (res) => {
        resolve(res)
      },
      fail: (error) => {
        reject(error)
      }
    });
  })
}

const onTouchStart = (e) => {
  hasDrawing.value = true;
  isDrawing.value = true;
  const { x, y } = e.touches[0];
  lastX.value = x;
  lastY.value = y;
}

const onTouchMove = (e) => {
  if (!isDrawing.value) return;

  const { x, y } = e.touches[0];
  drawLine(lastX.value, lastY.value, x, y);
  lastX.value = x;
  lastY.value = y;
}

const onTouchEnd = () => {
  isDrawing.value = false;
}

const drawLine = (fromX, fromY, toX, toY) => {
  ctx.beginPath();
  ctx.moveTo(fromX, fromY);
  ctx.lineTo(toX, toY);
  ctx.strokeStyle = lineColor.value;
  ctx.lineWidth = lineWidth.value;
  ctx.stroke();
}

const handler = async () => {
  if (!hasDrawing.value) {
    uni.showToast({
      title: '请先选择需要擦除的部分',
      icon: 'none'
    })

    return
  }

  uni.showLoading({
    title: '正在擦除水印',
    mask: true,
  })

  ctx.restore()

  let res = await $http.upload('api/global/fileupload/upload', previewImg.value).catch(() => {})

  if (res?.data) {
    let canvasUrl = await uploadCanvas().catch(() => {})

    let res2 = await $http.post('api/user/tools/pic/remove_watermark', { img_url: res.data, mask_url: canvasUrl }).catch(() => {})

    if (canvasUrl && res2?.data) {
      removeWatermarkUrl.value = res2.data.img_url
      uni.hideLoading()

      uni.showToast({
        title: '处理完成',
        icon: 'none'
      })
    } else {
      uni.hideLoading()
    }
  } else {
    uni.hideLoading()
  }
}

const uploadCanvas = () => {
  return new Promise((resolve, reject) => {
    uni.canvasToTempFilePath({
      canvas: paintCanvas,
      success: async (res) => {
        const canvas = uni.createOffscreenCanvas({ width: originalImgInfo.value.width, height: originalImgInfo.value.height , type: '2d' })
        const ctx = canvas.getContext('2d')
        const img = canvas.createImage()

        img.onload = () => {
          ctx.drawImage(img, 0, 0, originalImgInfo.value.width, originalImgInfo.value.height)
          ctx.restore()

          let base64 = canvas.toDataURL()

          const fs = uni.getFileSystemManager();
          const tempFilePath = `${uni.env.USER_DATA_PATH}/${new Date().getTime()}.png`;

          fs.writeFile({
            filePath: tempFilePath,
            data: base64.split(',')[1], // Remove the data URL prefix and decode Base64
            encoding: 'base64',
            success: async () => {
              let res1 = await $http.upload('api/global/fileupload/upload', tempFilePath).catch(() => {})

              if (res1?.data) {
                resolve(res1.data)
              } else {
                reject()
              }
            },
            fail: (error) => {
              reject(error)
            }
          });
        }

        img.src = res.tempFilePath
      },
      fail: (err) => {
        console.log('err', err)
        reject(err)
      }
    })
  })
}

const save = () => {
  uni.downloadFile({
    url: removeWatermarkUrl.value,
    success: (res) => {
      uni.saveImageToPhotosAlbum({
        filePath: res.tempFilePath,
        success: () => {
          uni.showToast({
            title: "保存成功",
            icon: "none",
            duration: 2000,
          });
        },
        fail: (res) => {
          console.log("保存失败", res);
          uni.showToast({
            title: "保存失败",
            icon: "none",
            duration: 2000,
          });
        },
      });
    },
    fail: (res) => {
      console.log("下载失败", res);
    },
    complete: () => {
      uni.hideLoading()
    }
  });
}
</script>

<style lang="scss">
page {
  height: 100%;
}

.pic-transform {
  height: 100%;
  overflow: hidden;
  display: flex;
  flex-direction: column;

  .transform-title {
    flex-shrink: 0;
    padding: 20rpx;
    font-size: 30rpx;
  }

  .select-box {
    flex-shrink: 0;
    display: flex;
    align-items: center;
    padding: 20rpx 36rpx;
    background: #ffffff;
    color: #333333;
    font-size: 28rpx;

    image {
      height: 70rpx;
      margin-right: 20rpx;
    }

    .select-title {
      flex-grow: 1;
    }
  }

  .tip {
    flex-shrink: 0;
    text-align: center;
    font-size: 24rpx;
    padding: 20rpx 0;
    color: #e04350;
  }

  .water-mark-box {
    flex-grow: 1;
    overflow: auto;
    position: relative;
    width: 700rpx;
    margin: 0 auto;
    padding: 0 0 20rpx;
    display: flex;
    align-items: center;
    justify-content: center;

    image {
      height: 100%;
    }

    .my-canvas {
      position: absolute;
      top: 0;
      bottom: 20rpx;
      z-index: 9;
      display: flex;
      align-items: center;

      canvas {

      }
    }
  }

  .bottom-options {
    flex-shrink: 0;
    width: 100%;
    height: 100rpx;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    background: #ffffff;
    z-index: 99;
  }
}
</style>
