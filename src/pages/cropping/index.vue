<template>
  <view>
    <view class="water-mark" v-show="false">
      <wd-watermark
          ref="waterRef"
          image="data:image/png;base64,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"
          :width="100"
          :height="100"
          :imageWidth="60"
          :imageHeight="43"
          :rotate="0"
      ></wd-watermark>
    </view>

   <template v-if="showCropper && resultList.length">
     <jp-cropper-watermark
         ref="cropperRef"
         @cancel="oncancel"
         @ok="onok"
         :watermarkType="1"
         mode="free"
         :width="width"
         :height="height"
         :maxWidth="1024"
         :maxHeight="1024"
         :url="resultList[currentIndex].url"
         v-model:showCropBox="showCropBox"
         @submit1="submit1"
         @submit2="submit2"
         @reAdd="reAdd"
     />

     <view class="toggle">
       <view class="left" @click="toggleIndex('decrease')" :class="{ disabled: resultList.length === 1 ||currentIndex === 0 }">〈</view>
       <view class="number">
         <view>{{ currentIndex + 1 }}</view>
         <view>/</view>
         <view >{{ resultList.length }}</view>
       </view>
       <view class="right" @click="toggleIndex('add')" :class="{ disabled: resultList.length === 1 ||currentIndex === resultList.length - 1 }">〉</view>
     </view>
   </template>

    <template v-if="!showCropper">
      <view class="img-list">
        <view class="img-item" v-for="item of resultList" :key="item.url">
          <image :src="item.url" mode="aspectFit" />
        </view>
      </view>

      <view class="cropping-fixed">
        <view style="padding: 0 2rem; height: 50px; display: flex; align-items: center">
          <view @click="reAdd" style="flex-grow: 1; display: flex">
            <view style="display: flex; flex-direction: column; align-items: center; gap: 6rpx">
              <image
                  src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/xiangji.png"
                  style="height: 16px; width: 16px"
                  mode="heightFix"
              />
              <view style="font-size: 22rpx; ">重新拍摄</view>
            </view>
          </view>

          <view style="display: flex; align-items: center; gap: 20rpx;">
            <button class="submit" @click="submit">{{ submitText }}</button>
            <button class="share" @click="onShare">分享</button>
          </view>
        </view>
      </view>
    </template>
  </view>

  <Share :show="shareShow"></Share>

  <id-card ref="idCardRef" :propUrls="urls"/>
  <bank-card ref="bankCardRef" :propUrls="urls"/>
  <hukou ref="hukouRef" :propUrls="urls"/>
  <passport ref="passportRef" :propUrls="urls"/>
  <driver ref="driverRef" :propUrls="urls"/>
  <certificate ref="certificateRef" :propUrls="urls"/>
  <license ref="licenseRef" :propUrls="urls"/>
  <fileScan ref="fileScanRef" :propUrls="urls"/>
  <test-paper ref="testPaperRef" :propUrls="urls"/>
  <Jigsaw ref="jigsawRef" :propUrls="urls"/>
  <PicTransform ref="picTransformRef" :propsChannel="tab" :propUrls="urls"/>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import JpCropperWatermark from './jp-cropper-watermark.vue'
import { onLoad, onShareAppMessage, onShow } from '@dcloudio/uni-app'
import { toRouter, toSwich } from '@/hooks/utils'
import $http from '@/hooks/http'
import IdCard from '../camera/certificate/idCard.vue'
import BankCard from '../camera/certificate/bankCard.vue'
import Hukou from '../camera/certificate/hukou.vue'
import Passport from '../camera/certificate/passport.vue'
import Driver from '../camera/certificate/driver.vue'
import Certificate from '../camera/certificate/certificate.vue'
import License from '../camera/certificate/license.vue'
import FileScan from '../camera/fileScan/index.vue'
import TestPaper from '../test-paper/index.vue'
import Jigsaw from '../camera/jigsaw/index.vue'
import PicTransform from '../pic-transform/index.vue'
import { shareShow, shareUrl, taskId } from '@/section/share'
import Share from '@/section/share.vue'

const user = ref({})

const tab = ref(null);
const cerIndex = ref(null);

