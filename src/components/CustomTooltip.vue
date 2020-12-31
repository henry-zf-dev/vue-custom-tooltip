<template>
  <div id="tooltip" role="tooltip">
    <div id="tooltipContent"></div>
    <div v-if="!hideArrow" id="arrow" data-popper-arrow></div>
  </div>
</template>

<script>
import {createPopper} from '@popperjs/core'

export default {
  name: 'CustomTooltip',
  props: {
    // tooltip 所依赖的样式
    // 需要通过该样式名，给页面元素绑定 mouseenter mouseleave 事件
    relyClass: {
      type: String,
      default: ''
    },
    // 显示 tooltip 时延
    openDelay: {
      type: Number,
      default: 500
    },
    // 隐藏 tooltip 时延
    hideDelay: {
      type: Number,
      default: 350
    },
    // tooltip 弹出位置
    placement: {
      type: String,
      default: 'top'
    },
    // tooltip 位置偏移量
    offset: {
      type: Array,
      default: () => {
        return [0, 15]
      }
    },
    // 是否隐藏小箭头
    hideArrow: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      instance: null,
      showTimer: null,
      hideTimer: null
    }
  },
  deactivated () {
    this.destroy()
  },
  destroyed () {
    this.destroy()
  },
  methods: {
    createTooltip (content) {
      const tooltip = document.querySelector('#tooltip')
      // tooltip 需要显示内容的 DOM 容器元素
      const tooltipContent = document.querySelector('#tooltipContent')
      this.instance = createPopper(content, tooltip, {
        placement: this.placement,
        modifiers: [
          {
            name: 'offset',
            options: {offset: this.offset}
          }
        ]
      })
      // 注意：外部调用时需要给元素指定 content 属性
      // tooltipContent 根据该属性，显示最终的内容，可以传递 html
      tooltipContent.innerHTML = content.getAttribute('content')
    },
    destroyTooltip () {
      if (this.instance) {
        this.instance.destroy()
        this.instance = null
      }
    },
    // 初始化，给 relyClass 元素绑定事件，该方法主要用于外部调用
    init () {
      this.destroy()
      const contentDom = document.getElementsByClassName(this.relyClass)
      // 所有需要绑定事件的 DOM 元素
      const contentArr = Array.from(contentDom)
      if (!contentArr.length) return
      const tooltip = document.querySelector('#tooltip')
      // 以下逻辑用于 tooltip 的延时显示与隐藏
      contentArr.forEach(content => {
        content.addEventListener('mouseenter', () => {
          clearTimeout(this.showTimer)
          clearTimeout(this.hideTimer)
          tooltip.removeAttribute('data-show')
          this.showTimer = setTimeout(() => {
            // 如果没有 content 属性，则不显示 tooltip
            if (!content.getAttribute('content')) return
            tooltip.setAttribute('data-show', '')
            this.createTooltip(content)
          }, this.openDelay)
        })
        content.addEventListener('mouseleave', () => {
          clearTimeout(this.hideTimer)
          this.hideTimer = setTimeout(() => {
            tooltip.removeAttribute('data-show')
            clearTimeout(this.showTimer)
            this.destroyTooltip()
          }, this.hideDelay)
        })
      })
      // 以下逻辑用于鼠标进入 tooltip 内容中时，取消 tooltip 的隐藏（用途：用户需要复制 tooltip 中的内容时）
      tooltip.addEventListener('mouseenter', () => {
        tooltip.setAttribute('data-show', '')
        clearTimeout(this.hideTimer)
      })
      tooltip.addEventListener('mouseleave', () => {
        tooltip.removeAttribute('data-show')
        clearTimeout(this.showTimer)
        clearTimeout(this.showTimer)
        this.destroyTooltip()
      })
    },
    // 清空所有绑定的事件，该方法主要用于外部调用
    destroy () {
      this.destroyTooltip()
      const contentDom = document.getElementsByClassName(this.relyClass)
      const contentArr = Array.from(contentDom)
      contentArr.forEach(content => {
        content.removeEventListener('mouseenter', () => {
        })
        content.removeEventListener('mouseleave', () => {
        })
      })
      const tooltip = document.querySelector('#tooltip')
      if (tooltip) {
        tooltip.removeEventListener('mouseenter', () => {
        })
        tooltip.removeEventListener('mouseleave', () => {
        })
      }
      clearTimeout(this.showTimer)
      clearTimeout(this.hideTimer)
      this.showTimer = null
      this.hideTimer = null
    }
  }
}
</script>

<style scoped>

  #tooltip {
    background: #333;
    border-radius: 4px;
    color: white;
    font-weight: bold;
    padding: 4px 8px;
    font-size: 12px;
    z-index: 2000;
    display: none;
  }

  #arrow,
  #arrow::before {
    position: absolute;
    width: 8px;
    height: 8px;
    z-index: -1;
  }

  #arrow::before {
    content: '';
    transform: rotate(45deg);
    background: #333;
  }

  #tooltip[data-show] {
    display: block;
  }

  #tooltip[data-popper-placement^='top'] > #arrow {
    bottom: -4px;
  }

  #tooltip[data-popper-placement^='bottom'] > #arrow {
    top: -4px;
  }

  #tooltip[data-popper-placement^='left'] > #arrow {
    right: -4px;
  }

  #tooltip[data-popper-placement^='right'] > #arrow {
    left: -4px;
  }

</style>
