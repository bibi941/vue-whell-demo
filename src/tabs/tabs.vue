<template>
  <div class="ash-tabs">
    <slot></slot>
  </div>
</template>

<script>
  import Vue from 'vue'

  export default {
    name: 'ash-tabs',
    props: {
      selected: {
        type: String,
        required: true
      },
      direction: {
        type: String,
        default: 'horizontal',
        validator(value) {
          return ['horizontal', 'vertical'].indexOf(value) >= 0
        }
      }
    },
    data() {
      return {eventBus: new Vue()}
    },
    provide() {
      return {eventBus: this.eventBus}
    },
    mounted() {
      this.emitSelected()
      this.throwError()
    },
    methods: {
      emitSelected() {
        //找到selected 对应的 vm 并发送事件
        this.$children.forEach((vm) => {
          if (vm.$options.name === 'ash-tabs-head') {
            vm.$children.forEach((childVm) => {
              if (childVm.$options.name === 'ash-tabs-item' && childVm.name === this.selected) {
                this.eventBus.$emit('update:selected', this.selected, childVm)
              }
            })
          }
        })
      },
      throwError() {
        if (this.$children.length === 0) {
          throw new Error('tabs的子组件必须是tabs-head和tabs-body，但你为空')
        }
      }
    }
  }
</script>

<style scoped lang='scss' type="text/scss">

</style>
