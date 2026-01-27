<script>
import { token } from '@/api/user'
import { toRouter } from '@/hooks/utils'
export default {
  onLaunch: function () {
    console.log('App Launch')
    uni.removeStorageSync("sign_order_no");

    let isModalShowing = false;
    uni.setStorageSync('$ossUrl', 'https://yonganpicture.oss-cn-shenzhen.aliyuncs.com/')
    uni.setStorageSync('$viewUrl', 'https://file.zyyttech.cn/onlinePreview?url=')
    uni.setStorageSync('$picsUrl', 'https://file.zyyttech.cn/picturesPreview?urls=')
    // uni.addInterceptor('request', {
    //   invoke(args) {
    //
    //     const token = uni.getStorageSync('token');
    //     if (token && args.url != '/api/login/wx') {
    //       args.header = {
    //         'Authorization': 'Bearer ' + token // 假设有一个getToken函数获取token
    //       }
    //     }
    //
    //     if (!args.url.includes('http')) {
    //       args.url = 'https://tools-api.xiaoohui.com' + args.url
    //     }
    //
    //     // let username = uni.getStorageSync('username')
    //     // if(!username && !args.url.includes('https://smapi.liwangtc.com')){
    //     //   if( args.url.indexOf('api/login') >= 0 || args.url.indexOf('api/sms') >= 0 ){
    //     //
    //     //   } else {
    //     //     if (!isModalShowing) {
    //     //       isModalShowing = true;
    //     //       uni.showModal({
    //     //         title: '提示',
    //     //         content: '您当前未登录或登录已失效，为了您有更好的体验，爱悦看需要您进行登录',
    //     //         showCancel: true,
    //     //         success: function (res) {
    //     //           if (res.confirm) {
    //     //             toRouter('/pages/login/index')
    //     //           } else if (res.cancel) {
    //     //             console.log('用户点击取消')
    //     //           }
    //     //         },
    //     //         complete: function () {
    //     //           isModalShowing = false;
    //     //         }
    //     //       })
    //     //     }
    //     //   }
    //     //
    //     //
    //     // }
    //   },
    //   success(args) {
    //     // 请求发送成功后做些什么
    //     // if( args.statusCode == 401 ){
    //     //   toRouter('/pages/login/index');
    //     // }
    //   },
    //   fail(err) {
    //     console.log( 'err', err );
    //
    //     // 请求发送失败后做些什么
    //   }
    // })
    // token()
    // uni.getStorageSync('username') ? '': toRouter('/pages/login/index')

    // 小程序版本更新
    const updateManager = uni.getUpdateManager();

    updateManager.onUpdateReady(() => {
      uni.showModal({
        title: '更新提示',
        content: '新版本已经准备好，是否重启小程序？',
        success(res) {
          if (res.confirm) {
            updateManager.applyUpdate();
          }
        },
      });
    });

    updateManager.onUpdateFailed((err) => {
      console.log('版本下载失败原因', err);

      uni.showToast({
        title: '新版本下载失败，请稍后再试',
        icon: 'none',
      });
    });
  },
  onShow: function (res) {
    console.log('App Show')

    // 场景值1038：从被打开的小程序返回
    if (res.scene === 1038) {
      const { appId, extraData } = res.referrerInfo;

      // appId为wxbd687630cd02ce1d：从签约小程序跳转回来
      if (appId === 'wxbd687630cd02ce1d') {
        if (extraData.return_code === 'SUCCESS') {
          let sign_order_no = uni.getStorageSync("sign_order_no");
          uni.setStorageSync('drawCount', 1);

          if (sign_order_no) {
            // 跳转到红包领取页面
            wx.navigateTo({
              url: '/pages/receiveRedPacket/receiveRedPacket',
            });
          } else {
            // 跳转到订购成功页面
            wx.navigateTo({
              url: '/pages/success/success',
            });
          }
        } else {
          // let sign_order_no = uni.getStorageSync("sign_order_no");
          // uni.setStorageSync('drawCount', 0);
          wx.showToast({
            title: '签约失败',
            icon: 'none',
          });

          // if (sign_order_no) {
          //   setTimeout(() => {
          //     // 跳转到红包领取页面
          //     wx.navigateTo({
          //       url: '/pages/lottery/lottery',
          //     });
          //   }, 500)
          // }
        }
      }
    }
  },
  onHide: function () {
    console.log('App Hide')
  }
}
</script>

