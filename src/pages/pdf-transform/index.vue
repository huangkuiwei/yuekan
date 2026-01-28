t<template>
  <view class="pdf-transform">
    <view class="transform-title">选择PDF</view>

    <view @click="choosePDFFile" class="select-box">
      <!--<image-->
      <!--    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/my/trans.png"-->
      <!--    mode="heightFix"-->
      <!--/>-->
      <view class="select-title" style="margin-left: 0.5rem">微信PDF文件</view>
      <wd-icon name="arrow-right" size="20" color="#999"></wd-icon>
    </view>

    <view class="history">
      <view class="history-title">历史PDF</view>

      <view class="pic-list">
        <view class="pic-item" v-for="item of taskInfoList" :key="item.task_id">
          <view class="container">
            <image
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/images/PDF.png"
                mode="aspectFill"
            />
            <view class="name">{{ item.name }}</view>

            <!--<view v-if="item.status === '处理成功'" @click="item.showOptions = true">-->
            <!--  <wd-icon name="more" size="20"></wd-icon>-->
            <!--</view>-->

            <view class="status">
              <text :style="{ color: item.status === '处理失败' ? 'red' : '' }">{{ item.status }}</text>
              <text v-if="item.status === '处理成功'" @click="lookResult(item)">查看</text>
            </view>
          </view>

          <!--<view class="options">-->
          <!--  <view-->
          <!--      @click="toShareFile(item)"-->
          <!--      class="d-grid_li"-->
          <!--  >-->
          <!--    <image-->
          <!--        style="height: 16px; width: 16px"-->
          <!--        src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/document/d3.png"-->
          <!--    />-->
          <!--    <view>分享</view>-->
          <!--  </view>-->

          <!--  <view @click.stop="toDelete(item)" class="d-grid_li">-->
          <!--    <image-->
          <!--        style="height: 16px; width: 16px"-->
          <!--        src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/document/d4.png"-->
          <!--    />-->
          <!--    <view>删除</view>-->
          <!--  </view>-->
          <!--</view>-->
        </view>
      </view>
    </view>

    <!-- TODO -->
    <!--<view class="bottom-options" v-if="taskInfoList.length">-->
    <!--  <button>开始转化</button>-->
    <!--</view>-->

    <wd-popup
        class="preview-img-dialog"
        :closable="false"
        :z-index="9999"
        modal-style="background-color:rgba(0,0,0,0.2);"
        v-model="showPreviewImg"
        @close="showPreviewImg = false"
    >
      <view class="img-box">
        <image mode="widthFix" :src="previewImg[previewIndex]"/>
      </view>

      <view class="arrow" v-show="previewImg.length > 1">
        <text @click="previewIndex !== 0 && (previewIndex -= 1)">上一张</text>
        <text @click="(previewIndex < (previewImg.length - 1)) && (previewIndex += 1)">下一张</text>
      </view>

      <div class="options">
        <wd-button type="info" @click="showPreviewImg = false">取消</wd-button>
        <wd-button @click="save">保存到相册</wd-button>
      </div>
    </wd-popup>
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
import { computed, ref, watchEffect } from 'vue'
import { onLoad, onShareAppMessage, onShow } from '@dcloudio/uni-app'
import $http from '@/hooks/http'
import { toRouter } from '@/hooks/utils'
import { onPay } from '@/pages/member/member'

const taskInfoList = ref([])
const channel = ref('')
const url = ref('')
const counApi = ref('')
const showPreviewImg = ref(false)
const previewImg = ref([])
const previewIndex = ref(0)
const count = ref(undefined);
const countTipDialog = ref(false)

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
  return $http.get(counApi.value, {}, {
    showLoginModal: true
  }).then((res) => {
    if (res.data.left < 0) {
      res.data.left = 0
    }

    count.value = res.data.left
  })
}

onLoad(async (options) => {
  channel.value = options.channel

  if (channel.value) {
    if (channel.value === 'word') {
      uni.setNavigationBarTitle({
        title: 'PDF转Word'
      })

      url.value = 'api/user/tools/pdf/pdf_to_word'
      counApi.value = `api/user/tools/pdf/tools_left/PdfToWord`
    } else if (channel.value === 'excel') {
      uni.setNavigationBarTitle({
        title: 'PDF转Excel'
      })

      url.value = 'api/user/tools/pdf/pdf_to_excel'
      counApi.value = `api/user/tools/pdf/tools_left/PdfToExcel`
    } else if (channel.value === 'ppt') {
      uni.setNavigationBarTitle({
        title: 'PDF转PPT'
      })

      url.value = 'api/user/tools/pdf/pdf_to_ppt'
      counApi.value = `api/user/tools/pdf/tools_left/PdfToPPT`
    } else if (channel.value === 'pic') {
      uni.setNavigationBarTitle({
        title: 'PDF转图片'
      })

      url.value = 'api/user/tools/pdf/pdf_to_img'
      counApi.value = `api/user/tools/pdf/tools_left/PdfToImg`
    } else if (channel.value === 'longpic') {
      uni.setNavigationBarTitle({
        title: 'PDF转长图'
      })

      url.value = 'api/user/tools/pdf/pdf_to_img'
      counApi.value = `api/user/tools/pdf/tools_left/PdfToImg`
    }

    taskInfoList.value = uni.getStorageSync(`transformPDFList${channel.value}`) || [];

    await getCount()

    if (count.value <= 0) {
      countTipDialog.value = true
    }

    if (count.value > 0 && options.url) {
      pdfHandler({
        name: Date.now().toString() + '.pdf',
        path: options.url,
      })
    }
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

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/share.jpg',
    path: '/pages/index/index',
  }
})


