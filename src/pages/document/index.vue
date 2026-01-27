<template>
  <view class="page-title">
    <text>我的文件</text>
  </view>

  <view class="banner"></view>

  <view class="index-top">
    <view class="index-header">
      <view class="index-title">
        <view class="search-document">
          <wd-input
              v-model="key"
              placeholder="搜索你想要的文件"
              :no-border="true"
              :use-prefix-slot="true"
              :clearable="true"
              @input="onInput"
              @change="onChange"
          >
            <template #prefix>
              <image style="width: 30rpx; padding: 10rpx 0 0 10rpx" mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/documnet/seach.png" />
            </template>
          </wd-input>
        </view>
      </view>
    </view>
  </view>

  <view class="global-m" style="background: #ffffff; position: relative; top: -8rpx; border-top-left-radius: 10rpx; border-top-right-radius: 10rpx;">
    <!--<view class="header">-->
    <!--  <text>所有文档</text>-->
    <!--  <image @click="addDic" mode="widthFix" style="width: 70rpx" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/folder.png"/>-->
    <!--  <image @click="selectFile" mode="widthFix" style="width: 70rpx" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/select-icon.png"/>-->
    <!--</view>-->

    <image
        @click="tobackDict"
        v-show="fileUrl.length > 0"
        src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/my/back.png"
        style="width: 30px; height: 30px; margin-bottom: 20rpx"
        mode=""
    />

    <view class="folder-box" v-if="!key">
      <view class="folder-box-title">我的文件夹</view>

      <view class="folder-list">
        <view class="folder-item" @click="getNewFile(item)" v-for="(item, index) in files.filter(x => !x.folder_name.includes('.'))" :key="index">
          <image
              mode="heightFix"
              src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/folder_icon.png"
          />

          <text class="name">{{ item.folder_name }}</text>
        </view>

        <view class="folder-item add-folder" @click="addDic">
          <text class="add-icon">+</text>
          <text class="name">新建</text>
        </view>
      </view>
    </view>

    <!--<view style="margin-top: 1rem; text-align: center">最近文件</view>-->
    <view style="display: flex; flex-direction: column; gap: 20rpx;">
      <view class="all-file">
        <view class="left">
          <text>所有文件</text>
          <text class="number">（{{ files.filter(x => x.folder_name.includes('.')).length }}）</text>
        </view>

        <view class="right" v-if="user.uid">
          <text @click="selectFile">{{ isSelectFile ? '取消' : '管理' }}</text>
          <text v-if="isSelectFile" class="all" @click="selectAll">全选</text>
        </view>
      </view>

      <view
          class="file-item-wrap"
          :class="{ 'd-active': item.id == tindex }"
          :style="{ background: !item.file_formmat ? '#F3F7F8' : '', paddingBottom: (!item.file_formmat && item.id == tindex) ? '70rpx' : null }"
          v-for="(item, index) in files.filter(x => x.folder_name.includes('.'))"
          :key="index"
          @click="getNewFile(item)"
      >
        <view class="file-item">
          <view
              class="left"
              :style="{
          width: item.file_formmat ? '110rpx' : '90rpx',
          height: item.file_formmat ? '110rpx' : '90rpx',
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
            <wd-icon v-if="/\.(doc|docx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="word1" size="55" color="#0072FF"></wd-icon>
            <wd-icon v-else-if="/\.(xls|xlsx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="excel" size="55" color="#00C650"></wd-icon>
            <wd-icon v-else-if="/\.(ppt|pptx)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="ppt" size="55" color="#FF3E4C"></wd-icon>
            <wd-icon v-else-if="/\.(pdf)$/i.test(item.folder_name)" class-prefix="icon" custom-class="iconfont" name="pdf" size="55" color="#FF3E4C"></wd-icon>

            <image
                v-if="!item.file_formmat"
                style="width: 30px; margin-left: 30rpx"
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/folder_icon.png"
            ></image>
          </view>

          <view class="right" :style="{ justifyContent: (item.id == tindex && item.file_formmat) ? 'center' : 'center', gap: item.id == tindex ? '10rpx' : '10rpx' }">
            <view class="filename">
              <text>{{ item.folder_name.split('.')[0] }}</text>
              <text v-if="item.file_formmat">{{ item.file_formmat }}</text>
            </view>

            <view
                class="time"
                v-if="item.create_at"
            >
              <!--<image-->
              <!--    mode="widthFix"-->
              <!--    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/time-icon.png"-->
              <!--/>-->
              <text>{{ item.create_at }}</text>
            </view>
          </view>

          <view
              @click.stop="(tindex = (tindex === item.id ? -1 : item.id)), (currentItem = (currentItem === item ? null : item))"
              class="doc-more"
          >
            <!--<image-->
            <!--    v-if="!isSelectFile"-->
            <!--    style="width: 6rpx"-->
            <!--    mode="widthFix"-->
            <!--    src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/index/more.png"-->
            <!--/>-->

            <view class="detect-count" @click.stop="isSelectFile = true; item.select = !item.select">
              <view v-if="item.select">
                <wd-icon color="#448BFF" name="check-circle-filled" size="22px"></wd-icon>
              </view>

              <view v-else>
                <wd-icon color="#E6E6E6" name="circle1" size="22px"></wd-icon>
              </view>
            </view>
          </view>
        </view>

        <view class="d-grid" :class="{ folder: !item.file_formmat }" style="grid-gap: 12rpx">
          <view @click.stop="toEdit(item)" class="d-grid_li d-grid_li1">
            <image
                mode="heightFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/index/option1.png"
            />
            <view>重命名</view>
          </view>
          <view
              @click.stop="toShareFile(item)"
              v-if="item.file_formmat"
              class="d-grid_li d-grid_li1"
          >
            <image
                mode="heightFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/index/option2.png"
            />
            <view>分享</view>
          </view>
          <view @click.stop="toMove(item)" class="d-grid_li d-grid_li1" v-if="item.file_formmat">
            <image
                mode="heightFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/index/option3.png"
            />
            <view>移动</view>
          </view>
          <view @click.stop="toDelete(item)" class="d-grid_li d-grid_li1">
            <image
                mode="heightFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/index/option4.png"
            />
            <view>删除</view>
          </view>
        </view>
      </view>
      <!--<view class="empty" v-if="files.length === 0">-->
      <!--  <view class="empty-box">-->
      <!--    <image-->
      <!--        style="width: 100%"-->
      <!--        mode="widthFix"-->
      <!--        src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/scantool/static/assets/home/new/empty-icon.png"-->
      <!--    ></image>-->
      <!--    <view style="margin-top: 0.112rpx; text-align: center"-->
      <!--    >暂无文档</view-->
      <!--    >-->
      <!--  </view>-->
      <!--</view>-->
    </view>
  </view>

  <view class="empty1">没有更多内容了~</view>

  <view class="select-options" v-if="isSelectFile">
    <view @click.stop="editAll" class="d-grid_li d-grid_li1">
      <image
          mode="heightFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/document/icon01.png"
      />
      <view>重命名</view>
    </view>
    <view
        @click.stop="shareAll"
        class="d-grid_li d-grid_li1"
    >
      <image
          mode="heightFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/document/icon02.png"
      />
      <view>分享</view>
    </view>
    <view @click.stop="moveAll" class="d-grid_li d-grid_li1">
      <image
          mode="heightFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/document/icon03.png"
      />
      <view>移动</view>
    </view>
    <view @click.stop="deleteAll" class="d-grid_li d-grid_li1">
      <image
          mode="heightFix"
          src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/yuekan/document/icon04.png"
      />
      <view>删除</view>
    </view>
  </view>

  <wd-message-box selector="wd-delete-box-slot"></wd-message-box>
  <wd-message-box selector="wd-add-box-slot">
    <view>
      <wd-input customClass="create-folder-input" v-model="fileName" placeholder="输入您的文件夹名"></wd-input>
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
</template>