<style>
@import url('@/unistyle.scss');
page{
  --wot-grid-item-padding: 0.4rem 0 !important;
  --wot-checkbox-checked-color: #00D7AD;
}
body{
  font-size: 1rem;
}
/*每个页面公共css */
</style>

<style lang="scss">
.count-tip-container {
  position: relative;
  padding: 36rpx 0;

  .close {
    text-align: right;
    position: absolute;
    top: 20rpx;
    right: 20rpx;

    image {
      width: 46rpx;
    }
  }

  .tip1 {
    padding: 0 30rpx;
    font-weight: 500;
    font-size: 30rpx;
    color: #62461E;
    margin-bottom: 20rpx;
  }

  .tip2 {
    padding: 0 30rpx;
    font-size: 26rpx;
    color: #97948F;
    margin-bottom: 38rpx;
  }

  .icon-list {
    padding: 0 30rpx;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 33rpx;

    .icon-item {
      width: 110rpx;
      height: 110rpx;
      background: #FFFFFF;
      border-radius: 20rpx;
      display: flex;
      align-items: center;
      justify-content: center;

      image {
        width: 56rpx;
      }
    }
  }

  .vip-list {
    background: #FFFFFF;
    border-radius: 30rpx 30rpx 0rpx 0rpx;
    padding: 30rpx;

    .title {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 26rpx;
      color: #999999;
      margin-bottom: 25rpx;
    }

    .list-detail {
      display: flex;
      flex-direction: column;
      gap: 20rpx;
      margin-bottom: 26rpx;

      .detail {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 32rpx;
        background: #FFFFFF;
        border-radius: 20rpx;
        border: 2px solid #E8E8E8;

        &.active {
          background: linear-gradient(0deg, #FFF7EC 0%, #FBFFFF 100%);
          border: 2px solid #DDDBCE;

          .right {
            text {
              &:nth-child(1) {
                color: #E25946;
              }

              &:nth-child(2) {
                color: #E25946;
              }
            }
          }
        }

        .left {
          display: flex;
          flex-direction: column;
          gap: 22rpx;

          text {
            &:nth-child(1) {
              font-weight: 500;
              font-size: 30rpx;
              color: #62461E;
            }

            &:nth-child(2) {
              font-size: 24rpx;
              color: #97948F;
            }
          }
        }

        .right {
          text {
            &:nth-child(1) {
              font-weight: 500;
              font-size: 30rpx;
              color: #62461E;
            }

            &:nth-child(2) {
              font-weight: bold;
              font-size: 55rpx;
              color: #62461E;
            }
          }
        }
      }
    }

    .buy {
      width: 690rpx;
      height: 90rpx;
      margin: 0 auto;
      background: linear-gradient(90deg, #FCE6C1 0%, #F6D19A 100%);
      border-radius: 45rpx;
      font-weight: 500;
      font-size: 34rpx;
      color: #6F4C14;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
}

.d-flex-center {
  background: transparent !important;
  padding: 40rpx 0 0 !important;
  font-weight: bold !important;
}

// 公共样式
.wd-button.is-primary {
  background: #448BFF !important;
  color: #ffffff !important;
}

.wd-message-box__title {
  font-weight: bold !important;
  padding-bottom: 50rpx !important;
}

.wd-message-box__content .wd-input__inner {
  border: 2rpx solid #C0C0C0 !important;
  padding: 0 20rpx !important;
  border-radius: 10rpx !important;
  font-size: 22rpx !important;
}

.wd-message-box__content .wd-input:after {
  display: none !important;
}
</style>
