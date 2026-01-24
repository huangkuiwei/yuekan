<template>
  <view class="index-top">
  </view>

  <view class="global-m" style="position: relative;">
    <view class="empty" v-if="!user.uid">
      <view class="empty-box">
        <image
            class="empty-icon"
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/empty.png"
        />

        <view class="login-box" v-if="!user.uid">
          <view class="tip">登录扫描账号，查看账号内同步文档</view>
          <view class="login" @click="toRouter('/pages/login/index')">登录</view>
        </view>
      </view>
    </view>

    <template v-else>
      <view class="options">
        <view class="option-item" @click="chooseLocalPicture(['camera'])">
          <image class="icon" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/icon03.png"/>
          <text>拍照导入</text>
        </view>

        <view class="option-item" @click="chooseLocalPicture(['album'])">
          <image class="icon" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/icon04.png"/>
          <text>相册导入</text>
        </view>

        <view class="option-item" @click="choosePDFFile">
          <image class="icon" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/icon05.png"/>
          <text>文档导入</text>
        </view>
      </view>

      <!--<view style="margin-top: 1rem; text-align: center">最近文件</view>-->
      <view class="isSelectFile" v-if="isSelectFile">
        <text @click="cancel">取消</text>
        <text>已选择{{ files.filter(item => item.select).length }}项</text>
        <text @click="selectAll">全选</text>
      </view>

      <view class="folder-list">
        <view class="header">
          <text class="title">所有文档</text>
          <text style="flex-grow: 1"></text>

          <view class="options1">
            <image @click="showAddDicDialog = true" mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/icon01.png"/>
            <image @click="selectFile1" mode="heightFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/icon02.png"/>
          </view>
        </view>

        <image
            @click="tobackDict"
            v-show="fileUrl.length > 0"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/my/back.png"
            style="width: 30px; height: 30px"
            mode=""
        />
        <view
            class="file-item"
            :class="{ 'd-active': item.id == tindex, lastFile: item.lastFile, file: item.file_formmat }"
            v-for="(item, index) in files"
            :key="index"
            @click="getNewFile(item)"
        >
          <view
              class="left"
              :style="{
          width: item.file_formmat ? '102rpx' : '90rpx',
          height: item.file_formmat ? '102rpx' : '90rpx',
        }"
          >
            <image
                v-if="
                            item.folder_name.includes('.') &&
                            /\.(jpg|jpeg|png|gif|bmp|webp|svg)$/i.test(item.folder_name)
                          "
                mode="aspectFit"
                :src="item.file_url"
            />
            <wd-icon v-if="/\.(doc|docx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="word1" size="60" color="#0072FF"></wd-icon>
            <wd-icon v-else-if="/\.(xls|xlsx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="excel" size="60" color="#00C650"></wd-icon>
            <wd-icon v-else-if="/\.(ppt|pptx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="ppt" size="60" color="#FF3E4C"></wd-icon>
            <wd-icon v-else-if="/\.(pdf)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="pdf" size="60" color="#FF3E4C"></wd-icon>

            <image
                v-if="!item.file_formmat"
                style="width: 93rpx; margin-left: 10rpx"
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/document/folder.png"
            ></image>
          </view>

          <view class="right" :style="{ justifyContent: 'center', gap: '10rpx' }">
            <view class="filename" :class="{ filename1: !item.file_formmat }">
              {{ item.folder_name }}
            </view>

            <view
                class="time"
                v-if="item.create_at"
            >
              <text>更新时间：{{ item.create_at }}</text>
            </view>
          </view>

          <view
              @click.stop="(tindex = (tindex === item.id ? -1 : item.id)), (currentItem = (currentItem === item ? null : item))"
              class="doc-more"
          >
            <template v-if="!isSelectFile">
              <template v-if="item.file_formmat || isSelectFile">
                <view class="more-dot" v-if="tindex === item.id"></view>
                <view class="more-dot1" v-else></view>
              </template>
            </template>

            <view class="detect-count" @click.stop="item.select = !item.select" v-else>
              <template v-if="item.file_formmat || isSelectFile">
                <view class="more-dot" v-if="item.select"></view>
                <view class="more-dot1" v-else></view>
              </template>
            </view>
          </view>
        </view>

        <view class="empty" v-if="files.length === 0">
          <view class="empty-box">
            <view class="add-box">
              <view class="tip">暂无文档数据</view>
            </view>
          </view>
        </view>
      </view>
    </template>
  </view>

  <!--<view class="select-options">-->
  <!--  <image-->
  <!--      mode="widthFix"-->
  <!--      src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon14/01.png"-->
  <!--      @click="moveAll"-->
  <!--  />-->

  <!--  <image-->
  <!--      mode="widthFix"-->
  <!--      src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon14/02.png"-->
  <!--      @click="editAll"-->
  <!--  />-->

  <!--  <image-->
  <!--      mode="widthFix"-->
  <!--      src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon14/03.png"-->
  <!--      @click="shareAll"-->
  <!--  />-->

  <!--  <image-->
  <!--      mode="widthFix"-->
  <!--      src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/icon14/04.png"-->
  <!--      @click="deleteAll"-->
  <!--  />-->
  <!--</view>-->

  <view class="select-options" v-if="selectFile || isSelectFile">
    <view class="select-options-container">
      <view @click.stop="isSelectFile ? moveAll() : toMove(selectFile)" v-if="(selectFile && selectFile.file_formmat || isSelectFile)" class="select-option">
        <image
            mode="heightFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/option01.png"
        />

        <view>移动</view>
      </view>

      <view @click.stop="isSelectFile ? editAll() : (showEditFileDialog = true)" class="select-option">
        <image
            mode="heightFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/option02.png"
        />

        <view>编辑</view>
      </view>

      <view @click.stop="isSelectFile ? deleteAll() : (showDeleteDialog = true)" class="select-option">
        <image
            mode="heightFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/option03.png"
        />

        <view>删除</view>
      </view>

      <view @click.stop="isSelectFile ? shareAll() : toShareFile(selectFile)" v-if="(selectFile && selectFile.file_formmat || isSelectFile)" class="select-option1">
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
            @click="(selectFile.file_name = selectFile.file_formmat || '')"
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

  <wd-popup v-model="showAddDicDialog" @close="showAddDicDialog = false" custom-style="background: transparent">
    <view class="edit-file">
      <image
          class="close"
          mode="widthFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/close2.png"
          @click="showAddDicDialog = false"
      />

      <view class="title">新建文件夹</view>
      <view class="input-box">
        <input type="text" v-model="fileName" placeholder="请输入文件夹名称" />
        <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/index/close.png"
            @click="fileName = ''"
        />
      </view>
      <view class="options">
        <view class="confirm" @click="addDic()">确定</view>
      </view>
    </view>
  </wd-popup>

  <wd-message-box selector="wd-delete-box-slot"></wd-message-box>
  <wd-message-box selector="wd-add-box-slot">
    <view>
      <wd-input customClass="create-folder-input" v-model="fileName" placeholder="请输入文件夹名称"></wd-input>
    </view>
  </wd-message-box>
  <wd-gap height="3rem"></wd-gap>
  <NavBar :index="2" v-if="!isSelectFile"></NavBar>
  <wd-popup custom-class="move-class" v-model="moveShow">
    <Move
        v-if="moveShow"
        ref="moveRef"
        @update="onUpdate"
        :fileId="fileId"
        v-model:moveShow="moveShow"
    ></Move>
  </wd-popup>
  <wd-message-box selector="wd-edit-box-slot"></wd-message-box>
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
import { ref, computed, watchEffect } from 'vue'
import NavBar from '@/section/a-navbar.vue'
import { shareShow, shareUrl, state } from '@/section/share'
import Share from '@/section/share.vue'
import { toRouter } from '@/hooks/utils'
import {
  onLoad,
  onPullDownRefresh,
  onShareAppMessage,
  onShow
} from '@dcloudio/uni-app'
import {
  initDictionary,
  deleteFile,
  toAddDict, moveShow
} from './document'
import { useMessage } from '/node_modules/wot-design-uni'
import $http from '@/hooks/http'
import Move from './move.vue'
import { ustate } from '@/pages/camera/camera'
import Util from '@/pages/camera/util2.vue'

