<template>
  <view class="index-top">
    <view class="page-title">
      <text>首页</text>
    </view>

    <view class="banner"> </view>

    <view class="index-header">
      <image
          class="head"
          :src="user.avatar_url || `https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png`"
          mode="aspectFit"
      />

      <view class="userinfo" v-if="user.uid">
        <view class="name1">{{ user.nickname || '微信用户' }}</view>
        <!-- TODO 到期时间 -->
        <view class="name2">{{ user.vip_type ? `会员到期还剩159天` : '普通用户' }}</view>
      </view>

      <view class="userinfo" v-else @click="toRouter('/pages/login/index')">
        <view class="name1">未登录</view>
        <view class="name2">游客</view>
      </view>

      <view class="vip" @click="myVip">
        <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/index/vip-icon.png" />
        <text>{{ user.vip_type ? '我的会员' : '加入会员' }}</text>
      </view>
    </view>

    <view class="box">
      <view class="tool-box">
        <view class="left">
          <view class="top">
            <view class="add-icon">+</view>

            <view class="tip1">
              <text>文件扫描</text>
              <text>扫描生成电子文件</text>
            </view>
          </view>

          <view class="bottom">
            <view class="recode-tip">
              <!-- TODO 切图 -->
              <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png" />
              <!-- TODO 数量 -->
              <text class="num">文件记录12</text>
              <text class="more">更多></text>
            </view>

            <view class="recode-pic">
              <text>WORD</text>
              <text>PPT</text>
              <text>PDF</text>
            </view>
          </view>
        </view>

        <view class="right">
          <view class="right-card">
            <!-- TODO 切图 -->
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png" />

            <view class="desc">
              <text>证件扫描</text>
              <text>扫描证件生成电子文件</text>
            </view>
          </view>

          <view class="right-card">
            <!-- TODO 切图 -->
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/default-head.png" />

            <view class="desc">
              <text>图片转pdf</text>
              <text>扫描证件生成电子文件</text>
            </view>
          </view>
        </view>
      </view>

      <view class="index-card">
        <view class="tools">
          <view class="tools-title">常用工具</view>

          <view class="card-grid">
            <view class="card-grid-wrapper" @click="toRouter('/pages/camera/index', 'tab=5')">
              <view class="card-grid_li">
                <wd-img
                    height="27"
                    mode="heightFix"
                    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/icon02.png"
                ></wd-img>
              </view>
              <view class="span"> 提取文字 </view>
            </view>

            <view class="card-grid-wrapper" @click="toRouter('/pages/picTransform/picTransform')">
              <view class="card-grid_li">
                <wd-img
                    height="27"
                    mode="heightFix"
                    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/icon03.png"
                ></wd-img>
              </view>
              <view class="span"> 图片转换 </view>
            </view>

            <view class="card-grid-wrapper" @click="toRouter('/pages/pdfTransform/pdfTransform')">
              <view class="card-grid_li">
                <wd-img
                    height="27"
                    mode="heightFix"
                    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/icon04.png"
                ></wd-img>
              </view>
              <view class="span"> PDF转换 </view>
            </view>

            <view class="card-grid-wrapper" @click="toSwich('/pages/tool/index')">
              <view class="card-grid_li">
                <wd-img
                    height="27"
                    mode="heightFix"
                    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/icon05.png"
                ></wd-img>
              </view>
              <view class="span"> 更多 </view>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>

  <view class="index-content" style="position: relative; z-index: 10">
    <view class="file-list-wrapper">
      <view class="card-top">
        <view class="tool-title">
          <text class="title-word">最近文档</text>
        </view>

        <view class="more" @click="toSwich('/pages/document/index')">查看更多 〉</view>
      </view>

      <view class="card-content">
        <view
            class="file-item"
            :class="{ 'd-active': item.id == tindex }"
            v-for="(item, index) in files"
            :key="index"
            @click="goPreview(item)"
        >
          <view
              @click.stop="(tindex = (tindex === item.id ? -1 : item.id)), (currentItem = (currentItem === item ? null : item))"
              class="doc-more"
          >
            <view class="more-dot" v-if="tindex === item.id"></view>
            <view class="more-dot1" v-else></view>
          </view>

          <view class="left" :style="{ justifyContent: 'center', gap: '12rpx' }">
            <view class="filename">
              {{ item.file_name }}
            </view>

            <view
                class="time"
                v-if="item.create_at"
            >
              <text>更新时间：{{ item.create_at }}</text>
            </view>
          </view>

          <view class="right">
            <image
                v-if="
                            item.file_name.includes('.') &&
                            /\.(jpg|jpeg|png|gif|bmp|webp|svg)$/i.test(item.file_name)
                          "
                mode="aspectFit"
                :src="item.file_url"
            />
            <wd-icon v-if="/\.(doc|docx)$/i.test(item.file_name)" class-prefix="icon" custom-class="iconfont" name="word1" size="60" color="#0072FF"></wd-icon>
            <wd-icon v-else-if="/\.(xls|xlsx)$/i.test(item.file_name)" class-prefix="icon" custom-class="iconfont" name="excel" size="60" color="#00C650"></wd-icon>
            <wd-icon v-else-if="/\.(ppt|pptx)$/i.test(item.file_name)" class-prefix="icon" custom-class="iconfont" name="ppt" size="60" color="#FF3E4C"></wd-icon>
            <wd-icon v-else-if="/\.(pdf)$/i.test(item.file_name)" class-prefix="icon" custom-class="iconfont" name="pdf" size="60" color="#FF3E4C"></wd-icon>
          </view>
        </view>

        <view class="empty" v-if="files.length === 0 || !user.uid">
          <view class="empty-box">
            <image
                class="empty-icon"
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/empty.png"
            />

            <view class="add-box">
              <view class="tip" v-if="user.uid">暂无文档</view>

              <template v-else>
                <view style="font-size: 22rpx;color: #656565; margin-bottom: 25rpx;">登录扫描账号，查看账号内同步文档</view>
                <view class="login" @click="toRouter('/pages/login/index')">登录</view>
              </template>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>

  <view class="select-options" v-if="selectFile">
    <view class="select-options-container">
      <view @click.stop="showEditFileDialog = true" class="select-option">
        <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/option02.png"
        />

        <view>编辑</view>
      </view>

      <view @click.stop="showDeleteDialog = true" class="select-option">
        <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/option03.png"
        />

        <view>删除</view>
      </view>

      <view @click.stop="toShareFile(selectFile)" class="select-option1">
        <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/btn02.png"
        />
      </view>
    </view>
  </view>

  <wd-popup v-model="showEditFileDialog" @close="showEditFileDialog = false" custom-style="background: transparent">
    <view class="edit-file" v-if="selectFile">
      <image
          class="close"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/close2.png"
          @click="showEditFileDialog = false"
      />

      <view class="title">编辑名称</view>
      <view class="input-box">
        <input type="text" v-model="selectFile.file_name" />
        <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/close.png"
            @click="selectFile.file_name && (selectFile.file_name = selectFile.file_formmat)"
        />
      </view>
      <view class="options">
        <view class="confirm" @click="toEdit(selectFile)">确定</view>
      </view>
    </view>
  </wd-popup>

  <wd-popup v-model="showDeleteDialog" @close="showDeleteDialog = false" custom-style="background: transparent">
    <view class="delete-dialog">
      <image
          class="close"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/close2.png"
          @click="showDeleteDialog = false"
      />

      <view class="title">是否确定删除该文件，文件删除后无法找回。</view>
      <view class="options">
        <view class="cancel" @click="showDeleteDialog = false">取消</view>
        <view class="confirm" @click="toDelete(selectFile)">确定</view>
      </view>
    </view>
  </wd-popup>

  <wd-gap height="2rem"></wd-gap>
  <wd-popup
    v-model="show"
    position="bottom"
    custom-style="height: 250px; z-index: 1000;"
    @close="show = false"
  >
    <view class="popup-close">
      <wd-icon
        @click="show = false"
        name="close-circle"
        size="24"
        color="#888"
      ></wd-icon>
    </view>
    <view class="popup-grid">
      <view @click="toRouter('/pages/pic-transform/index', 'type=pic&channel=16')" class="popup-grid_li">
        <image
            style="width: 85rpx"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/pdf.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转PDF</view>
      </view>

      <view class="popup-grid_li">
        <image
            @click="toRouter('/pages/camera/index?tab=13&from=tool')"
            style="width: 85rpx"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/pintu.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">拼图</view>
      </view>

      <view @click="toRouter('/pages/pic-transform/index', 'type=pic&channel=17')" class="popup-grid_li">
        <image
            style="width: 85rpx"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/ppt.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转PPT</view>
      </view>

      <view @click="toRouter('/pages/pic-transform/index', 'type=pic&channel=14')" class="popup-grid_li">
        <image
            style="width: 85rpx"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/word.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转Word</view>
      </view>

      <view @click="toRouter('/pages/pic-transform/index', 'type=pic&channel=15')"  class="popup-grid_li">
        <image
          style="width: 85rpx"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/excel.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转Excel</view>
      </view>
    </view>
  </wd-popup>

  <wd-popup
    v-model="pdfShow"
    position="bottom"
    custom-style="height: 260px; z-index: 1000;"
    @close="pdfShow = false"
  >
    <view class="popup-close">
      <wd-icon
        @click="pdfShow = false"
        name="close-circle"
        size="24"
        color="#888"
      ></wd-icon>
    </view>
    <view class="popup-grid">
      <view class="popup-grid_li" @click="toRouter('/pages/pdf-transform/index', 'type=pdf&channel=pic')">
        <image
          style="width: 85rpx"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/img.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转图片</view>
      </view>

      <view class="popup-grid_li" @click="toRouter('/pages/pdf-transform/index', 'type=pdf&channel=excel')">
        <image
            style="width: 85rpx"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/excel.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转Excel</view>
      </view>

      <view class="popup-grid_li" @click="toRouter('/pages/pdf-transform/index', 'type=pdf&channel=longpic')">
        <image
          style="width: 85rpx"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/longimg.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转长图</view>
      </view>
      <view @click="toRouter('/pages/pdf-transform/index', 'type=pdf&channel=ppt')" class="popup-grid_li">
        <image
          style="width: 85rpx"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/ppt.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转PPT</view>
      </view>

      <view class="popup-grid_li" @click="toRouter('/pages/pdf-transform/index', 'type=pdf&channel=word')">
        <image
          style="width: 85rpx"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon1/word.png"
        ></image>
        <view style="margin-top: 0.6rem; font-size: 28rpx; color: #333333">转Word</view>
      </view>
    </view>
  </wd-popup>
  <wd-message-box selector="wd-delete-box-slot"></wd-message-box>
  <wd-message-box selector="wd-add-box-slot">
    <view>
      <wd-input v-model="fileName" placeholder="请输入文件夹名称"></wd-input>
    </view>
  </wd-message-box>

  <wd-message-box selector="wd-edit-box-slot"></wd-message-box>
  <NavBar :index="1"></NavBar>
  <Share :show="shareShow"></Share>

  <wd-popup
      :z-index="999999"
      custom-style="max-height: calc(100vh - 44px); background: #262626;"
      v-model="ustate"
      position="bottom"
  >
    <Util @changeTab="onTabs" :toolType="toolType"></Util>
  </wd-popup>
