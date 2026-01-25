<template>
  <view>
    <view class="water-mark" v-show="false">
      <wd-watermark
          ref="waterRef"
          image="data:image/png;base64,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UmVhZHlxyWU8AAADg2lUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4gPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNi4wLWMwMDMgMTE2LmRkYzdiYzQsIDIwMjEvMDgvMTctMTM6MTg6MzcgICAgICAgICI+IDxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4LW5zIyI+IDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bXBNTTpPcmlnaW5hbERvY3VtZW50SUQ9InhtcC5kaWQ6MWFjMjRhYTAtNTFhNy0yZDRjLWFhZjgtZTY1YmM0Y2FkZjNkIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjBFODA0OTE5RjExMDExRjBBRTU5OThBOEZERkJGODhCIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjBFODA0OTE4RjExMDExRjBBRTU5OThBOEZERkJGODhCIiB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIFBob3Rvc2hvcCAyMS4yIChXaW5kb3dzKSI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOjJjNDYxYzA5LTdjNDAtNjk0YS1iNmRmLTFkYzBhMmZiYzU5NiIgc3RSZWY6ZG9jdW1lbnRJRD0iYWRvYmU6ZG9jaWQ6cGhvdG9zaG9wOmJjY2JmNGRmLWZkNDAtNzA0Yy05NjVlLTRiZjM0YzU2NmY4YyIvPiA8L3JkZjpEZXNjcmlwdGlvbj4gPC9yZGY6UkRGPiA8L3g6eG1wbWV0YT4gPD94cGFja2V0IGVuZD0iciI/PhkQd6QAAAp2SURBVHja7FxNSBtbFL61roUB94UE94VI9y0Tuhci3bck5O0KSqSufWR4QneK4bkXQ7svhtd9MbT7koD7wkD3pe+e9Dvx5Hgmmhg1P/fAZUzmJ+18852f794zj37//u2CzacthVsQwA0WwA0WwA0WwA12O1se98S3b9+Gu/fA9v79+8Dc4JaDPbRt+HHmR82PlQd1y/flWubdfHh76jdVP2I/KtgSwLszD+4Cgxr5zZYfr/xI/ENewfdtvzn3o+nHt+CWZwvUFT/2/J8dPx77se6BbQhPlhLYYHNg7gy43IYHre0/b+EzsTIPIK1Q1fDHdhCHPwZwp5ChAJLc7JH//MlvV/0oevC6xvGx/74lvqog9o4NbnDLd2d1P1K43KIfLwloDawHNedHL0v227Jgb0tk0QHcKWLtAcVTD9COiKObfpQ1sCh/iNXbYKq0W8XeAO7dAVsxmPzF768LdhKLmcmUGX9S+4m9KbLqAO40Auu/P/KbH6hdyyiDZGxldlr7d8ZlbwD37oElV/vLf/8X3POhdL9gbwvsHLZ/L4D7MMBS7HxpAEsxNofYycLFTz9KOEfG1nd+RPhb72f2RgHcezawi+rYktpFnz/5/RdCuIhQHml2NsDenwZ7U7E/gDtBVm5QqUJbKU5QHCWXi3rWAZC6le1CkFiFcLEDJsYGe5md+9fsD+DeEtQtgFIVID1BbP0A9kXMMA/aZ4qNRq3ahnBRYUUKTCWV6shg5571sIj95QDu7WNoFaAURUny1Y8L/zkPcYJAfyUY1gNEZrvEVEu4QCzuKVPyWIgdW0Ldkkb/jjiAe/sYGgFQ6RYJ1ESxqYnYSp+/6XipQI1QFpFw8SXjWLr+MzxYidqXE3VxAPeGTLXiWB80ANeWQr+QDSnm1pAwOcRLXas6iBPk5n+A+XTcBYGlXDl5hK7B9hhu+vCm/6/lBQe0jEGM6s3S+O1zIRq0Mlx2TbhHcp15P04p6fLX+Oi3zMgddfqh37+L6+zBBae4VkO6X2I4xWnxe7R940aY411aUGA3RFlSBEtfI4H6lz77G7tpsIeYfA4wWnCdO8YcbJJRy1bhmhnYIhQqZyRiOeHCv+PYkWaIFtUtEwibItnpiptNozDkXJn9dtX3PHV3Xa3KyVoqMue6cuUJ2JqHqx/ZFtUttwT7HBKXRDCU3S8dkxOzO00A4Qwlqg63y9fcR30s52kTMLGoJupbeKD6rpzOgafYGhfchWMuVkf0S5EMS+F+C8NYTKDClRdwzqpIrJzFXj9oWU1bXIPOPcWxZYO9Ycov4+bHso5ErP3P/Zk03xwC1hN2tVTnZlz3TABPx+XBuioDRMmVrmXVdQpg8jbiqVa5YuQF0Tj//+U5BfW5lOqIrSg9ONZaWfAR2FwRypHlgnnpaUGA2mem388qErt5/twaAuwxvqZM+gAzSXzOC1VvLyZzWfMVGS9no1XLTSprCDUqAbC1jDq47S514qH1Mcd3gDkMWOmKI5FJj7289dG4nfWT6hWaxKJ0sQaYQNyRS0UFK0k82MXfbY6TDBDY0uQM2H8+B2N6yY7lxjP+Lbx2SiZor/3mHwDmGFh/zPFt7uPc9wqpNcB5DSxMxsJDdqkqqWorVhfB/DQrqULNWldTfVq2JBCOwdIzJE/b+O5ObWmGQd3SU2lD1gH3a0zSfzGp3jUSlVgkQ6mQA1O5tkkwvYOkR9a7OUiKBQPgPB6a4/u4R8szCOoG3G/XGWuAAQ4nJOtiP7nJDiU8NHmO8weYK5MjpUpFKFMSxNMazie2x7K0gdUgaf7NGbN4YNL7uldLswQqyo/e/KqhELGY0AFobTco/LNEyPOvA+Bmsd5dasgtXLuARCfBv+ORYii787EXti0Mc3UXXEYZU0J92MJoQk3qZabMLEpyyJWjNKIslNo2yjJOsx6sGM9ZcQfJUmsWCLE8xaCSK2SBPTHWAcuak+dVmX0FUaOW3OCkN6s+FSRXHwByhAeEZcmKAtnBLUez4u2WpxRYUoi+ova04mBOANEQmW8kpMCBh0Bovg1eE4XpOVoIfoprHWK8Y3CV9a6P4zXIBfVdy40w97pIzOW4mhig1gQQsahTc6JG1XLfETJVyd5ekxWtJ4Zbr8BT0CqIdUuadJdLTyMjMWoPidsPYlMpYgjm9tsc4TYp5p34seb+zHFWdUIzxHU3VWyl5OyEyxIsfPuZoTrJ33+BeD0pL7VYIgZKlaz50MfIRi9Qf8aKXdpSMK2W1aaBmvSZBlZNoKfTkAHPSylkrUXqZ65w2etKQKDVD6dq9WFbxM+a+r7XEI2/+01aUJ5OIWrIcqrhrq4nDuDeJDs2mGKt3Y2EitQ1Gpm/w306BeS6cK8DmTMWltNDkxNLab7r34eXSGclY76PmNtf8ZBhK2AUv06g1zQlfqcjlSiAc4qES0+jUeL06yaxWJxTx2/uiqnCQywy78diAJrDvo8TfKhnMubK9yodZByzhcSJRX9yn2uydSODvU3E0Lr6z1Y4FqsbWDCybs3eiADFwrjPKjanyIaLkwR2Vt3yAWrFBMnPYzfYxMTAr7nBFYRSZGDA9uGKYxX7CKAVA2A6X6+eOGOXj+OtRK2cwQ7KoJOM2aaFA5fbGdfVwrCqADVTH+ZzDPbqSXZi1GoGIO0M8eEIIOqYOVKbxiKD21sqqpjGDcR9Nl+jz2r2XlmLBO1Yr+qPkZjFKgmi3/oBKTIxJt5HatNYdLdsMk2xWYOSU+yNVKw8dKIrTjwEOnk7d4PLXNh43jcx4vFIbRqLDm7LYFpqgFrg1/QYD8NAHAR7rRbJpnFejN7ZWEiFsVF61fHAbE9SdZpncDkDbrurLYj9bBXx7wgSYFGLA0IweKo9giFSaCu6y97ZEs47QWIViaasX5ATj90c2vKEQa26yx6abgawZdx0uuEVUauxK68YsZen377580+c3WTlVIzX+z+jpuzgt/LTJvRPK3P7jU1WBmy5bKdaJFDy3IS9WS2Sp3DjjSGg7wLU3XkHdlLgRu5ysbf1TsOyTIxE41V8w0RMZ85pxnFdqEfpNarO3IM6SXDNm40MmNccaYGgaWW0I7B3aIukCzZRt9y/2aKPhsDeRuwrKRCHvbjD2qfZO6xFshxgnSy4fLPPRbLEzcIpSpjSTdjLAKmMuGEweh+qU0FkwENjbgB3fKObnXd2B7gl7+l1w9cx0HoYWNjgsia/SDH1vuvcrBtrAWnquWgPKRvihJUcHaNMWpgM+KHq3GHGjVeZJl4R3xqFgRkiRjA3JasfwdY1l/2KeM648wGy6QOXu+pcBnAsgFj6M7lu7owLbvcBY+4o4HJnOn1Pr4HXXXQ5qE6Ufb+ZV/13XpirEyRu1nJYv3SAciZxl90Ehyh5gk0puFSvxsYEeckNivs7YGkHoIb4OgPgXhEWIGi0ZOkE1YkYvB9i62yBWzRY28wobQKwM5JQ9V63o9ogcxkxONiMMZfc8QEmEvjVeqw9B5sDt/wXMt+SKHdCsjRHpRC/zqDiVG/rpFpBgz2ciCEtJExzDG6we7Kxu/yCBeYGC+AGC+AGC+AGC+AGcIMFcIMFcIPdp/0vwACC0wm+8HVH3wAAAABJRU5ErkJggg=="
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
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/share.png',
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