const addMessage = useMessage('wd-add-box-slot')
const deleteMessage = useMessage('wd-delete-box-slot')
const editMessage = useMessage('wd-edit-box-slot')

const value = ref(0),
    tindex = ref(-1),
    files = ref([]),
    fileUrl = ref([]),
    currentItem = ref(null)
const fileName = ref('')
const options = ref([
  { label: '全部', value: 0 }
])
const fileId = ref('')
const key = ref(null)
let user = ref({})
const isSelectFile = ref(false)
let MenuButtonInfo = uni.getMenuButtonBoundingClientRect()
const headerTop = ref(MenuButtonInfo.top + MenuButtonInfo.height + 10 + 'px')

const lastFolderInfo = computed(() => {
  return fileUrl.value[fileUrl.value.length - 1] || {}
})

const selectFile = ref()
const showEditFileDialog = ref(false)
const showDeleteDialog = ref(false)
const showAddDicDialog = ref(false)

const toolType = ref(1)
const picList = ref([])

watchEffect(() => {
  files.value.forEach(item => {
    if (!item.file_name) {
      item.file_name = item.folder_name
    }
  })

  let lastFileIndex = files.value.findLastIndex(item => !item.file_formmat)

  if (lastFileIndex !== -1) {
    files.value[lastFileIndex].lastFile = true
  }

  let file = files.value.find(item => item.id === tindex.value)

  if (file) {
    selectFile.value = JSON.parse(JSON.stringify(file))
  } else {
    selectFile.value = undefined
  }
})

