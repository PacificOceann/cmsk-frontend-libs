<template>
  <div :class="mode==='pop'?'mask':''" v-if="value">
    <div :class="mode==='pop'?'verifybox':''" :style="{'max-width': imgSize.width.toString().indexOf('%') > -1 ?
    imgSize.width : parseInt(imgSize.width)+30+'px'}">
      <div class="verifybox-top"  v-if="mode==='pop'">
        {{ title }}
        <span class="verifybox-close" @click="closeBox">
          <i class="iconfont icon-close"></i>
        </span>
      </div>
      <div class="verifybox-bottom" :style="{padding:mode==='pop'?'15px':'0'}">
        <!-- 验证码容器 -->
        <VerifySlide
          :type="verifyType"
          :image-info="imageInfo"
          :mode="mode"
          :check-status="checkStatus"
          :vSpace="vSpace"
          :explain="explain"
          :check-tips="checkTips"
          :imgSize="imgSize"
          :blockSize="blockSize"
          :barSize="barSize"
          :defaultImg = "defaultImg"
          :refresh="refresh"
          @actionEnd="actionEndHandle"
          @success="success"
          @checkStatusChange="checkStatusChange"
          ref="instance"
        />
      </div>
    </div>
  </div>
</template>
<script lang="ts">
/**
 * Verify 验证码组件
 * @description 分发验证码使用
 **/
import { Vue, Component, Prop, Ref, Model, Emit } from 'vue-property-decorator'
import VerifySlide from './Verify/VerifySlide.vue'
import {
  CaptchaPositionType,
  ImageSizeType,
  ImageInfoType,
  CheckParamsType,
  CheckStatusType
} from '@/@types/component'

@Component({
  name: 'SliderCaptcha',
  components: {
    VerifySlide
  }
})

export default class extends Vue {
  @Model('input', { type: Boolean, default: false }) public value!: boolean
  // 验证码出现类型 pop=>弹窗 fixed=>悬浮
  @Prop({ default: 'pop' }) private mode!: CaptchaPositionType
  // 标题
  @Prop({ default: '请完成安全验证' }) private title!: string
  // 验证码当前校验状态
  @Prop({ default: 'waiting' }) private checkStatus!: CheckStatusType
  // 距离顶部或者底部的距离
  @Prop({ default: 5 }) private vSpace!: number
  // 滑块说明文字
  @Prop() private explain!: string
  // 验证提示文字
  @Prop() private checkTips!: string
  // 重新刷新的方法
  @Prop({ default: () => { return false } }) private refresh!: () => void
  // 图片大小
  @Prop({ default: () => ({ width: 310, height: 155 }) }) private imgSize!: ImageSizeType
  // 图片信息
  @Prop({ default: () => ({ backImgBase: '', blockBackImgBase: '' }) }) private imageInfo!: ImageInfoType
  // 小方块的大小
  @Prop({ default: () => ({ width: 50, height: 50 }) }) private blockSize!: ImageSizeType
  // 滑块的大小
  @Prop({ default: () => ({ width: 310, height: 40 }) }) private barSize!: ImageSizeType

  @Ref('instance') readonly componentInstance!: any

  // 内部验证码类型
  private verifyType = '1'
  // 默认图片
  private defaultImg: string = require('@/assets/image/default.jpg')

  @Emit('input')
  private input () {
    return false
  }

  public closeBox () {
    this.input()
  }

  @Emit('success')
  private success (captchaVerification: string) {
    this.closeBox()
    return captchaVerification
  }

  @Emit('actionEnd')
  private actionEnd (data: CheckParamsType) {
    return data
  }

  private actionEndHandle (params: CheckParamsType) {
    this.actionEnd(params)
  }

  @Emit('checkStatusChange')
  private checkStatusChange (params: CheckStatusType) {
    return params
  }
}
</script>
<style src="./verify.scss" lang="scss"></style>