<script setup>
import { ref, computed } from 'vue'
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

const addMessage = useMessage('wd-add-box-slot')
const deleteMessage = useMessage('wd-delete-box-slot')
const editMessage = useMessage('wd-edit-box-slot')

const value = ref(0),
    tindex = ref(-1),
    files = ref([]),
    fileUrl = ref([]),
    currentItem = ref(null)
const fileName = ref(null)
const options = ref([
  { label: '筛选', value: 0 }
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

const selectAll = () => {
  files.value.forEach((item) => {
    item.select = true
  })
}

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
  if (!confirm) {
    deleteHandler(item)
    return
  }

  deleteMessage
      .confirm({
        msg: item.file_formmat ? '删除后不可恢复，确认删除选中文件吗？' : '删除后不可恢复，确认删除选中文件夹吗？'
      })
      .then((type) => {
        if (type.action == 'confirm') {
          deleteHandler(item)
        }
      })
      .catch(() => {
        console.log('点击了取消按钮')
      })
}

const deleteHandler = (item) => {
  deleteFile(item.file_formmat ? 'api/user/hdfs/file/file/delete' : 'api/user/hdfs/file/folder/delete',
      item.file_formmat ? {
        folder_id: lastFolderInfo.value.id || 0,
        id: item.id
      } : {
        id: item.id
      }).then(() => {
    tindex.value = -1

    uni.showLoading({
      title: '正在加载'
    })

    tindex.value = -1
    initDictionary(lastFolderInfo.value.id).then((data) => {
      options.value = [{ label: '筛选', value: 0 }]

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

const selectFile = () => {
  isSelectFile.value = !isSelectFile.value
  tindex.value = -1

  files.value.forEach((item) => {
    item.select = false
  })
}

const addDic = () => {
  if (!user.value.uid) {
    uni.showModal({
      title: '提示',
      content: '您当前未登录或登录已失效，为了您有更好的体验，爱悦看需要您进行登录',
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

  fileName.value = null
  addMessage
      .confirm({
        title: '新建文件夹'
      })
      .then(() => {
        if (!fileName.value) {
          uni.showToast({ title: '文件夹名称不能为空', icon: 'none' })
          return
        }
        toAddDict(lastFolderInfo.value.id, fileName.value).then(() => {
          uni.showLoading({
            title: '正在加载'
          })

          initDictionary(lastFolderInfo.value.id).then((data) => {
            // if (!fileUrl.value.length) {
            options.value = [{ label: '筛选', value: 0 }]

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
      })
      .catch(() => {
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
  editMessage
      .prompt({
        title: '重命名',
        inputValue: item.folder_name,
        inputPattern: !item.file_formmat
            ? /^(?!\s*$).+/
            : /^[^\\\/:*?"<>|\r\n]+$$/,
        inputError: '文件名格式不正确(文件需要后缀名)',
        inputPlaceholder: '请输入名称'
      })
      .then((resp) => {
        if (resp.action == 'confirm') {
          tindex.value = -1

          if (!item.file_formmat) {
            $http.post('api/user/hdfs/file/folder/rename', {
              id: item.id,
              folder_name: resp.value
            }).then(() => {
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
            })
          } else {
            $http.post('api/user/hdfs/file/file/rename', {
              id: item.id,
              file_name: resp.value
            }).then(() => {
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
            })
          }
        }
      })
      .catch((error) => {
        console.log(error)
      })
}

onShareAppMessage(() => {
  return {
    title: '高清电子文档一键转换',
    imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/paper-tip-elf-app/logo2.jpg',
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

  toEdit(select[0])
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
      title: '不能选择文件夹',
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

  uni.showModal({
    title: '温馨提示',
    content: '删除后不可恢复，确认删除选中文件吗？',
    success(res) {
      if (res.confirm) {
        select.forEach(item => {
          toDelete(item, false)
        })
      }
    },
  });
}
</script>
<style>
page {
  --wot-drop-menu-fs: 1rem;
  background: #F6F7F9;
}

.wd-input__suffix {
  padding-right: 20rpx;
}

.document-drop-menu .wd-drop-menu__list {
  background: none !important;
  font-weight: 500;
  font-size: 30rpx;
  color: #111111;
  margin-right: 38rpx;
}

.search-document {
  flex-grow: 1;
}

.search-document .wd-input {
  height: 80rpx;
  background: #F8F8F8;
  border-radius: 40rpx;
}
</style>
<style scoped lang="scss">
.page-title {
  color: #111111;
  background: #ffffff;
}

.banner {
  padding: calc(var(--page-title-height)) 0 30rpx;
  background: #ffffff;
}

.index-top {
  // padding: 0.8rem;
  --wot-card-margin: 0;
}

.index-header {
  position: relative;

  .index-title {
    background: #ffffff;
    padding: 0 30rpx 42rpx;
    font-size: 34rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;

    text {
      font-size: 30rpx;
      color: #FFFFFF;
      margin-right: 22rpx;
    }

    image {
      width: 101rpx;
    }
  }
}

.folder-box {
  margin-bottom: 40rpx;

  .folder-box-title {
    font-weight: 500;
    font-size: 30rpx;
    color: #111111;
    margin-bottom: 34rpx;
  }

  .folder-list {
    padding: 0 20rpx;
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    gap: 37rpx;

    .folder-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      image {
        height: 110rpx;
        margin-bottom: 20rpx;
      }

      .name {
        font-weight: 500;
        font-size: 24rpx;
        color: #111111;
      }
    }

    .add-folder {
      .add-icon {
        width: 120rpx;
        height: 120rpx;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 50rpx;
        color: #333333;
        background: #F5F5F5;
        border-radius: 20rpx;
        margin-bottom: 20rpx;
      }
    }
  }
}

.all-file {
  display: flex;
  align-items: center;
  justify-content: space-between;

  .left {
    font-weight: 500;
    font-size: 30rpx;
    color: #111111;

    .number {
      font-size: 24rpx;
      color: #999999;
    }
  }

  .right {
    font-size: 24rpx;
    color: #999999;
    display: flex;
    align-items: center;
    gap: 30rpx;

    .all {
      color: #448BFF;
    }
  }
}

.file-item-wrap {
  // background: #F6F7F9;
  padding: 10rpx 0;
  border-radius: 20rpx;
}

.file-item {
  display: flex;
  align-items: center;

  .left {
    flex-shrink: 0;
    margin-right: 18rpx;
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
      display: flex;
      align-items: center;

      text {
        &:nth-child(1) {
          font-weight: 500;
          font-size: 25rpx;
          color: #333333;
        }

        &:nth-child(2) {
          font-size: 22rpx;
          color: #999999;
        }
      }
    }

    .time {
      font-size: 24rpx;
      color: #999999;
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
  padding: 0.6rem;
}

.empty1 {
  text-align: center;
  font-size: 22rpx;
  color: #999999;
  margin-top: 30rpx;
}

.d-active {
  // background: rgba(247, 248, 252, 1);
  // border-radius: 8px;
  overflow: hidden;
  padding-bottom: 0.5rem;
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
  font-size: 24rpx;
  color: #111111;
  gap: 20rpx;
  margin-top: 40rpx;

  image {
    height: 23rpx;
  }
}

.d-grid_li1 {
}

.d-active .d-grid {
  display: grid;
}

.global-m {
  padding: 0 30rpx 80rpx;

  .header {
    display: flex;
    align-items: center;
    padding-bottom: 10rpx;
    font-weight: bold;
    font-size:  30rpx;

    text {
      flex-grow: 1;
    }

    image {
      margin-left: 10rpx;
      flex-shrink: 0;
    }
  }
}

.select-options {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 20rpx 100rpx 50rpx;
  box-shadow: 0rpx -5rpx 10rpx 0rpx rgba(190,196,211,0.32);
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-top: 2rpx solid #eeeeee;
  background: #ffffff;

  .d-grid_li {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    font-size: 24rpx;
    color: #111111;
    gap: 20rpx;
    margin-top: 20rpx;

    image {
      height: 30rpx;
      width: auto;
    }
  }
}
</style>