onLoad(() => {
  uni.startPullDownRefresh()

  initDictionary().then((res) => {
    res.forEach(item => {
      options.value.push({ label: item.folder_name, value: item.folder_path_id, ...item })
    })
  })
})

onShow(() => {
  tindex.value = -1
  fileUrl.value = []

  uni.showLoading({
    title: '正在加载'
  })

  initDictionary().then((data) => {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    }).finally(() => {
      uni.hideLoading()
    })
  }).catch(() => {
    uni.hideLoading()
  })

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

// onShareAppMessage((res) => {
//   return {
//     title: "分享",
//     path: "/pages/preview/index?shareUrl=" + encodeURIComponent(shareUrl.value) + "&shareType=file",
//     imageUrl: shareUrl.value,
//   };
// });

onPullDownRefresh(() => {
  initDictionary(lastFolderInfo.value.id).then((data) => {
    tindex.value = -1

    // if (!fileUrl.value.length) {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    })
    // }
  }).finally(() => {
    uni.stopPullDownRefresh()
  })
})

const onInput = () => {
  tindex.value = -1
  fileUrl.value = []

  uni.showLoading({
    title: '正在加载'
  })

  initDictionary().then((data) => {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    }).finally(() => {
      uni.hideLoading()
    })
  }).catch(() => {
    uni.hideLoading()
  })
}

const onChange = () => {
  tindex.value = -1
  fileUrl.value = []

  uni.showLoading({
    title: '正在加载'
  })

  initDictionary().then((data) => {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    }).finally(() => {
      uni.hideLoading()
    })
  }).catch(() => {
    uni.hideLoading()
  })
}

const onSelectChange = ($event) => {
  if ($event.value === 0) {
    tindex.value = -1
    fileUrl.value = []

    uni.showLoading({
      title: '正在加载'
    })

    initDictionary().then((data) => {
      $http.post('api/user/hdfs/file/file/list', {
        folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
        key: key.value || null
      }).then((res) => {
        res.data = res.data.slice(0, 20)

        if (!key.value) {
          files.value = data.map(item => ({
            ...item,
            select: false,
          }))
        } else {
          files.value = []
        }

        res.data.forEach(item => {
          files.value.push({
            ...item,
            folder_name: item.file_name,
            select: false,
          })
        })
      }).finally(() => {
        uni.hideLoading()
      })
    }).catch(() => {
      uni.hideLoading()
    })
  } else {
    getNewFile($event.selectedItem)
  }
}

const toDelete = (item, confirm = true) => {
  deleteHandler(item)
}

const deleteHandler = (item) => {
  deleteFile(item.file_formmat ? 'api/user/hdfs/file/file/delete' : 'api/user/hdfs/file/folder/delete',
      item.file_formmat ? {
        folder_id: lastFolderInfo.value.id || 0,
        id: item.id
      } : {
        id: item.id
      }).then(() => {
    showDeleteDialog.value = false
    tindex.value = -1
    isSelectFile.value = false

    uni.showLoading({
      title: '正在加载'
    })

    initDictionary(lastFolderInfo.value.id).then((data) => {
      options.value = [{ label: '全部', value: 0 }]

      data.forEach(item => {
        options.value.push({ label: item.folder_name, value: item.folder_path_id, ...item })
      })

      // if (!fileUrl.value.length) {
      $http.post('api/user/hdfs/file/file/list', {
        folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
        key: key.value || null
      }).then((res) => {
        res.data = res.data.slice(0, 20)

        if (!key.value) {
          files.value = data.map(item => ({
            ...item,
            select: false,
          }))
        } else {
          files.value = []
        }

        res.data.forEach(item => {
          files.value.push({
            ...item,
            folder_name: item.file_name,
            select: false,
          })
        })
      }).finally(() => {
        uni.hideLoading()
      })
      // }
    }).catch(() => {
      uni.hideLoading()
    })
  })
}

