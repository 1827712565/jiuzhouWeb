<template>
  <view class="authentication">
    <page-header :title="$t('authentication')"/>

    <scroll-view scroll-y class="container">
      <!-- 认证成功状态显示 -->
      <view v-if="isVerified" class="success-container">
        <view class="success-icon">✓</view>
        <text class="success-title">认证成功</text>
        <text class="success-desc">您的身份认证已通过审核</text>
      </view>

      <!-- 认证失败状态显示 -->
      <view v-else-if="isRejected" class="rejected-container">
        <view class="rejected-icon">!</view>
        <text class="rejected-title">认证失败</text>
        <text class="rejected-desc">{{ refusedMsg }}</text>
        <button class="resubmit-btn" @click="isRejected = false">重新提交</button>
      </view>

      <!-- 原有的表单内容 -->
      <view v-else class="form-container">
        <!-- 国籍选择 -->
        <view class="form-item">
          <text class="label">{{ $t('nationality') }}</text>
          <picker
            @change="handleNationalityChange"
            :value="nationalityIndex"
            :range="nationalities"
            class="picker"
          >
            <view class="picker-value">
              {{ nationalities[nationalityIndex] || $t('selectNationality') }}
              <text class="arrow-down">></text>
            </view>
          </picker>
        </view>

        <!-- 真实姓名 -->
        <view class="form-item">
          <text class="label">{{ $t('realName') }}</text>
          <input
            v-model="formData.real_name"
            type="text"
            :placeholder="$t('enterRealName')"
            class="input"
          />
        </view>

        <!-- 证件号码 -->
        <view class="form-item">
          <text class="label">{{ $t('idNumber') }}</text>
          <input
            v-model="formData.id_number"
            type="text"
            :placeholder="$t('enterIdNumber')"
            class="input"
          />
        </view>

        <!-- 证件照片上传 -->
        <view class="upload-section">
          <text class="section-title">{{ $t('uploadDocuments') }}</text>

          <!-- 正面照片 -->
          <view class="form-item">
            <text class="label">{{ $t('frontImage') }}</text>
            <view class="upload-box" @click="chooseImage('front')">
              <image
                v-if="formData.front_image"
                :src="formData.front_image"
                mode="aspectFit"
                class="preview-image"
              />
              <view v-else class="upload-placeholder">
                <text class="upload-icon">+</text>
                <text class="upload-text">{{ $t('uploadFront') }}</text>
              </view>
            </view>
          </view>

          <!-- 背面照片 -->
          <view class="form-item">
            <text class="label">{{ $t('backImage') }}</text>
            <view class="upload-box" @click="chooseImage('back')">
              <image
                v-if="formData.back_image"
                :src="formData.back_image"
                mode="aspectFit"
                class="preview-image"
              />
              <view v-else class="upload-placeholder">
                <text class="upload-icon">+</text>
                <text class="upload-text">{{ $t('uploadBack') }}</text>
              </view>
            </view>
          </view>

          <!-- 手持照片 -->
          <view class="form-item">
            <text class="label">{{ $t('handheldImage') }}</text>
            <view class="upload-box" @click="chooseImage('handheld')">
              <image
                v-if="formData.handheld_image"
                :src="formData.handheld_image"
                mode="aspectFit"
                class="preview-image"
              />
              <view v-else class="upload-placeholder">
                <text class="upload-icon">+</text>
                <text class="upload-text">{{ $t('uploadHandheld') }}</text>
              </view>
            </view>
          </view>
        </view>

        <!-- 提交按钮 -->
        <button
          class="submit-btn"
          :class="{'disabled': !isFormValid}"
          :disabled="!isFormValid"
          @click="handleSubmit"
        >
          {{ $t('submit') }}
        </button>
      </view>
    </scroll-view>
  </view>
</template>

<script>
import PageHeader from '@/components/page-header/page-header.vue'
import { uploadFile }  from '@/untils/request'