</template>

<script setup>
import { ref, watchEffect } from 'vue'
import NavBar from "@/section/a-navbar.vue";
import { toRouter, toSwich } from "@/hooks/utils";
import { deleteFile } from '../document/document'
import { useMessage } from '/node_modules/wot-design-uni'
import { onShow } from '@dcloudio/uni-app'
import { onLoad, onShareAppMessage } from '@dcloudio/uni-app'
import { shareShow, shareUrl, state, docUrl} from '@/section/share'
import Share from '@/section/share.vue'
import $http from '@/hooks/http'
import Util from '../camera/util2.vue'
import { ustate } from '../camera/camera'

const addMessage = useMessage('wd-add-box-slot')
const deleteMessage = useMessage('wd-delete-box-slot')
const editMessage = useMessage('wd-edit-box-slot')

const files = ref([]), tindex = ref(-1), currentItem = ref(null), fileUrl = ref(uni.getStorageSync('username'));
const show = ref(false), dictMitem = ref(null), fileName = ref('');
const pdfShow = ref(false),editValue = ref('');
let user = ref({});
let keyword = ref(undefined)
const toolType = ref(1)
const picList = ref([])

let MenuButtonInfo = uni.getMenuButtonBoundingClientRect()
const headerTop = ref(MenuButtonInfo.top + MenuButtonInfo.height + 'px')
const showEditFileDialog = ref(false)
const showDeleteDialog = ref(false)

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

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/share.png',
    path: '/pages/index/index',
  }
})