const getNewFile = (item) => {
  if (isSelectFile.value) {
    return
  }

  if (!item.file_formmat) {
    fileUrl.value.push(item)
    uni.showLoading({
      title: '正在加载'
    })

    initDictionary(lastFolderInfo.value.id).then((data) => {
      $http.post('api/user/hdfs/file/file/list', {
        folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
        key: key.value || null
      }).then((res) => {
        res.data = res.data.slice(0, 20)

        if (!key.value) {
          files.value = data.map(item => ({
            ...item,
            select: false,
          }))
        } else {
          files.value = []
        }

        res.data.forEach(item => {
          files.value.push({
            ...item,
            folder_name: item.file_name,
            select: false,
          })
        })
      }).finally(() => {
        uni.hideLoading()
      })

      uni.hideToast()
      tindex.value = -1
    }).catch(() => {
      uni.hideLoading()
    })
  } else {
    if (/\.(jpg|jpeg|png|gif|bmp|webp|svg)$/i.test(item.file_name)) {
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
        toRouter('/pages/ai-view/index', 'url=' + encodeURIComponent(item.file_url))
      }
    }
  }
}

const selectFile1 = () => {
  isSelectFile.value = !isSelectFile.value
  tindex.value = -1
}

const addDic = () => {
  if (!fileName.value) {
    uni.showToast({ title: '文件夹名称不能为空', icon: 'none' })
    return
  }
  toAddDict(lastFolderInfo.value.id, fileName.value).then(() => {
    showAddDicDialog.value = false
    fileName.value = ''
    tindex.value = -1
    isSelectFile.value = false
    uni.showLoading({
      title: '正在加载'
    })

    initDictionary(lastFolderInfo.value.id).then((data) => {
      // if (!fileUrl.value.length) {
      options.value = [{ label: '全部', value: 0 }]

      data.forEach(item => {
        options.value.push({ label: item.folder_name, value: item.folder_path_id, ...item })
      })

      $http.post('api/user/hdfs/file/file/list', {
        folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
        key: key.value || null
      }).then((res) => {
        res.data = res.data.slice(0, 20)

        if (!key.value) {
          files.value = data.map(item => ({
            ...item,
            select: false,
          }))
        } else {
          files.value = []
        }

        res.data.forEach(item => {
          files.value.push({
            ...item,
            folder_name: item.file_name,
            select: false,
          })
        })
      }).finally(() => {
        uni.hideLoading()
      })
      // }
    }).catch(() => {
      uni.hideLoading()
    })
  })
}