onShow(async () => {
  await getCount()

  if (count.value === 0) {
    countTipDialog.value = true
  }
})

watchEffect(() => {
  // 本地存储一份
  if (channel.value) {
    uni.setStorageSync(`transformPDFList${channel.value}`, taskInfoList.value)
  }
})

const exit = () => {
  if (count.value === 0) {
    uni.navigateBack()
  } else {
    countTipDialog.value = false
  }
}

const choosePDFFile = async () => {
  await getCount()

  if (count.value === 0) {
    countTipDialog.value = true
    return
  }

  if (!count.value) {
    return
  }

  uni.chooseMessageFile({
    count: 1,
    type: 'file',
    extension: ['pdf'],
    success: async (response) => {
      pdfHandler(response.tempFiles[0])
    },
    fail: (err) => {
      console.log(err);
    }
  });
}

const pdfHandler = async (tempFilePath) => {
  uni.showLoading({
    title: '正在创建任务'
  })

  let res = await $http.upload('api/global/fileupload/upload', tempFilePath.path).catch(() => {})

  if (res?.data) {
    let res2 = await $http.post(url.value, { file_url: res.data }).catch(() => {})
    uni.hideLoading()

    if (res2?.data) {
      let taskInfo = {
        ...res2.data,
        status: '已创建',
        name: tempFilePath.name,
        file_urls: undefined,
        file_datas: undefined,
        showOptions: false
      }
      taskInfoList.value.unshift(taskInfo)
      getResult(taskInfo)
    } else{
      uni.showToast({
        title: '任务创建失败',
        icon: 'none'
      })
    }
  } else {
    uni.showToast({
      title: '图片上传失败',
      icon: 'none'
    })
  }
}

const getResult = async (taskInfo) => {
  let currentTaskInfo = taskInfoList.value.find(item => item.task_id === taskInfo.task_id)
  let res = await $http.get(`api/user/tools/pdf/convert_result/${taskInfo.office_type}/${taskInfo.task_id}`).catch(() => {})

  if (!res?.data) {
    // 失败处理
    currentTaskInfo.status = '处理失败';
    return
  }

  if (res.data.status === '已创建' || res.data.status === '处理中') {
    setTimeout(() => {
      getResult(currentTaskInfo)
    }, 500)
  }

  if (res.data.status === '处理成功') {
    if (channel.value === 'longpic') {
      let genUrl = await mergeImages(res.data.file_urls).catch(() => {})

      if (genUrl) {
        res.data.file_urls = [genUrl]

        // 保存结果
        $http.post('api/global/fileupload/save_result_file', {
          file_url: genUrl
        })
      }
    }
  }

  // 更新数据
  currentTaskInfo.status = res.data.status;
  currentTaskInfo.file_urls = res.data.file_urls;
  currentTaskInfo.file_datas = res.data.file_datas;
}

const mergeImages = async (urls) => {
  return new Promise(async (resolve, reject) => {
    let urlsInfoList = await getImageInfo(urls);
    let totalHeight = 0

    urlsInfoList.forEach(item => {
      let radio = 600 / item.width
      totalHeight += (item.height * radio)
    })

    const canvas = uni.createOffscreenCanvas({ width: 600, height: totalHeight , type: '2d' })

    if (!canvas) {
      uni.showToast({
        title: '合并图片失败',
        icon: 'none'
      })

      reject()
      return
    }

    const ctx = canvas.getContext('2d')

    ctx.fillStyle = '#fff'
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    let dy = 0

    for (let i = 0; i < urlsInfoList.length; i++) {
      const height = await createImgAndDraw(canvas, ctx, urlsInfoList[i].path, dy).catch(() => {})
      height && (dy += height)
    }

    ctx.restore()
    let base64 = canvas.toDataURL()

    const fs = uni.getFileSystemManager();
    const tempFilePath = `${uni.env.USER_DATA_PATH}/${new Date().getTime()}.png`;

    fs.writeFile({
      filePath: tempFilePath,
      data: base64.split(',')[1], // Remove the data URL prefix and decode Base64
      encoding: 'base64',
      success: async () => {
        let res = await $http.upload('api/global/fileupload/upload', tempFilePath).catch(() => {})

        if (res.data) {
          resolve(res.data)
        } else {
          reject(res)
        }
      },
      fail: (error) => {
        reject(error)
      }
    });
  })
}