export default {
  components: {
    PageHeader
  },
  data() {
    return {
      nationalities: ['China', 'USA', 'UK', 'Japan', 'Korea'],
      nationalityIndex: 0,
      formData: {
        nationality: '',
        real_name: '',
        id_number: '',
        front_image: '',
        back_image: '',
        handheld_image: '',
        api_front_image: '',
        api_back_image: '',
        api_handheld_image: ''
      },
      isVerified: false,
      isRejected: false,
      refusedMsg: ''
    }
  },
  computed: {
    isFormValid() {
      return this.formData.nationality &&
        this.formData.real_name &&
        this.formData.id_number &&
        this.formData.front_image &&
        this.formData.back_image &&
        this.formData.handheld_image
    }
  },
  onShow() {
    this.GetUserMes()
  },
  methods: {
    async GetUserMes() {
      try {
        let res = await this.$service("/api/user/GetAuthentication", "post")
        res = res.data
        if (res.code === 0) {
          // 确保 res.data 是对象
          if(typeof res.data === 'string') {
            if(res.data === '已认证' || res.data === 'auth.authentication_approved') {
              this.isVerified = true
              this.isRejected = false
            } else if(res.data === '未认证' || res.data === 'auth.authentication_pending') {
              this.isVerified = false
              this.isRejected = false
            }
          } else if(typeof res.data === 'object') {
            if(res.data.status === 'error') {
              this.isRejected = true
              this.isVerified = false
              this.refusedMsg = res.data.refused_msg
            }
            // 只有当返回数据是对象时才更新 formData
            let types = this.nationalities
            this.verification_type = res.data.verification_type
            this.nationalityIndex = types.indexOf(res.data.nationality)
            // 合并数据而不是直接赋值
            Object.assign(this.formData, res.data)
          }
        }
      } catch (err) {
        uni.showToast({
          title: this.$t('verification.verifyFailed'),
          icon: 'none'
        })
      }
    },

    handleNationalityChange(e) {
      this.nationalityIndex = e.detail.value
      // 确保 formData 是对象
      if(typeof this.formData === 'string') {
        this.formData = {}
      }
      this.formData.nationality = this.nationalities[this.nationalityIndex]
    },
    async chooseImage(type) {
      try {
        const tipConfig = {
          front: {
            content: '请上传证件正面照片，确保照片清晰完整'
          },
          back: {
            content: '请上传证件背面照片，确保照片清晰完整'
          },
          handheld: {
            content: '请上传手持证件照片，需要能同时看清您的面部和证件信息'
          }
        }[type]

        const modalRes = await new Promise((resolve) => {
          uni.showModal({
            title: '上传提示',
            content: tipConfig.content,
            success: resolve
          })
        })

        if (!modalRes.confirm) return

        const imageRes = await uni.chooseImage({
          count: 1,
          sizeType: ['compressed'],
          sourceType: ['album', 'camera']
        })

        const filePath = imageRes.tempFilePaths[0]

        uni.showLoading({
          title: '上传中'
        })

        try {
          const uploadRes = await uploadFile(filePath, 'other')
          
          // 添加调试日志
          console.log('uploadRes:', uploadRes)
          console.log('typeof uploadRes:', typeof uploadRes)

          // 如果返回的是字符串，尝试解析 JSON
          let processedRes = uploadRes
          if (typeof uploadRes === 'string') {
            try {
              processedRes = JSON.parse(uploadRes)
            } catch (e) {
              console.error('JSON parse error:', e)
              throw new Error('上传返回格式错误')
            }
          }

          // 检查处理后的响应
          console.log('processedRes:', processedRes)

          if (!processedRes || typeof processedRes !== 'object') {
            throw new Error('上传返回格式错误')
          }

          const img_url = processedRes.img_url || processedRes.imgUrl || processedRes.url
          const api_url = processedRes.api_url || processedRes.apiUrl || processedRes.path

          if (!img_url || !api_url) {
            throw new Error('上传返回数据不完整')
          }

          // 更新对应的图片
          switch(type) {
            case 'front':
              this.formData.front_image = img_url
              this.formData.api_front_image = api_url
              break
            case 'back':
              this.formData.back_image = img_url
              this.formData.api_back_image = api_url
              break
            case 'handheld':
              this.formData.handheld_image = img_url
              this.formData.api_handheld_image = api_url
              break
          }

          uni.showToast({
            title: '上传成功',
            icon: 'success'
          })
        } catch (error) {
          console.error('上传错误:', error)
          uni.showToast({
            title: error.message || '上传失败',
            icon: 'none'
          })
        } finally {
          uni.hideLoading()
        }
      } catch (error) {
        console.error('选择图片错误:', error)
        uni.showToast({
          title: '选择图片失败',
          icon: 'none'
        })
      }
    },
    async handleSubmit() {
      if (!this.isFormValid) return

      try {
        uni.showLoading({
          title: this.$t('submitting')
        })

        // 检查必要的数据
        if (!this.formData || typeof this.formData !== 'object') {
          throw new Error('表单数据无效')
        }

        const res = await this.$service("/api/user/Authentication", "post", this.formData)
        
        if (res.data && res.data.code === 0) {
          uni.showToast({
            title: '提交成功',
            icon: 'success'
          })
          
          // 更新状态
          this.isVerified = true
          this.isRejected = false
          
          setTimeout(() => {
            uni.navigateBack()
          }, 1500)
        } else if (res.data && res.data.code === 1) {
          // 处理已提交过的情况
          uni.showToast({
            title: '正在认证中',
            icon: 'none',
            duration: 2000
          })
        } else {
          throw new Error(res.data?.message || '提交失败')
        }
      } catch (error) {
        console.error('提交错误:', error)
        // 如果是数组索引错误，显示"已经提交过"
        if (error.message && error.message.includes('Undefined array key')) {
          uni.showToast({
            title: '已经提交过',
            icon: 'none',
            duration: 2000
          })
        } else {
          uni.showToast({
            title: error.message || '提交失败，请稍后重试',
            icon: 'none',
            duration: 2000
          })
        }
      } finally {
        uni.hideLoading()
      }
    }
  }
}
</script>

