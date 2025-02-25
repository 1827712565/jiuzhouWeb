<template>
    <view class="setting">
        <view class="sz-item">
            <!-- 手机号码 -->
            <view class="menu-item" @click="sjhm">
              <view class="menu-left">
                <image class="menu-icon" src="/static/iconfont/phone.svg" mode="aspectFit"/>
                <text class="menu-title">手机号码</text>
              </view>
              <view class="menu-right">
                 <text class="menu-value">{{ userInfo.user_mobile }}</text>
                 <text class="iconfont icon-right">></text>
              </view>
            </view>
            <!-- 邮箱 -->
            <view class="menu-item" @click="yx">
              <view class="menu-left">
                <image class="menu-icon" src="/static/iconfont/email.svg" mode="aspectFit"/>
                <text class="menu-title">邮箱</text>
              </view>
              <view class="menu-right">
                <text class="menu-value">{{ userInfo.user_mail }}</text>
                 <text class="iconfont icon-right">></text>
              </view>
            </view>

            <!-- 版本信息 -->
            <view class="menu-item" @click="versions">
              <view class="menu-left">
                <image class="menu-icon" src="/static/iconfont/bbxx.svg" mode="aspectFit"/>
                <text class="menu-title">版本信息</text>
              </view>
              <view class="menu-right">
                 <text></text>
                 <text class="iconfont icon-right">></text>
              </view>
            </view>
            <!-- 隐私协议 -->
            <view class="menu-item" @click="privacy">
              <view class="menu-left">
                <image class="menu-icon" src="/static/iconfont/ysxy.svg" mode="aspectFit"/>
                <text class="menu-title">隐私协议</text>
              </view>
              <view class="menu-right">
                 <text></text>
                 <text class="iconfont icon-right">></text>
              </view>
            </view>
            <!-- 关于我们 -->
            <view class="menu-item" @click="aboutUs">
              <view class="menu-left">
                <image class="menu-icon" src="/static/iconfont/gywm.svg" mode="aspectFit"/>
                <text class="menu-title">关于我们</text>
              </view>
              <view class="menu-right">
                 <text></text>
                 <text class="iconfont icon-right">></text>
              </view>
            </view>

            

        </view>
    </view>
</template>

<script>
export default {
    data(){
        return{
            userInfo: {}
        }
    },
    onShow() {
        if(uni.getStorageSync('token')) {
            this.getUserInfo();
        } else {
            uni.navigateTo({
                url: '/pages/login/login'
            });
        }
    },
    methods:{
        getUserInfo() {
            this.$service("/api/user/GetUserInfo", "post")
                .then((res) => {
                    if (res.data.code === 0) {
                        this.userInfo = res.data.data;
                    } else if (res.code == 1) {
                        uni.showToast({
                            title: res.message,
                            icon: 'none'
                        });
                    }
                })
                .catch((err) => {
                    console.error('请求失败:', err);
                    uni.showToast({
                        title: '请求失败，请稍后再试',
                        icon: 'none'
                    });
                });
        },
        sjhm(){
            uni.navigateTo({
                url:'/pages/bind-phone/bind-phone'
            })
        },
        yx(){
            uni.navigateTo({
                url:'/pages/bind-email/bind-email'
            })
        },
        versions(){
          uni.navigateTo({
            url:'/pages/setting/versions'
          })
        },
        privacy(){
          uni.navigateTo({
            url:'/pages/setting/privacy'
          })
        },
        aboutUs(){
          uni.navigateTo({
            url:'/pages/setting/aboutUs'
          })
        }
    }
}

</script>

<style>
.setting{
  margin-top: 30rpx;
}
.menu-right{
  width: 60%;
  display: flex;
  justify-content: space-between;
}
.menu-value{
  color: #7a7373;
  /* margin-right: 30rpx; */
}
.sz-item{
    /* width: 100%; */
    height: 100%;
    margin-left: 24rpx;
    margin-right: 24rpx;
}
.menu-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 32rpx 24rpx;
  position: relative;
  background: #2a2a2a;
  border-radius: 16rpx;
  color: #fff;

  &:not(:last-child)::after {
    content: '';
    position: absolute;
    left: 24rpx;
    right: 24rpx;
    bottom: 0;
    height: 2rpx;
    background: rgba(255, 255, 255, 0.1);
  }

  .menu-icon {
    width: 40rpx;
    height: 40rpx;
    filter: invert(1);
    opacity: 0.5;
  }

  &:active {
    background: rgba(255, 255, 255, 0.05);
  }

  &.menu-item-disabled {
    pointer-events: none;

    .menu-icon {
      opacity: 0.3;
    }

    .icon-right {
      display: none;
    }
  }
}
.menu-left {
  display: flex;
  align-items: center;
  gap: 16rpx;
}
 /* 适配 iPhone X 及以上机型 */
@supports (padding-bottom: constant(safe-area-inset-bottom)) or (padding-bottom: env(safe-area-inset-bottom)) {
  .page-container {
    min-height: calc(100vh - constant(safe-area-inset-bottom));
    min-height: calc(100vh - env(safe-area-inset-bottom));
  }
}
</style>