const chooseLocalPicture = (items) => {
  uni.chooseImage({
    count: 9,
    sizeType: ['original', 'compressed'],
    sourceType: items,
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

const cancel = () => {
  files.value.forEach(item => {
    item.select = false
  })

  isSelectFile.value = false
  tindex.value = -1
}

const selectAll = () => {
  files.value.forEach(item => {
    item.select = true
  })
}

const tobackDict = () => {
  fileUrl.value.pop()

  uni.showLoading({
    title: '正在加载'
  })

  initDictionary(lastFolderInfo.value.id).then((data) => {
    // if (!fileUrl.value.length) {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    }).finally(() => {
      uni.hideLoading()
    })
    // }
  }).catch(() => {
    uni.hideLoading()
  })
}

const toMove = (item) => {
  moveShow.value = true

  if (Array.isArray(item)) {
    fileId.value = item.map(item => item.id)
  } else {
    fileId.value = item.id
  }
}

const onUpdate = () => {
  tindex.value = -1
  initDictionary(lastFolderInfo.value.id).then((data) => {
    // if (!fileUrl.value.length) {
    $http.post('api/user/hdfs/file/file/list', {
      folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
      key: key.value || null
    }).then((res) => {
      res.data = res.data.slice(0, 20)

      if (!key.value) {
        files.value = data.map(item => ({
          ...item,
          select: false,
        }))
      } else {
        files.value = []
      }

      res.data.forEach(item => {
        files.value.push({
          ...item,
          folder_name: item.file_name,
          select: false,
        })
      })
    }).finally(() => {
      uni.hideLoading()
    })
    // }
  }).catch(() => {
    uni.hideLoading()
  })
}

const toEdit = (item) => {
  uni.showLoading({
    title: '加载中',
    mask: true
  })

  if (!item.file_formmat) {
    $http.post('api/user/hdfs/file/folder/rename', {
      id: item.id,
      folder_name: selectFile.value.file_name
    }).then(() => {
      tindex.value = -1
      showEditFileDialog.value = false
      isSelectFile.value = false

      uni.showLoading({
        title: '正在加载'
      })

      initDictionary(lastFolderInfo.value.id).then((data) => {
        // if (!fileUrl.value.length) {
        $http.post('api/user/hdfs/file/file/list', {
          folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
          key: key.value || null
        }).then((res) => {
          res.data = res.data.slice(0, 20)

          if (!key.value) {
            files.value = data.map(item => ({
              ...item,
              select: false,
            }))
          } else {
            files.value = []
          }

          res.data.forEach(item => {
            files.value.push({
              ...item,
              folder_name: item.file_name,
              select: false,
            })
          })
        }).finally(() => {
          uni.hideLoading()
        })
        // }
      }).catch(() => {
        uni.hideLoading()
      })
    }).finally(() => {
      uni.hideLoading();
    })
  } else {
    $http.post('api/user/hdfs/file/file/rename', {
      id: item.id,
      file_name: selectFile.value.file_name
    }).then(() => {
      tindex.value = -1
      showEditFileDialog.value = false
      isSelectFile.value = false

      uni.showLoading({
        title: '正在加载'
      })

      initDictionary(lastFolderInfo.value.id).then((data) => {
        // if (!fileUrl.value.length) {
        $http.post('api/user/hdfs/file/file/list', {
          folder_id: key.value ? null : (lastFolderInfo.value.id || 0),
          key: key.value || null
        }).then((res) => {
          res.data = res.data.slice(0, 20)

          if (!key.value) {
            files.value = data.map(item => ({
              ...item,
              select: false,
            }))
          } else {
            files.value = []
          }

          res.data.forEach(item => {
            files.value.push({
              ...item,
              folder_name: item.file_name,
              select: false,
            })
          })
        }).finally(() => {
          uni.hideLoading()
        })
        // }
      }).catch(() => {
        uni.hideLoading()
      })
    }).finally(() => {
      uni.hideLoading();
    })
  }
}

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/new_scantools/share.png',
    path: '/pages/index/index',
  }
})


const toShareFile = (item) => {
  uni.showLoading({
    title: '正在分享...'
  })

  let newFilePath = `${uni.env.USER_DATA_PATH}/${item.folder_name}`

  uni.downloadFile({
    url: item.file_url,
    filePath: newFilePath,
    success: () => {
      uni.shareFileMessage({
        filePath: newFilePath,
        success() {
          tindex.value = -1
          showEditFileDialog.value = false
          isSelectFile.value = false
          console.log('文件转发成功')
        },
        fail(res) {
          console.log('文件转发失败', res)
        },
        complete() {
          uni.hideLoading()
        }
      })
    },
    fail(res) {
      console.log('文件下载失败', res)
      uni.hideLoading()
    }
  })
}

const moveAll = () => {
  let select = files.value.filter(item => item.select)

  if (!select.length) {
    uni.showToast({
      title: '请选择文件',
      icon: 'none',
    })

    return
  }

  if (select.find(item => !item.file_formmat)) {
    uni.showToast({
      title: '文件夹不支持移动',
      icon: 'none',
    })

    return
  }

  toMove(select)
}

const editAll = () => {
  let select = files.value.filter(item => item.select)

  if (!select.length) {
    uni.showToast({
      title: '请选择文件',
      icon: 'none',
    })

    return
  }

  if (select.length !== 1) {
    uni.showToast({
      title: '只能选择一个文件',
      icon: 'none',
    })

    return
  }

  // toEdit(select[0])
  tindex.value = select[0].id
  showEditFileDialog.value = true
}

const shareAll = () => {
  let select = files.value.filter(item => item.select)

  if (!select.length) {
    uni.showToast({
      title: '请选择文件',
      icon: 'none',
    })

    return
  }

  if (select.length !== 1) {
    uni.showToast({
      title: '只能选择一个文件',
      icon: 'none',
    })

    return
  }

  if (select.find(item => !item.file_formmat)) {
    uni.showToast({
      title: '文件夹不支持分享',
      icon: 'none',
    })

    return
  }

  toShareFile(select[0])
}

