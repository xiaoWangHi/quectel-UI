<template>
  <div class="full full-el-tabs">
    <el-tabs
      v-model="activeName"
      :lazy="true"
      :tab-position="tabPosition"
      :type="tabType"
      :closable="closable"
      :stretch='stretch'
      @tab-remove="tabRemove"
      @tab-click='tabClick'
    >
      <el-tab-pane
        v-for="item in tabsList"
        :key="item.index"
        :label="item.label"
        :name="item.name"
        class="scrollbar"
        lazy
        :disabled='item.disabled'
      >
        <transition v-if="transition" name="slide-fade" mode="in-out">
          <inner-render v-if="renderShow" :render="item.render"></inner-render>
        </transition>
        <inner-render v-else :render="item.render"></inner-render>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  name: 'QIndexTabs',
  props: {
    remote: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    transition: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    innerList: {
      type: Array,
      required: true
    },
    tabPosition: {
      type: String,
      default: 'top'
    },
    tabType: {
      type: String,
      default: '' // card、border-card
    },
    stretch: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    closable: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    innerDate: {}
  },
  data() {
    return {
      activeName: '',
      renderShow: true
    }
  },
  mounted() {
    // console.log(this.$route.params.activeName,  this.tabsList[0].name)
    if(!this.remote) {
      this.activeName = this.$route.params.activeName ? this.$route.params.activeName : this.tabsList[0].name
    }
  },
  methods: {
    /**
     * 标签删除
    */
    tabRemove(item) {
      this.$emit('tabRemove', item)
    },
    /**
    * 标签点击
   */
    tabClick(item) {
      this.renderShow = false
      this.$nextTick(() => {
        this.renderShow = true
      })
      this.$emit('tabClick', item)
    },
    activeInit(el) {
      if (el) {
        if (this.$refs[el]) {
          this.$refs[el].init(this.innerDate, this.getState(el))
        } else {
          setTimeout(() => {
            this.activeInit(el)
          }, 1000)
        }
      }
    },
    /**
     * 获取状态
     */
    getState(el) {
      const state = this.tabsList.filter(e => e.name === el)
      if (state.length) {
        return state[0].state
      } else {
        return ''
      }
    }
  },
  watch: {
    activeName: {
      handler(el) {
        setTimeout(() => {
          this.activeInit(el)
        }, 200)
      },
      immediate: true
    }
  },
  computed: {
    tabsList() {
      return this.innerList.filter(
        item => !item.hidden
      )
    }
  },
  components: {
    innerRender: {
      functional: true,
      props: {
        render: {
          type: Function,
          required: true
        }
      },
      render: (h, c) => c.props.render(h, c)
    }
  }
}
</script>
<style lang="scss" scoped>
.full {
    height: 100%;
    max-width: 100% !important;
    width: 100%;
    overflow: hidden;
}
.full-el-tabs {
  background: $--color-white;
  padding: 6px 20px;
    .el-tabs {
        height: 100%;
        overflow: hidden;
        @include flex-column;
        .el-tabs__content {
            flex: 1;
            >div {
                height: 100%;
                overflow-y: auto;
            }
        }
        &.el-tabs--left {
            @include fj(flex-start);
            flex-direction: row;
            .el-tabs__content {
                height: 100%;
                overflow-y: scroll;
            }
        }
    }
}
</style>