const width = ref(100);
const height = ref(100);
const urls = ref([]);
const currentIndex = ref(0);
const showCropper = ref(true);
const idCardRef = ref(null);
const bankCardRef = ref(null);
const hukouRef = ref(null);
const passportRef = ref(null);
const driverRef = ref(null);
const certificateRef = ref(null);
const licenseRef = ref(null);
const fileScanRef = ref(null);
const testPaperRef = ref(null);
const jigsawRef = ref(null);
const picTransformRef = ref(null);
const submitText = ref('完成');
const cropperRef = ref(null)
const waterRef = ref(null)
const showCropBox = ref(false);

onLoad(async (options) => {
  tab.value = options.tab;
  cerIndex.value = options.cerIndex;

  if (options.urls) {
    urls.value = options.urls.split(',')
  }

  $http.get('api/user/auth/userauth/info?referch=1').then(res => {
    let vip_info = res.data.vip_info

    user.value = {
      ...res.data,
      ...vip_info,
    };
  })
})

onMounted(() => {
  setTimeout(async () => {
    if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
      submitText.value = '正在转换'
      await picTransformRef.value.setResultList(urls.value)
    }

    width.value = resultList.value[currentIndex.value].width
    height.value = resultList.value[currentIndex.value].height
  }, 100)
})

onShow(() => {
  let globalData = getApp().globalData

  if (globalData.watermarkImg) {
    resultList.value[currentIndex.value].url = globalData.watermarkImg
    globalData.watermarkImg = undefined
  }
})

const resultList = computed(() => {
  if (tab.value === '4') {
    if (cerIndex.value === '1') {
      return idCardRef?.value?.resultList || []
    } else if (cerIndex.value === '2') {
      return bankCardRef?.value?.resultList || []
    } else if (cerIndex.value === '3') {
      return hukouRef?.value?.resultList || []
    } else if (cerIndex.value === '4') {
      return passportRef?.value?.resultList || []
    } else if (cerIndex.value === '5') {
      return driverRef?.value?.resultList || []
    } else if (cerIndex.value === '6') {
      return certificateRef?.value?.resultList || []
    } else if (cerIndex.value === '7') {
      return licenseRef?.value?.resultList || []
    }
  } else if (tab.value === '6') {
    return fileScanRef?.value?.resultList || []
  } else if (tab.value === '10') {
    return testPaperRef?.value?.resultList || []
  } else if (tab.value === '13') {
    return jigsawRef?.value?.resultList || []
  } else if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
    return picTransformRef?.value?.resultList || []
  }

  return []
})

const submit1 = async () => {
  uni.setNavigationBarTitle({
    title: ''
  })

  currentIndex.value = 0

  if (tab.value === '4') {
    if (cerIndex.value === '1') {
      await idCardRef.value.submit()
    } else if (cerIndex.value === '2') {
      await bankCardRef.value.submit()
    } else if (cerIndex.value === '3') {
      await hukouRef.value.submit()
    } else if (cerIndex.value === '4') {
      await passportRef.value.submit()
    } else if (cerIndex.value === '5') {
      await driverRef.value.submit()
    } else if (cerIndex.value === '6') {
      await certificateRef.value.submit()
    } else if (cerIndex.value === '7') {
      await licenseRef.value.submit()
    }
  } else if (tab.value === '6') {
    await fileScanRef.value.submit()
  } else if (tab.value === '10') {
    await testPaperRef.value.submit()
  } else if (tab.value === '13') {
    await jigsawRef.value.submit()
  }

  cropperRef.value.step = 2
}

const submit2 = async () => {
  if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
    await picTransformRef.value.submit()
    submitText.value = '预览'
  } else {
    uni.showLoading({
      title: '加载中...',
      mask: true,
    })

    let saveUrlItem = resultList.value[0]

    if (tab.value === '6') {
      await fileScanRef.value.mergePic()
      saveUrlItem = fileScanRef.value.mergePicUrl[0]
    }

    // 非VIP加水印分享
    if (!user.value.vip_type) {
      let watermarkPicInfo = await toTransformPics(saveUrlItem).catch(() => {})

      if (watermarkPicInfo) {
        saveUrlItem = watermarkPicInfo
      }
    }

    let file_url = saveUrlItem.url

    // 文件未上传就先进行上传处理
    if (!saveUrlItem.url.includes('hnenjoy.oss-cn-shanghai.aliyuncs.com')) {
      let res = await $http.upload('api/global/fileupload/upload', saveUrlItem.url).catch(() => {})

      if (res?.data) {
        file_url = res.data
      } else {
        uni.showToast({
          title: '图片上传失败',
          icon: 'none',
        })
      }
    }

    // 合成长图
    if (tab.value === '13') {
      let res = await $http.post('api/user/tools/pic/composite_long_image', {
        img_urls: [file_url]
      })

      if (res?.data) {
        taskId.value = res.data.task_id
      }
    }

    // 保存结果
    await $http.post('api/global/fileupload/save_result_file', {
      file_url: file_url,
      taskid: taskId.value
    }).catch(() => {})

    shareUrl.value = file_url
  }

  uni.hideLoading()
  showCropper.value = false
}