const createImgAndDraw = async (canvas, ctx, url, dy) => {
  return new Promise((resolve) => {
    const img = canvas.createImage()
    img.src = url
    img.crossOrigin = "anonymous";
    img.referrerPolicy = "no-referrer";

    img.onload = () => {
      let height = (img.height / img.width) * 600
      ctx.drawImage(img, 0, dy, 600, height)
      resolve(height)
    }
  })
}

const getImageInfo = async (urls) => {
  let promiseArr = urls.map((url) => {
    return new Promise(async (resolve, reject) => {
      uni.getImageInfo({
        src: url,
        success: (res) => {
          resolve(res)
        },
        fail: (err) => {
          console.log("获取图片信息失败:", err);
          reject(err)
        },
      });
    })
  })

  return Promise.all(promiseArr)
}

const lookResult = (item) => {
  if (channel.value === 'word' ||
      channel.value === 'excel' ||
      channel.value === 'ppt'
  ) {
    let url = item.file_urls?.[0]

    if (url) {
      toRouter('/pages/ai-view/index', 'url=' + encodeURIComponent(url))
    } else {
      uni.showToast({
        title: '无转换内容',
        icon: 'none'
      })
    }
  }
  // 图片
  else if (channel.value === 'pic' || channel.value === 'longpic') {
    previewImg.value = item.file_urls
    previewIndex.value = 0
    showPreviewImg.value = true
  }
}

const save = () => {
  uni.showLoading({
    title: '正在下载'
  })

  if (channel.value === 'longpic') {
    const fs = uni.getFileSystemManager();
    const tempFilePath = `${uni.env.USER_DATA_PATH}/${new Date().getTime()}.png`;

    fs.writeFile({
      filePath: tempFilePath,
      data: previewImg.value[previewIndex.value].split(',')[1],
      encoding: 'base64',
      success() {
        uni.saveImageToPhotosAlbum({
          filePath: tempFilePath,
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
    });
  } else {
    let url = previewImg.value[previewIndex.value]
    let filename = url.split('/')[url.split('/').length - 1]
    let newFilePath = `${uni.env.USER_DATA_PATH}/${filename}`

    uni.downloadFile({
      url: url,
      filePath: newFilePath,
      success: () => {
        uni.saveImageToPhotosAlbum({
          filePath: newFilePath,
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
      complete: () => {
        uni.hideLoading()
      }
    });
  }
}
</script>

<style lang="scss">
.pdf-transform {
  padding-bottom: 120rpx;
  padding-top: 30rpx;

  .transform-title {
    padding-left: 32rpx;
    margin-bottom: 30rpx;
    font-size: 32rpx;
    font-weight: bold;
    color: #333333;
  }

  .select-box {
    display: flex;
    align-items: center;
    padding: 20rpx 36rpx;
    background: #ffffff;
    margin-bottom: 60rpx;
    color: #333333;
    font-size: 28rpx;

    image {
      height: 158rpx;
      margin-right: 42rpx;
    }

    .select-title {
      flex-grow: 1;
      font-size: 28rpx;
    }
  }

  .history {
    display: flex;
    flex-direction: column;

    .history-title {
      padding: 0 32rpx;
      font-size: 32rpx;
      font-weight: bold;
      color: #333333;
      margin-bottom: 44rpx;
    }

    .pic-list {
      display: flex;
      flex-direction: column;
      gap: 20rpx;

      .pic-item {
        padding: 20rpx 40rpx;
        background: #ffffff;

        .container {
          display: flex;
          align-items: center;

          image {
            height: 70rpx;
            width: 70rpx;
            border-radius: 8rpx;
            margin-right: 20rpx;
            flex-shrink: 0;
          }

          .name {
            flex-grow: 1;
            padding-right: 20rpx;
            font-size: 26rpx;
            word-break: break-all;
          }

          .status {
            color: #2bb3ed;
            flex-shrink: 0;
            font-size: 28rpx;
            display: flex;
            align-items: center;
            gap: 10rpx;
          }
        }
      }
    }
  }

  .bottom-options {
    box-sizing: border-box;
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 100rpx;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    background: #ffffff;
    padding: 0 52rpx;

    .left {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      span {
        color: #333333;
        font-size: 20rpx;
      }

      image {
        width: 50rpx;
      }
    }

    button {
      margin: 0;
      background: #448BFF;
      border-radius: 35rpx;
      width: 200rpx;
      height: 70rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #ffffff;
      font-size: 30rpx;
    }
  }

  .wd-popup {
    width: 600rpx !important;
    background: transparent !important;
    position: relative;

    .img-box {
      max-height: 70vh !important;
      overflow: auto !important;

      image {
        width: 100%;
        display: block;
      }
    }

    .arrow {
      text {
        color: #ffffff;
        background: rgba(0, 0, 0, 0.5);
        width: 90rpx;
        height: 90rpx;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 20rpx;

        &:nth-child(1) {
          position: absolute;
          left: 6rpx;
          top: 40%;
        }

        &:nth-child(2) {
          position: absolute;
          right: 6rpx;
          top: 40%;
        }
      }
    }

    .options {
      margin-top: 20rpx;
      display: flex;
      align-items: center;
      justify-content: space-between;

      button {
        width: 30%;
      }
    }
  }
}
</style>