const deleteAll = () => {
  let select = files.value.filter(item => item.select)

  if (!select.length) {
    uni.showToast({
      title: '请选择文件',
      icon: 'none',
    })

    return
  }

  select.forEach(item => {
    toDelete(item, false)
  })
}
</script>
<style>
page {
  padding-bottom: 200rpx;
  --wot-drop-menu-fs: 1rem;
}

.wd-input__suffix {
  padding-right: 20rpx;
}

.document-drop-menu .wd-drop-menu__list {
  background: none !important;
  color: #ffffff;
  font-size: 40rpx;
}

.search-document .wd-input {
  border-radius: 8rpx;
  width: 350rpx;
  height: 66rpx;
}
</style>
<style scoped lang="scss">
.index-top {
  background: linear-gradient(-1deg, #FAFAFA, #F7F6FF);
}

.index-header {
  height: 180rpx;
}

.folder-list {
  // .lastFile {
  //   border-bottom: 2rpx solid #E7E7E7;
  // }
}

.file-item {
  display: flex;
  align-items: center;
  margin-bottom: 20rpx;

  &.file {
  }

  .left {
    flex-shrink: 0;
    margin-right: 26rpx;
    display: flex;
    align-items: center;
    // justify-content: center;

    image {
      width: 100%;
      height: 100%;
      border-radius: 8rpx
    }

    .iconfont {
      font-size: 140rpx;
    }
  }

  .right {
    position: relative;
    align-self: stretch;
    flex-grow: 1;
    display: flex;
    flex-direction: column;

    .filename {
      font-size: 24rpx;
      color: #1F1F1F;

      &.filename1 {
        font-size: 28rpx;
        color: #000000;
        font-weight: 500;
      }
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

  .doc-more {
    flex-shrink: 0;

    .more-dot {
      width: 24rpx;
      height: 24rpx;
      background: #856BFF;
      border-radius: 50%;
      border: 2rpx solid #856BFF;
    }

    .more-dot1 {
      width: 24rpx;
      height: 24rpx;
      background: #FFFFFF;
      border-radius: 50%;
      border: 2rpx solid #856BFF;
    }
  }
}

.navbar {
  display: flex;
  align-items: center;
}

.icons {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding-right: 1rem;
  margin-left: 50px;
}

.doc-cell {
  --wot-cell-padding: 0;
  --wot-cell-title-fs: 1rem;
  --wot-cell-label-fs: 0.8rem;
}

.doc-more {
  display: flex;
  align-items: center;
}

.d-active {
}

.folder {
  position: absolute;
  bottom: -46rpx;
  right: 0;
  width: 50%;
  grid-template-columns: repeat(2, 1fr) !important;
}

.d-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 10px;
  display: none;
}

.d-grid-3 {
  grid-template-columns: repeat(3, 1fr);
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

.global-m {
  padding: 28rpx 28rpx 45rpx;
  border-radius: 40rpx;

  .options {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 34rpx;

    .option-item {
      width: 211rpx;
      height: 112rpx;
      background: #FFFFFF;
      border-radius: 20rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 20rpx;

      .icon {
        width: 34rpx;
      }

      text {
        font-size: 31rpx;
        color: #000000;
      }
    }
  }

  .isSelectFile {
    border-radius: 40rpx 40rpx 0 0;
    background: #856BFF;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 34rpx;
    color: #ffffff;

    text {
      font-weight: 500;
      font-size: 26rpx;

      &:nth-child(2) {
        font-size: 30rpx;
      }
    }
  }

  .folder-list {
    background: #ffffff;
    border-radius: 10rpx;
    padding: 30rpx 20rpx;

    .header {
      display: flex;
      align-items: center;
      padding-bottom: 25rpx;
      margin-bottom: 20rpx;
      border-bottom: 2rpx solid #00000020;

      .title {
        font-weight: 500;
        font-size: 35rpx;
        color: #1F1F1F;
      }

      .options1 {
        display: flex;
        align-items: center;
        gap: 44rpx;

        image {
          height: 34rpx;
          width: auto;
        }
      }
    }
  }
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
        height: 38rpx;
        width: auto;
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

.empty {
  display: flex;
  align-items: center;
  justify-content: center;

  .empty-box {
    width: 100%;
    padding: 50rpx 0;
    box-sizing: border-box;
    background: #ffffff;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .empty-icon {
      width: 245rpx;
    }
  }

  .login-box, .add-box {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .tip {
      font-size: 22rpx;
      color: #656565;
      margin-bottom: 25rpx;
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
</style>