<style lang="scss" scoped>

.authentication {
  min-height: 100vh;
  background: #1c1c1c;
  display: flex;
  flex-direction: column;

  .container {
    flex: 1;
    padding: 30rpx;
    box-sizing: border-box;
    height: calc(100vh - 88rpx - constant(safe-area-inset-top));
    height: calc(100vh - 88rpx - env(safe-area-inset-top));
  }

  .form-container {
    background-color: #2a2a2a;
    border-radius: 24rpx;
    padding: 30rpx;
    box-sizing: border-box;
  }

  .form-item {
    margin-bottom: 40rpx;
  }

  .label {
    display: block;
    margin-bottom: 20rpx;
    color: rgba(255, 255, 255, 0.6);
    font-size: 28rpx;
  }

  .input {
    width: 100%;
    height: 88rpx;
    background-color: rgba(255, 255, 255, 0.05);
    border-radius: 16rpx;
    padding: 0 30rpx;
    font-size: 28rpx;
    color: rgba(255, 255, 255, 0.9);
    box-sizing: border-box;

    &::placeholder {
      color: rgba(255, 255, 255, 0.3);
    }
  }

  .picker {
    width: 100%;
  }

  .picker-value {
    width: 100%;
    height: 88rpx;
    background-color: rgba(255, 255, 255, 0.05);
    border-radius: 16rpx;
    padding: 0 30rpx;
    font-size: 28rpx;
    color: rgba(255, 255, 255, 0.9);
    display: flex;
    align-items: center;
    justify-content: space-between;
    box-sizing: border-box;

    .arrow-down {
      font-size: 24rpx;
      color: rgba(255, 255, 255, 0.3);
    }
  }

  .upload-section {
    margin-bottom: 40rpx;

    .section-title {
      font-size: 32rpx;
      color: #fff;
      margin-bottom: 30rpx;
    }
  }

  .upload-box {
    width: 100%;
    height: 360rpx;
    background-color: rgba(255, 255, 255, 0.05);
    border-radius: 16rpx;
    overflow: hidden;
    box-sizing: border-box;

    .preview-image {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }

    .upload-placeholder {
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      .upload-icon {
        font-size: 64rpx;
        color: rgba(255, 255, 255, 0.3);
        margin-bottom: 16rpx;
      }

      .upload-text {
        font-size: 28rpx;
        color: rgba(255, 255, 255, 0.3);
      }
    }
  }

  .submit-btn {
    width: 100%;
    height: 88rpx;
    background-color: #1db954;
    color: #fff;
    border: none;
    border-radius: 16rpx;
    font-size: 32rpx;
    margin-top: 60rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
    line-height: 1;
    box-sizing: border-box;

    &.disabled {
      background-color: rgba(255, 255, 255, 0.08);
      color: rgba(255, 255, 255, 0.3);
    }
  }

  .success-container {
    background-color: #2a2a2a;
    border-radius: 24rpx;
    padding: 60rpx 30rpx;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .success-icon {
      width: 120rpx;
      height: 120rpx;
      background-color: #1db954;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 60rpx;
      color: #fff;
      margin-bottom: 30rpx;
    }

    .success-title {
      font-size: 36rpx;
      color: #fff;
      margin-bottom: 20rpx;
      font-weight: 500;
    }

    .success-desc {
      font-size: 28rpx;
      color: rgba(255, 255, 255, 0.6);
    }
  }

  .rejected-container {
    background-color: #2a2a2a;
    border-radius: 24rpx;
    padding: 60rpx 30rpx;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .rejected-icon {
      width: 120rpx;
      height: 120rpx;
      background-color: #ff4d4f;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 60rpx;
      color: #fff;
      margin-bottom: 30rpx;
      font-weight: bold;
    }

    .rejected-title {
      font-size: 36rpx;
      color: #fff;
      margin-bottom: 20rpx;
      font-weight: 500;
    }

    .rejected-desc {
      font-size: 28rpx;
      color: rgba(255, 255, 255, 0.6);
      text-align: center;
      margin-bottom: 40rpx;
      padding: 0 30rpx;
    }

    .resubmit-btn {
      width: 320rpx;
      height: 88rpx;
      background-color: #1db954;
      color: #fff;
      border: none;
      border-radius: 16rpx;
      font-size: 32rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 0;
      line-height: 1;
    }
  }
}
</style>