onShow(() => {
  initFile()
})

// onShareAppMessage((res) => {
//   return {
//     title: "分享",
//     path: "/pages/preview/index?shareUrl=" + encodeURIComponent(shareUrl.value) + '&shareType=file',
//     imageUrl: shareUrl.value,
//   };
// })

const selectFile = ref()

watchEffect(() => {
  let file = files.value.find(item => item.id === tindex.value)

  if (file) {
    selectFile.value = JSON.parse(JSON.stringify(file))
  } else {
    selectFile.value = undefined
  }
})

const myVip = () => {
  if (user.uid) {
    if (user.value.vip_type) {
      toRouter('/pages/my/index')
    } else {
      toRouter('/pages/member/index')
    }
  } else {
    toRouter('/pages/login/index')
  }
}

const search = () => {
  if (!user.value.uid) {
    uni.showModal({
      title: '提示',
      content: '您当前未登录或登录已失效，为了您有更好的体验，悦看需要您进行登录',
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

  initFile()
}

const choosePDFFile = () => {
  uni.chooseMessageFile({
    count: 1,
    type: 'file',
    extension: ['pdf'],
    success: async (response) => {
      picList.value = response.tempFiles

      toolType.value = 1
      ustate.value = true
    },
    fail: (err) => {
      console.log(err);
    }
  });
}

const chooseLocalPicture = () => {
  uni.chooseImage({
    count: 9,
    sizeType: ["original", "compressed"],
    sourceType: ["album"],
    success: (res) => {
      if (typeof res.tempFilePaths === 'string') {
        picList.value = [res.tempFilePaths]
      } else {
        picList.value = res.tempFilePaths
      }

      toolType.value = 2
      ustate.value = true
    },
    fail: (err) => {
      console.error("选择本地图片失败：", err);
    },
  });
}

const onTabs = (item) => {
  if (toolType.value === 1) {
    toRouter('/pages/pdf-transform/index', item.url + '&url=' + picList.value[0].path)
  } else {
    getCount(item.index)
  }
}

const getCount = async (tab) => {
  let action_name = ''
  let url = ''

  if (tab === 4) {
    action_name = 'ScanCertificate'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 5) {
    action_name = 'TextExtraction'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 6) {
    action_name = 'ScanFile'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 7) {
    action_name = 'PhotoCounting'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 8) {
    action_name = 'HandwriteExtraction'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 9) {
    action_name = 'PhotoTranslate'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 10) {
    action_name = 'PhotoRemoveHandwrite'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 11) {
    action_name = 'IdentifyingFormulas'
    url = 'api/user/tools/scan/tools_left'
  } else if (tab === 12) {
    action_name = 'AddWatermark'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 13) {
    action_name = 'CompositeLongImage'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 14) {
    action_name = 'ImgToWord'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 15) {
    action_name = 'ImgToExcel'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 16) {
    action_name = 'ImgToPdf'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 17) {
    action_name = 'ImgToPPT'
    url = 'api/user/tools/pic/tools_left'
  } else if (tab === 18) {
    action_name = 'RemoveWatermark'
    url = 'api/user/tools/pic/tools_left'
  }

  let res = await $http.get(`${url}/${action_name}`, {}, {
    showLoginModal: true
  }).catch(() => {})

  if (res?.data) {
    // 剩余次数
    if (res.data.left <= 0) {
      uni.showToast({
        title: '该功能可用次数为0，请重新选择',
        icon: 'none',
      })
    }
    else {
      if (picList.value.length == 0) {
        uni.showToast({
          title: '请先选择图片',
          icon: 'none'
        })
        return
      }

      ustate.value = false

      // 扫描服务-证件扫描
      if (tab === 4) {
        toRouter("/pages/cropping/index", "urls=" + picList.value.slice(0, 2).join(',') + '&tab=4&cerIndex=1');
        return
      }

      // 扫描服务-文字提取/手写文字识别
      if (tab === 5 || tab === 8) {
        toRouter('/pages/text-extraction/index', 'url=' + picList.value[0] + `&tab=${tab}`)
        return
      }

      // 扫描服务-文件扫描，最多上传9张
      if (tab === 6) {
        toRouter("/pages/cropping/index", "urls=" + picList.value.join(',') + '&tab=6');
        return
      }

      // 扫描服务-拍照计数 TODO 计数接口未返回数据
      if (tab === 7) {
        toRouter('/pages/detect-view/index', 'url=' + picList.value[0])
        return
      }

      // 扫描服务-拍照翻译
      if (tab === 9) {
        toRouter('/pages/translate/index', 'url=' + picList.value[0])
        return
      }

      // 扫描服务-试卷去手写
      if (tab === 10) {
        toRouter("/pages/cropping/index", "urls=" + picList.value.join(',') + '&tab=10');
        return
      }

      // 扫描服务-识别公式
      if (tab === 11) {
        toRouter('/pages/mathjax/index', 'url=' + picList.value[0] + '&type=gongshi')
        return
      }

      // 图片工具-图片加水印
      if (tab === 12) {
        toRouter('/pages/watermark/index', 'url=' + picList.value[0] + '&type=watermark')
        return
      }

      // 图片工具-拼图，最多上传9张
      if (tab === 13) {
        toRouter("/pages/cropping/index", "urls=" + picList.value.join(',') + '&tab=13');
        return
      }

      // 图片工具-图片转word/excel/ppt/pdf，最多上传9张
      if (tab === 14 || tab === 15 || tab === 16 || tab === 17) {
        toRouter("/pages/cropping/index", "urls=" + picList.value.join(',') + `&tab=${tab}`);
        return;
      }
    }
  }
}

const toShareFile = (item) => {
  uni.showLoading({
    title: '正在分享...'
  })

  let newFilePath = `${uni.env.USER_DATA_PATH}/${item.file_name}`

  console.log('newFilePath', newFilePath)

  uni.downloadFile({
    url: item.file_url,
    filePath: newFilePath,
    success: () => {
      uni.shareFileMessage({
        filePath: newFilePath,
        success() {
          tindex.value = -1
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
    }
  });
}

const initFile = () => {
  uni.showLoading({
    title: '正在加载'
  })

  $http.post('api/user/hdfs/file/file/list', {
    folder_id: 0,
    key: keyword.value
  }).then((res) => {
    files.value = res.data?.slice(0, 6);
  }).catch(() => {
    files.value = []
  }).finally(() => {
    uni.hideLoading()
  })
}

const toDelete = (item) => {
  uni.showLoading({
    title: '加载中',
    mask: true
  })

  deleteFile('api/user/hdfs/file/file/delete', {
    folder_id: 0,
    id: item.file_path_id
  }).then(() => {
    showDeleteDialog.value = false
    tindex.value = -1
    initFile()
  }).finally(() => {
    uni.hideLoading();
  })
}

const toEdit = (item) => {
  uni.showLoading({
    title: '加载中',
    mask: true
  })

  if (!item.file_name.includes('.')) {
    $http.post('api/user/hdfs/file/folder/rename', {
      id: item.file_path_id,
      folder_name: selectFile.value.file_name
    }).then(() => {
      tindex.value = -1
      showEditFileDialog.value = false
      initFile()
    }).finally(() => {
      uni.hideLoading();
    })
  } else {
    $http.post('api/user/hdfs/file/file/rename', {
      id: item.file_path_id,
      file_name: selectFile.value.file_name
    }).then(() => {
      tindex.value = -1
      showEditFileDialog.value = false
      initFile()
    }).finally(() => {
      uni.hideLoading();
    })
  }
};

const goPreview = (item) => {
  if( /\.(jpg|jpeg|png|gif|bmp|webp|svg)$/i.test(item.file_name) ){
    toRouter('/pages/preview/index', `previewFileUrl=${encodeURIComponent(item.file_url)}&type=img&title=${item.file_name}`)
  } else {
    if (item.file_formmat === '.pdf' && uni.getDeviceInfo().platform === 'android') {
      let filename = item.file_url.split('/')[item.file_url.split('/').length - 1]
      let newFilePath = `${uni.env.USER_DATA_PATH}/${filename}`

      uni.downloadFile({
        url: item.file_url,
        filePath: newFilePath,
        success: (res) => {
          uni.openDocument({
            filePath: newFilePath,
            showMenu: true,
            success: (res) => {
              console.log("打开成功", res);
            },
            fail: (res) => {
              console.log("打开失败", res);
            },
          });
          console.log("下载成功", res);
        },
        fail: (res) => {
          console.log("下载失败", res);
        },
      });
    } else {
      toRouter('/pages/ai-view/index', 'url=' + encodeURIComponent(item.file_url))
    }
  }
}
</script>

<style lang="scss">
page {
  background: linear-gradient(-90deg, #44CDD3 0%, #8FD8ED 0%, #73E0E2 0%, #5BA7F4 51%, #5191FF 100%) left top/100% 782rpx no-repeat;
  padding-bottom: 200rpx;
}
</style>

<style lang="scss" scoped>
page {
}
.index-card {
  --wot-card-margin: 0;
}
.logo {
  height: 200rpx;
  width: 200rpx;
  margin-top: 200rpx;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 50rpx;
}

.text-area {
  display: flex;
  justify-content: center;
}
.title {
  font-size: 36rpx;
  color: #8f8f94;
}
.index-top {
  // padding: 0.8rem;
  --wot-card-margin: 0;
}
.search {
  display: flex;

  :deep(.wd-input) {
    flex: 1;
    height: 2.5rem;
    border-radius: 2rem;
    display: flex;
    padding-left: 0.5rem;
    align-items: center;
  }
}
.vip {
  display: flex;
  align-items: center;
  margin: 0 0 0 0.8rem;
}

.page-title {
}

.banner {
  padding: calc(var(--page-title-height)) 0 52rpx;
}

.index-header {
  padding: 0 30rpx;
  display: flex;
  align-items: center;
  margin-bottom: 53rpx;

  .head {
    width: 100rpx;
    height: 100rpx;
    border-radius: 50%;
    margin-right: 20rpx;
  }

  .userinfo {
    display: flex;
    flex-direction: column;
    gap: 12rpx;
    flex-grow: 1;

    .name1 {
      font-weight: bold;
      font-size: 30rpx;
      color: #FFFFFF;
    }

    .name2 {
      font-size: 24rpx;
      color: #FFFFFF;
    }
  }

  .vip {
    position: relative;

    image {
      width: 178rpx;
    }

    text {
      font-size: 24rpx;
      color: #FFFFFF;
      position: absolute;
      top: 20rpx;
      right: 16rpx;
    }
  }
}

.box {
  padding: 0 30rpx;

  .tool-box {
    display: flex;
    align-items: center;
    justify-content: space-between;

    .left {
      width: 335rpx;
      background: linear-gradient(0deg, #75A9FE 0%, #4A8EFD 100%);
      border-radius: 30rpx;
      box-sizing: border-box;
      padding: 40rpx 0 20rpx;

      .top {
        display: flex;
        align-items: center;
        justify-content: center;
        margin-bottom: 36rpx;

        .add-icon {
          width: 60rpx;
          height: 58rpx;
          background: #80AFFF;
          border-radius: 20rpx;
          font-size: 36rpx;
          color: #FFFFFF;
          margin-right: 18rpx;
          display: flex;
          align-items: center;
          justify-content: center;
        }

        .tip1 {
          display: flex;
          flex-direction: column;

          text {
            &:nth-child(1) {
              font-weight: 500;
              font-size: 28rpx;
              color: #FFFFFF;
            }

            &:nth-child(2) {
              font-size: 22rpx;
              color: #D5E5FF;
            }
          }
        }
      }

      .bottom {
        padding: 32rpx 22rpx 0;
        width: 100%;
        background: #70A6FF;
        border-radius: 30rpx;
        box-sizing: border-box;

        .recode-tip {
          display: flex;
          align-items: center;
          font-size: 24rpx;
          color: #FFFFFF;
          margin-bottom: 30rpx;

          image {
            width: 22rpx;
            margin-right: 10rpx;
          }

          .num {
            flex-grow: 1;
            color: #D5E5FF;
          }
        }

        .recode-pic {
          display: flex;
          align-items: center;
          gap: 10rpx;

          text {
            width: 80rpx;
            height: 53rpx;
            background: #9EC2FF;
            border-radius: 15rpx;
            font-size: 22rpx;
            color: #FFFFFF;
            display: flex;
            align-items: center;
            justify-content: center;
          }
        }
      }
    }

    .right {
      align-self: stretch;
      width: 335rpx;
      display: flex;
      flex-direction: column;
      justify-content: space-between;

      .right-card {
        background: #FFFFFF;
        border-radius: 30rpx;
        padding: 36rpx 0;
        display: flex;
        align-items: center;
        justify-content: center;

        image {
          width: 55rpx;
          margin-right: 24rpx;
        }

        .desc {
          display: flex;
          flex-direction: column;
          gap: 10rpx;

          text {
            &:nth-child(1) {
              font-weight: 500;
              font-size: 28rpx;
              color: #333333;
            }

            &:nth-child(2) {
              font-size: 22rpx;
              color: #999999;
            }
          }
        }
      }
    }
  }

  .tools {
    .tools-title {
      font-weight: 500;
      font-size: 28rpx;
      color: #1F1F1F;
      margin-bottom: 47rpx;
    }
  }
}

.index-content {
}
.card-title {
  display: flex;
  padding: 10rpx 20rpx 0;

  .name {
    flex: 1;
    font-weight: bold;
    font-size: 1rem;
  }
}
.card-grid {
  padding: 0 33rpx;
  display: flex;
  align-items: center;
  justify-content: space-between;

  > view {
    margin-bottom: 53rpx;
  }

  .card-grid_li {
    display: flex;
    justify-content: center;
    position: relative;

    .hot {
      position: absolute;
      top: 0;
      left: 6rpx;
      width: 48rpx;
    }
  }

  .span {
    display: flex;
    justify-content: center;
    margin-top: 22rpx;
    font-size: 26rpx;
    color: #000000;
  }
}

.file-list-wrapper {
  padding: 0 35rpx;

  .card-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 40rpx;

    .tool-title {
      display: flex;
      align-items: center;

      .title-word {
        font-weight: 500;
        font-size: 28rpx;
        color: #1F1F1F;
      }
    }

    .more {
      font-size: 24rpx;
      color: #BBBBBB;
    }
  }

  .card-title1 {
    padding-bottom: 26rpx;
    border-bottom: 2rpx solid #E7E7E7;

    .tip {
      text-align: center;
      font-size: 18rpx;
      color: #000000;
      margin-bottom: 35rpx;
    }

    .options {
      display: flex;
      align-items: center;
      justify-content: space-between;

      .option-item {
        width: 315rpx;
        height: 73rpx;
        background: #FFFFFF;
        border-radius: 15rpx;
        border: 2rpx solid #E5E5E5;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 24rpx;

        image {
          width: 22rpx;
        }

        text {
          font-size: 23rpx;
          color: #000000;
        }
      }
    }
  }

  .recent-title {
    font-weight: 500;
    font-size: 36rpx;
    color: #000000;
    padding: 30rpx 0;
  }

  .card-content {
    display: flex;
    flex-direction: column;
    gap: 40rpx;

    .empty {
      padding: 0 0 80rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      top: -40rpx;

      .empty-box {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        .empty-icon {
          width: 284rpx;
        }
      }

      .login-box, .add-box {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        .tip {
          font-weight: 500;
          font-size: 25rpx;
          color: #9E9E9E;
        }

        .login {
          width: 157rpx;
          height: 55rpx;
          background: #856BFF;
          border-radius: 15rpx;
          font-weight: 500;
          font-size: 21rpx;
          color: #ffffff;
          display: flex;
          align-items: center;
          justify-content: center;
        }

        .icon1 {
          width: 37rpx;
        }
      }
    }

    .file-item {
      display: flex;

      .doc-more {
        flex-shrink: 0;
        position: relative;
        top: 20rpx;

        .more-dot {
          width: 28rpx;
          height: 28rpx;
          border-radius: 50%;
          border: none;
          background: url("https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/icon06.png") left top/100% 100% no-repeat;
        }

        .more-dot1 {
          width: 24rpx;
          height: 24rpx;
          background: #FFFFFF;
          border-radius: 50%;
          border: 2rpx solid #856BFF;
        }
      }

      .left {
        align-self: stretch;
        flex-grow: 1;
        display: flex;
        flex-direction: column;

        .filename {
          font-size: 24rpx;
          color: #1F1F1F;
        }

        .time {
          font-size: 24rpx;
          color: #9C9C9E;
          display: flex;
          align-items: center;

          image {
            width: 20rpx;
            margin-right: 12rpx
          }
        }
      }

      .right {
        flex-shrink: 0;
        height: 140rpx;
        width: 140rpx;
        margin-right: 18rpx;
        display: flex;
        align-items: center;
        justify-content: center;

        image {
          width: 100%;
          height: 100%;
          border-radius: 8rpx
        }

        .iconfont {
          font-size: 140rpx;
        }
      }
    }
  }
}

.ad-right {
  position: relative;
  .vip-point {
    position: absolute;
    top: 0;
    right: 0;
  }
}
.popup-close {
  display: flex;
  height: 40px;
  align-items: center;
  justify-content: flex-end;
  padding: 0 1rem;
}
.popup-grid {
  padding: 0 1rem;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 40rpx;
  margin-top: 1rem;
}
.popup-grid_li {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.d-active {
  // background: rgba(247, 248, 252, 1);
  // border-radius: 8px;
  // overflow: hidden;
  // padding-bottom: 0.5rem;
}
.d-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 10px;
  display: none;
}
.d-grid_li {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  font-size: 0.8rem;
  color: rgba(51, 51, 51, 1);
}

.d-grid_li1 {
  height: 44rpx;
  border: 2rpx solid #cccccc;
  border-radius: 22rpx;
}

.d-active .d-grid {
  display: grid;
}
.doc-more{
  padding: 0.6rem;
}

.select-options {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #ffffff;
  box-shadow: 0rpx -6rpx 27rpx 0rpx rgba(192,212,220,0.04);
  border: 1px solid #DEDEDE;
  z-index: 99999;

  .select-options-container {
    padding: 32rpx 63rpx 50rpx;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .select-option {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 10rpx;

      image {
        width: 38rpx;
        height: auto;
      }

      view {
        font-size: 22rpx;
        color: #000000;
      }
    }

    .select-option1 {
      image {
        width: 182rpx;
        height: auto;
      }
    }
  }
}

.edit-file {
  width: 524rpx;
  padding: 32rpx 0;
  background: #FFFFFF;
  border-radius: 30rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;

  .close {
    position: absolute;
    top: 30rpx;
    right: 30rpx;
    width: 20rpx;
  }

  .title {
    font-weight: 500;
    font-size: 28rpx;
    color: #000000;
    margin-bottom: 39rpx;
  }

  .input-box {
    width: 420rpx;
    height: 85rpx;
    background: #F7F7F7;
    border-radius: 10rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0 20rpx;
    margin-bottom: 60rpx;

    input {
      flex-grow: 1;
      font-size: 36rpx;
      color: #464646;
    }

    image {
      flex-shrink: 0;
      padding-left: 20rpx;
      width: 32rpx;
      height: auto;
    }
  }

  .options {
    display: flex;
    align-items: center;
    justify-content: space-between;

    .confirm {
      background: #856BFF;
      width: 284rpx;
      height: 73rpx;
      color: #ffffff;
      font-weight: 600;
      font-size: 28rpx;
      border-radius: 36rpx;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
}

.delete-dialog {
  width: 524rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: url("https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/bg03.png") left top/100% 100% no-repeat;
  padding: 164rpx 32rpx 32rpx;
  box-sizing: border-box;
  position: relative;

  .close {
    position: absolute;
    top: 60rpx;
    right: 30rpx;
    width: 20rpx;
  }

  .title {
    font-size: 34rpx;
    color: #464646;
    line-height: 49rpx;
    margin-bottom: 46rpx;
  }

  .options {
    align-self: stretch;
    display: flex;
    align-items: center;
    justify-content: space-between;

    view {
      width: 223rpx;
      height: 73rpx;
      font-weight: 600;
      font-size: 28rpx;
      border-radius: 36rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #ffffff;
    }

    .cancel {
      background: #F1C587;
    }

    .confirm {
      background: #856BFF;
    }
  }
}
</style>
