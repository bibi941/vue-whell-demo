/**
* @create-date :2019-Jan-Sunday-10:31 PM
* @author : fangXinRui
*/
<template>
  <div class="ash-sub-nav" :class="{active,vertical}" v-click-outside="close">
    <span class="ash-sub-nav-label" @click="onClick">
      <slot name="title"></slot>
      <span class="ash-sub-nav-icon" :class="{open,vertical}">
        <icon name="left"></icon>
      </span>
    </span>
    <template v-if="vertical">
      <transition @enter="enter" @after-enter="afterEnter" @leave="leave">
        <div class="ash-sub-nav-popover" :class="{vertical}" v-show="open">
          <slot></slot>
        </div>
      </transition>
    </template>
    <template v-else>
      <transition name="slide-fade">
        <div class="ash-sub-nav-popover" v-show="open">
          <slot></slot>
        </div>
      </transition>
    </template>
  </div>
</template>

<script>
  import Icon from '../icon'
  import ClickOutside from '../click-outside'

  export default {
    name: 'ash-sub-nav',
    inject: ['root', 'vertical'],
    components: {Icon},
    directives: {ClickOutside},
    props: {
      name: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        open: false
      }
    },
    created() {

    },
    mounted() {
    },
    computed: {
      active() {
        return this.root.namePath.indexOf(this.name) >= 0
      }
    },
    methods: {
      enter(el, done) {
        el.style.height = 'auto'
        let {height} = el.getBoundingClientRect()
        el.style.height = '0'
        el.getBoundingClientRect()
        el.style.height = `${height}px`
        el.addEventListener('transitionend', () => {
          done()
        })
      },
      afterEnter(el) {
        el.style.height = 'auto'
      },
      leave(el, done) {
        let {height} = el.getBoundingClientRect()
        el.style.height = `${height}px`
        el.getBoundingClientRect()
        el.style.height = '0'
        el.addEventListener('transitionend', () => {
          done()
        })
      },
      onClick() {
        this.open = !this.open
      },
      close() {
        this.open = false
      },
      updateNamePath() {
        this.root.namePath.unshift(this.name)
        if (this.$parent.updateNamePath) {
          this.$parent.updateNamePath()
        } else {//顶级菜单为 nav-item
        }
      }
    }
  }
</script>

<style scoped lang='scss' type="text/scss">
  @import "var";
  .slide-fade-enter-active {
    transition: all .3s ease;
  }
  .slide-fade-leave-active {
    transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }
  .slide-fade-enter, .slide-fade-leave-to
    /* .slide-fade-leave-active for below version 2.1.8 */ {
    transform: translateX(-10px);
    opacity: 0;
  }
  .ash-sub-nav {
    position: relative;
    color: $grey-lv3;
    &.vertical {
      color: $grey-lv6;
      .ash-sub-nav-label {
        &:hover {
          background: $purple-lv0;
        }
      }

    }
    &.active {
      position: relative;
      color: $grey-lv6;
      &.vertical {
        &::after {
          border: none;
        }
      }
      &::after {
        content: '';
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        border-bottom: 2px solid $purple-lv2;
      }
    }
    &-icon {
      display: none;
    }
    &-label {
      padding: 10px 20px;
      display: block;
      /*fixme 文档现实错误暂时隐藏*/
      /*width: 100%;*/
    }
    &-popover {
      transition: all 300ms;
      z-index: 5;
      &.vertical {
        padding-left: 1em;
        overflow: hidden;
        position: static;
        box-shadow: none;
        border-radius: 0;
        border: none;
        margin-top: 0;
      }
      margin-top: 4px;
      background: white;
      position: absolute;
      top: 100%;
      left: 0;
      white-space: nowrap;
      border: 1px solid $grey-lv1;
      border-radius: $border-radius;
      box-shadow: $box-shadow-light;
    }
  }

  //非第一层
  .ash-sub-nav .ash-sub-nav {
    &.active {
      &::after {
        display: none;
      }
    }
    .ash-sub-nav-label {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .ash-sub-nav-popover {
      z-index: 5;
      top: 0;
      left: 100%;
      margin-left: 8px;
      &.vertical {
        margin-left: 0;
      }
    }
    .ash-sub-nav-icon {
      transition: all 300ms;
      display: inline-flex;
      &.vertical{
        transform: rotate(90deg);
        &.open {
          transform: rotate(270deg);
        }
      }
      svg {
        fill: $grey-lv3
      }
      &.open {
        transform: rotate(180deg);
      }
    }
  }

</style>
