<template>
	<view class="addBankCard">
		<!-- 输入框 -->
		<view class="addBankCard-text">请输入银行</view>
		<input />
		<view class="addBankCard-text">请输入银行卡号</view>
		<input type="number" />
		<view class="addBankCard-text">请输入持卡人姓名</view>
		<input  type="text"/>
		<!-- 按钮 -->
		<view class="addBankCard-button">
			<button>添加</button>
		</view>
		
	</view>
</template>

<script>
</script>

<style>
	.addBankCard{
		width: 100%;
		height: 100vh;
		/* background-color: aliceblue; */
		color: #fff;
		padding: 0 30rpx;
		
	}
	input{
		width: 90%;
		height: 70rpx;
		background-color: #5c5c5c;
		border-radius: 5px;
		margin-bottom: 20rpx;
		padding-left: 10px;
	}
	.addBankCard-text{
		margin-bottom: 10rpx;
	}
	.addBankCard-button{
		width: 92%;
		height: 96rpx;
		margin-top: 60rpx;
	}
</style>