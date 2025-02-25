<template>
    <view class="bankcard">
        <view class="bankcard-show">
            <view class="bankcard-image"><image src="../../static/logo.png"></image></view>
            <view class="bankcard-text">
				<view class="bankcard-bank">
					中国农业银行
				</view>
				<view class="bankcard-mark">
					{{ formatBankCard('6541236514785256') }}
				</view>
			</view>
        </view>
        <view class="bankcard-add" @click="bankcardadd">
            <view class="bankcard-add-left">
                <view class="bankcard-add-left-icon">+</view>
                <view class="bankcard-add-left-text">添加银行卡</view>
            </view>
        </view>
    </view>
</template>

<script>
export default {
    methods: {
        formatBankCard(cardNumber) {
            if (!cardNumber) return '';
            const first4 = cardNumber.substring(0, 4);
            const last4 = cardNumber.substring(cardNumber.length - 4);
            return `${first4} **** **** ${last4}`;
        },
		bankcardadd(){
			uni.navigateTo({
				url:'/pages/mine/addBankCard'
			})
		}
    }
}
</script>

<style>
.bankcard{
    /* width: 100%; */
    height: 100vh;
    margin: 0 30rpx;
}
.bankcard-add{
    width: 91%;
    height: 200rpx;
    background-color: #2a2a2a;
    border-radius: 15rpx;
    padding: 30rpx;
}
.bankcard-add-left{
    display: flex;
    justify-content: center;
    line-height: 200rpx;
    color: #fff;
}
.bankcard-add-left-icon{
    font-size: 50rpx;
    margin-right: 20rpx;
}
.bankcard-add-left-text{
    font-size: 30rpx;
    margin-top: 5rpx;
}
.bankcard-show{
	display: flex;
	width: 91%;
	height: 200rpx;
	background-color: #2a2a2a;
	border-radius: 15rpx;
	padding: 30rpx;
	margin-bottom: 30rpx;
}
.bankcard-image>image{
	width: 40px;
	height: 40px;
	border-radius: 50%;
}
.bankcard-text{
	color: #fff;
	margin-left: 40rpx;
}
.bankcard-bank{
	margin-top: 15rpx;
	margin-bottom: 60rpx;
}
</style>