<template>
  <view class="zuni-slither_item"
    :style="calcTransformX">
    <view class="zuni-slither_item_content"
      @touchstart.stop="handleTouchStart"
      @touchmove.stop="handleTouchMove"
      @touchend.stop="handleTouchEnd">
      <slot></slot>
    </view>
    <view class="zuni-slither_item_actions"
      :style="{right: calcActionsPositionRight}">
      <view class="zuni-slither_item_action"
        v-for="(action, index) in actions"
        :style="{width: calcActionStyle(action.style).width, color: calcActionStyle(action.style).color, background: calcActionStyle(action.style).background, opacity: calcOpacity}"
        @click="triggerAction(index)"
        :key="index">
        <text class="zuni-slither_item_action_text">
          {{action.title}}
        </text>
      </view>
    </view>
  </view>
</template>

<script>
import './zuni-slither.less'
let SLITHER_INDEX = 0
export default {
  name: 'zuni-slither',
  props: {
    actions: {
      type: Array,
      default() {
        return [
          {
            title: '操作',
            style: {},
            trigger(index) {
              console.log(index)
            }
          }
        ]
      }
    },
    fade: {
      type: Boolean,
      default: false
    },
    activeIndex: {
      required: true,
      default: null
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      activeStatus: this.activeIndex,
      start: {
        x: 0,
        y: 0
      },
      end: {
        x: 0,
        y: 0
      },
      touchTarget: {
        isClick: false,
        previousX: 0,
        offsetX: 0,
        index: null,
        maxToSlide: 100
      }
    }
  },
  computed: {
    calcOpacity() {
      if (!this.fade) return 1
      return this.touchTarget.isMoving ||
        (this.activeStatus !== null &&
          this.activeIndex === this.touchTarget.index &&
          !this.touchTarget.isClick)
        ? 1
        : 0
    },
    calcTransformX() {
      if (this.touchTarget.index !== this.activeIndex) {
        return 'transform: translate(0, 0)'
      } else {
        return `transform: translate(${this.touchTarget.offsetX}px, 0)`
      }
    },
    calcActionsPositionRight() {
      const unit = this.getPlatformUnit()
      const right =
        this.actions
          .map(action => parseFloat(this.calcActionStyle(action.style).width))
          .reduce((total, width) => total + width) *
          -1 +
        unit
      return right
    }
  },
  methods: {
    handleClick(e) {
      const activeStatus = this.activeStatus !== null
      this.$emit('click', {
        event: e,
        activeStatus,
        index: this.touchTarget.index
      })
      this.$nextTick(() => (this.activeStatus = this.activeIndex))
    },
    handleTouchStart(e) {
      this.activeStatus = this.activeIndex
      if (!this.disabled) {
        this.start = {
          x: e.touches[0].clientX,
          y: e.touches[0].clientY
        }
        this.touchTarget.isClick = true
        this.touchTarget.previousX = e.touches[0].clientX
        this.touchTarget.offsetX = 0
        this.$emit('update:activeIndex', this.touchTarget.index)
      }
    },
    handleTouchMove(e) {
      let currentX = e.touches[0].clientX
      let currentY = e.touches[0].clientY
      let isDirectionX =
        Math.abs(currentY - this.start.y) <= Math.abs(currentX - this.start.x)

      this.touchTarget.isClick = false

      if (isDirectionX) {
        e.preventDefault()
        let diffX = currentX - this.touchTarget.previousX
        const offsetX = this.touchTarget.offsetX + diffX
        if (diffX >= 0) {
          this.touchTarget.offsetX = Math.min(0, offsetX)
        } else {
          this.touchTarget.offsetX = Math.max(
            -this.touchTarget.maxToSlide,
            offsetX
          )
        }
        this.touchTarget.previousX = currentX
      }
    },
    handleTouchEnd(e) {
      const isReached =
        Math.abs(this.touchTarget.offsetX) < this.touchTarget.maxToSlide / 3
      if (isReached) {
        this.touchTarget.offsetX = 0
        this.$emit('update:activeIndex', null)
      } else {
        this.touchTarget.offsetX = -this.touchTarget.maxToSlide
        this.activeStatus = this.activeIndex
        this.$emit('slide-out', this.touchTarget.index)
      }
      if (this.touchTarget.isClick) {
        this.handleClick(e)
      }
    },
    triggerAction(index) {
      this.actions[index] && this.actions[index].trigger(this.touchTarget.index)
    },
    calcActionStyle(styleOptions) {
      const unit = this.getPlatformUnit()
      const actionStyle = Object.assign(
        {
          width: 180,
          color: '#fff',
          background: '#D65649'
        },
        styleOptions
      )
      actionStyle.width = actionStyle.width + unit
      return actionStyle
    },
    getPlatformUnit() {
      return uni.getSystemInfoSync().platform === 'devtools' ? 'rpx' : 'upx'
    }
  },
  mounted() {
    this.touchTarget.index = SLITHER_INDEX
    SLITHER_INDEX++
    this.touchTarget.maxToSlide = uni.upx2px(
      Math.abs(parseFloat(this.calcActionsPositionRight))
    )
  },
  watch: {
    activeIndex() {
      if (this.touchTarget.index !== this.activeIndex) {
        this.touchTarget.offsetX = 0
      }
    }
  }
}
</script>
