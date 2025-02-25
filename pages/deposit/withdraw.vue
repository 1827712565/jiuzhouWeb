<template>
	<view class="withdraw">
		<!-- 银行卡 -->
		<view class="withdraw-card">
			<view class="withdraw-number">{{ formatBankCard('6541236514785256') }}</view>
			<view class="withdraw-text">中国农业银行</view>
		</view>
		<!-- 提现 -->
		 <view class="withdraw-money">
			<view class="withdraw-may">可提现金额 : 58741.98 元</view>
			<view class="withdraw-input">
				<view>￥</view>
				<input type="number" placeholder="请输入提现金额">
			</view>
		 </View>
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
		}
	}
</script>

<style>
.withdraw{
	padding: 20rpx;
}
.withdraw-card{
	width: 100%;
	height: 280rpx;
	background-color: #2a2a2a;
	border-radius: 20rpx;
	display: flex;
	flex-direction: column;
    align-items: center;
    justify-content: center;
	color: #fff;
}
.withdraw-number{
	margin-bottom: 40rpx;
	font-size: 48rpx;
}
.withdraw-money{
	margin-top: 30rpx;
	width: 94%;
	height: 200rpx;
	background-color: #2a2a2a;
	border-radius: 20rpx;
	padding: 20rpx;
	color: #fff;
}
.withdraw-input{
	/* background-color: gold; */
	display: flex;
	align-items: center;
	height: 100rpx;
	margin-top: 30rpx;
}
.withdraw-input>input{
	width: 100%;
	height: 80rpx;
	color: #fff;
}
.withdraw-input>view{
	font-size:42rpx;
	margin-right: 10rpx;
}
</style>