const submit = async () => {
  if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
    let url = picTransformRef.value.previewUrl
    let filesuffix = url.split('.')[url.split('.').length - 1]

    let filename = url.split('/')[url.split('/').length - 1]
    let newFilePath = `${uni.env.USER_DATA_PATH}/${filename}`

    if (url) {
      if (filesuffix.toLowerCase() === 'pdf' && uni.getDeviceInfo().platform === 'android') {
        uni.downloadFile({
          url: url,
          filePath: newFilePath,
          success: (res) => {
            uni.openDocument({
              filePath: newFilePath,
              showMenu: true,
              success: (res) => {
                console.log('打开成功', res)
              },
              fail: (res) => {
                console.log('打开失败', res)
              }
            })
            console.log('下载成功', res)
          },
          fail: (res) => {
            console.log('下载失败', res)
          }
        })
      } else {
        toRouter('/pages/ai-view/index', 'url=' + encodeURIComponent(picTransformRef.value.previewUrl))
      }
    } else {
      uni.showToast({
        title: '无转换内容',
        icon: 'none'
      })
    }
    return
  }

  toSwich('/pages/document/index')
}

const reAdd = () => {
  uni.navigateBack()
}

const onok = (ev) => {
  resultList.value[currentIndex.value].url = ev.path
};

const onShare = async () => {
  let url = ''

  if (tab.value === '4') {
    if (cerIndex.value === '1') {
      url = idCardRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '2') {
      url = bankCardRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '3') {
      url = hukouRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '4') {
      url = passportRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '5') {
      url = driverRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '6') {
      url = certificateRef?.value?.resultList?.[0]?.url
    } else if (cerIndex.value === '7') {
      url = licenseRef?.value?.resultList?.[0]?.url
    }
  } else if (tab.value === '6') {
    url = fileScanRef?.value?.resultList?.[0]?.url
  } else if (tab.value === '10') {
    url = testPaperRef?.value?.resultList?.[0]?.url
  } else if (tab.value === '13') {
    url = jigsawRef?.value?.resultList?.[0]?.url
  } else if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
    url = picTransformRef.value.previewUrl
  }

  if (tab.value === '14' || tab.value === '15' || tab.value === '16' || tab.value === '17') {
    uni.showLoading({
      title: '正在加载...'
    })

    let filename = url.split('/')[url.split('/').length - 1]
    let newFilePath = `${uni.env.USER_DATA_PATH}/${filename}`

    uni.downloadFile({
      url: url,
      filePath: newFilePath,
      success: () => {
        uni.shareFileMessage({
          filePath: newFilePath,
          success() {
            console.log('文件转发成功');
          },
          fail(res) {
            console.log('文件转发失败', res);
          },
          complete() {
            uni.hideLoading();
          }
        });
      },
      fail(res) {
        console.log('文件下载失败', res);
        uni.hideLoading();
      },
      complete: () => {
        uni.hideLoading();
      }
    });
  } else {
    shareShow.value = true
  }
}

const oncancel = () => {};

const toggleIndex = (type) => {
  if (type === 'add') {
    if (resultList.value.length === 1 ||currentIndex.value === resultList.value.length - 1) {
      uni.showToast({
        title: '已经是最后一张了',
        icon: 'none'
      })

      return
    }

    currentIndex.value += 1
  } else {
    if (resultList.value.length === 1 || currentIndex.value === 0) {
      uni.showToast({
        title: '已经是第一张了',
        icon: 'none'
      })

      return
    }

    currentIndex.value -= 1
    width.value = resultList.value[currentIndex.value].width
    height.value = resultList.value[currentIndex.value].height
  }
}

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/share.jpg',
    path: '/pages/index/index',
  }
})


