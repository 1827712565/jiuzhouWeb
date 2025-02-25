<template>
  <view class="page-container">
    <!-- 顶部资产卡片 -->
    <view class="asset-card">
      <view class="card-header" @click="showInfoLayer">
        <text class="title">账户总资产折合(USDT)</text>
        <text class="info-icon">i</text>
      </view>
      <text class="total-amount">{{ totalAssets }}</text>
      <view class="action-buttons">
        <view class="action-item" @click="handleDeposit">
          <image class="action-icon" src="/static/iconfont/cz.svg" mode="aspectFit"/>
          <text class="action-text">充值</text>
        </view>
        <view class="action-item" @click="handleWithdraw">
          <image class="action-icon" src="/static/iconfont/tx.svg" mode="aspectFit"/>
          <text class="action-text">提现</text>
        </view>
        <view class="action-item" @click="handleTransfer">
          <image class="action-icon" src="/static/iconfont/dh.svg" mode="aspectFit"/>
          <text class="action-text">兑换</text>
        </view>
      </view>
    </view>

    <!-- 资产说明图层 -->
    <view class="mask" v-show="isShowLayer" @click="hideInfoLayer">
      <view class="mask-content">
        将您账户中的所有资产（包括加密货币、法币、代币等）按照当前市场价格统一换算为 USDT（Tether） 的价值总和。
      </view>
    </view>

    <!-- 资产列表标题 -->
    <view class="assets-header">
      <text class="section-title">资产列表</text>
    </view>

    <!-- 资产列表 -->
    <view class="assets-list">
      <view v-for="(asset, index) in assetsList" :key="index" class="asset-item" @click="goToDetail(asset)">
        <view class="asset-left">
          <text class="coin-name">{{ asset.currency }}</text>
          <view class="asset-amounts">
            <text class="available">可用 {{ asset.money }}</text>
            <text class="frozen">冻结 {{ asset.freeze_money }}</text>
          </view>
        </view>
        <view class="asset-right">
          <view class="asset-value">
            <text class="value-label">折合(USDT)</text>
            <text class="value-amount">{{ asset.valueInUSDT }}</text>
          </view>
          <text class="iconfont icon-right">></text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      totalAssets: '0',
      assetsList: [
        {
          currency: 'BTC',
          money: '0',
          freeze_money: '0',
          valueInUSDT: '0'
        },

      ],
      isShowLayer: false
    }
  },
  onShow() {
    if(uni.getStorageSync('token')) {
      this.getWalletList();
    } else {
      uni.navigateTo({
        url: '/pages/login/login'
      });
    }
  },
  methods: {
    getWalletList() {
      this.$service("/api/financial/WalletList", "post")
        .then((res) => {
          res = res.data;
          if (res.code === 0) {
            this.assetsList = res.data || [];
            // 计算总资产

          } else if (res.code == 1) {
            uni.showToast({
              title: res.message,
              icon: 'none'
            });
          }
        }).catch((err) => {
          console.error('请求失败:', err);
          uni.showToast({
            title: '请求失败，请稍后再试',
            icon: 'none'
          });
        });
    },
    handleDeposit() {
      uni.navigateTo({
        url: '/pages/deposit/deposit'
      })
    },
    handleWithdraw() {
      uni.navigateTo({
        url: '/pages/deposit/withdraw'
      })
    },
    handleTransfer() {
      uni.navigateTo({
        url: '/pages/deposit/transfer'
      })
    },
    goToDetail(asset) {
      uni.navigateTo({
        url: `/pages/asset-detail/asset-detail?symbol=${asset.symbol}`
      })
    },
    showInfoLayer() {
      this.isShowLayer = true;
    },
    hideInfoLayer() {
      this.isShowLayer = false;
    }
  }
}
</script>

<style lang="scss" scoped>
.page-container {
  min-height: 100vh;
  background-color: #1c1c1c;
  padding: 30rpx;
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}

.asset-card {
  background: #2a2a2a;
  border-radius: 16rpx;
  padding: 30rpx;
  margin-bottom: 24rpx;

  .card-header {
    display: flex;
    align-items: center;
    gap: 8rpx;
    margin-bottom: 16rpx;

    .title {
      font-size: 28rpx;
      color: rgba(255, 255, 255, 0.5);
    }

    .info-icon {
      width: 32rpx;
      height: 32rpx;
      line-height: 32rpx;
      text-align: center;
      border-radius: 50%;
      border: 2rpx solid rgba(255, 255, 255, 0.5);
      color: rgba(255, 255, 255, 0.5);
      font-size: 24rpx;
    }
  }

  .total-amount {
    font-size: 48rpx;
    color: #ffffff;
    font-weight: 500;
    margin-bottom: 32rpx;
    display: flex;
    justify-content: center;
  }
}

.action-buttons {
  display: flex;
  justify-content: space-around;
  
  .action-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8rpx;

    .action-icon {
      width: 48rpx;
      height: 48rpx;
      filter: invert(1);
      opacity: 0.5;
    }

    .action-text {
      font-size: 24rpx;
      color: #ffffff;
    }
  }
}

.assets-header {
  margin: 32rpx 0 16rpx;
  
  .section-title {
    font-size: 32rpx;
    color: #ffffff;
    font-weight: 500;
  }
}

.assets-list {
  background: #2a2a2a;
  border-radius: 16rpx;
  overflow: hidden;
}

.asset-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 32rpx 24rpx;
  
  &:not(:last-child) {
    border-bottom: 2rpx solid rgba(255, 255, 255, 0.1);
  }

  &:active {
    background: rgba(255, 255, 255, 0.05);
  }
}

.asset-left {
  .coin-name {
    font-size: 32rpx;
    color: #ffffff;
    font-weight: 500;
    margin-bottom: 8rpx;
  }

  .asset-amounts {
    display: flex;
    gap: 24rpx;
    
    .available, .frozen {
      font-size: 24rpx;
      color: rgba(255, 255, 255, 0.5);
    }
  }
}

.asset-right {
  display: flex;
  align-items: center;
  gap: 16rpx;

  .asset-value {
    text-align: right;
    
    .value-label {
      display: block;
      font-size: 24rpx;
      color: rgba(255, 255, 255, 0.5);
      margin-bottom: 4rpx;
    }

    .value-amount {
      font-size: 28rpx;
      color: #ffffff;
    }
  }

  .icon-right {
    font-size: 24rpx;
    color: rgba(255, 255, 255, 0.3);
  }
}

// 适配 iPhone X 及以上机型
@supports (padding-bottom: constant(safe-area-inset-bottom)) or (padding-bottom: env(safe-area-inset-bottom)) {
  .page-container {
    min-height: calc(100vh - constant(safe-area-inset-bottom));
    min-height: calc(100vh - env(safe-area-inset-bottom));
  }
}

.mask {
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  z-index: 9;
  
  .mask-content {
    width: 70%;
    height: 20%;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background: #2a2a2a;
    padding: 40rpx;
    border-radius: 16rpx;
    color: #fff;
    font-size: 28rpx;
  }
}
</style> 