const toTransformPics = (imgInfo) => {
  return new Promise((resolve, reject) => {
    const waterMarkUrl = waterRef.value.waterMarkUrl;

    if (uni.createOffscreenCanvas) {
      const canvasImg = uni.createOffscreenCanvas({
        height: imgInfo.height,
        width: imgInfo.width,
        type: "2d",
      });

      canvasImg.id = 'water-canvas'

      const ctxImg = canvasImg.getContext("2d");
      const img = canvasImg.createImage();
      img.crossOrigin = "anonymous";
      img.referrerPolicy = "no-referrer";
      img.src = imgInfo.url

      img.onload = () => {
        ctxImg.drawImage(img, 0, 0, imgInfo.width, imgInfo.height);
        ctxImg.restore();

        const img2 = canvasImg.createImage();
        img2.src = waterMarkUrl;

        img2.onload = () => {
          const numRows = Math.ceil(imgInfo.height / img2.height);
          const numColumns = Math.ceil(imgInfo.width / img2.width);

          for (var i = 0; i < numRows; i++) {
            for (var j = 0; j < numColumns; j++) {
              const x = j * img2.width;
              const y = i * img2.height;

              ctxImg.drawImage(img2, x, y, img2.width, img2.height);
            }
          }

          ctxImg.restore();

          let urlBase = canvasImg.toDataURL();

          const fs = uni.getFileSystemManager();
          const tempFilePath = `${uni.env.USER_DATA_PATH}/${new Date().getTime()}.png`;

          fs.writeFile({
            filePath: tempFilePath,
            data: urlBase.split(',')[1], // Remove the data URL prefix and decode Base64
            encoding: 'base64',
            success: async () => {
              let res = await $http.upload('api/global/fileupload/upload', tempFilePath).catch(() => {})

              if (res?.data) {
                resolve({
                  ...imgInfo,
                  url: res.data
                })
              } else {
                reject(res)
              }
            },
            fail: (error) => {
              reject(error)
            }
          });
        };
      };
    }
  })
};
</script>
<style>
page {
  background: #f7f8fc;
}
</style>
<style scoped lang="scss">
.cropping-img {
  height: calc(100vh - 100px);
  display: flex;
  align-items: center;
  justify-content: center;
}
.cropping-fixed {
  height: 50px;
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 9999;
  background: #fff;
  width: 100vw;
  border-top: 1px solid #eee;
  font-size: 0.85rem;
  --wot-button-primary-bg-color: #00D7AD;
}
.cropping-jp{
  overflow: auto;
  display: flex;
  align-items: center;
  font-size: 26rpx;
}

.submit {
  background: #ffffff;
  border: 2rpx solid #448BFF;
  border-radius: 999px;
  font-size: 28rpx;
  color: #448BFF;
  width: 180rpx;
  margin: 0;
  padding: 0;
}

.share {
  background: #448BFF;
  border-radius: 999px;
  font-size: 28rpx;
  color: #ffffff;
  width: 180rpx;
  margin: 0;
  padding: 0;
}

.cropping-jp .d-flex-column-center{
  flex-shrink: 0;
  flex-grow: 1;
}

.step2 {
  padding: 0 20rpx;

  image {
    width: 100%;
  }
}

.toggle {
  position: fixed;
  bottom: 140rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  // background: #ffffff;
  padding: 10rpx 40rpx;
  left: 50%;
  transform: translateX(-50%);
  z-index: 9999999;
  color: #2F2F46;
  font-size: 28rpx;

  .number {
    padding: 0 2rpx;
    flex-shrink: 0;
    white-space: nowrap;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 34rpx;
  }

  .left {
    font-weight: bold;
  }

  .right {
    font-weight: bold;
  }

  .disabled {
    color: #D9D9D9;
    border-radius: 10rpx;
  }
}

.img-list {
  display: flex;
  flex-wrap: wrap;
  padding: 40rpx;

  .img-item {
    background: #ffffff;
    width: 315rpx;
    box-shadow: 0 3px 3px #eee;
    height: 446rpx;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    border-radius: 20rpx;
    margin-bottom: 20rpx;

    &:nth-child(2n - 1) {
      margin-right: 40rpx;
    }

    image {
      height: 100%;
      border-radius: 20rpx;
    }
  }
}
</style>