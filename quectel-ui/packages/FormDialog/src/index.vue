<template>
  <div class="formDialog">
    <el-dialog
      :class="{'changeStyle': changeStyle}"
       v-loading="loading"
      :title="option.title"
      :visible="option.visible || false"
      :width="option.width || '50%'"
      append-to-body
      :fullscreen="fullscreen || option.fullscreen"
      :close-on-click-modal="false"
      :show-close='false'
      :before-close="close">
      <div slot="title" class="fj dialog-title">
        <div class="font16 h3"> <i class="iconfont" :class="option.titleIcon"></i> &nbsp; {{option.title}}</div>
        <div class="titleIcon">
          <slot name="icon-left"></slot>
          <el-tooltip class="item" effect="dark" :content="fullscreen ? $t('message.cancel') + $t('message.screen') : $t('message.screen')" placement="bottom">
            <i class="iconfont contentColor font24 pointer" :class="fullscreen ? 'iconsuoxiaotuichuquanpingshouhui' : 'iconquanping1'" @click="fullscreen = !fullscreen"></i>
          </el-tooltip>
          <slot name="icon-right"></slot>
          <i class="iconfont iconiconddgb contentColor font24 pointer" :class="!fullscreen && changeStyle ? 'closeIcon' : ''" @click="close"></i>
        </div>
      </div>
      <slot name="content-top"></slot>
      <!-- 表单内容 -->
      <el-form class="formContent" :ref="option.formRef || 'ruleForm'" :model="formModel" v-if="formArr && formArr.length" :label-width="option.labelWidth || '80px'" :size="option.fromSize || 'mini'" :rules='rules' :disabled="option.formDisabled">
        <el-form-item v-for="(item, index) in formArr" :key="index" :label="item.label" :prop="item.prop">
          <!--
            仅文字显示
           -->
          <div v-if="item.formType === 'text'">
            {{formModel[item.bind]}}
          </div>
          <!--
          输入框
          bind: 绑定值   --必填
          formType：input  --必填
          尺寸： size
          类型：type
          输入框头部图标 prefixIcon
          输入框尾部图标 suffixIcon
        -->
          <div v-if="item.formType === 'input'">
            <el-input v-model="formModel[item.bind]"
                      :type="item.type || 'text'"
                      :disabled="item.disabled"
                      :prefix-icon="item.prefixIcon"
                      :suffix-icon="item.suffixIcon"
                      :clearable="item.clearable"
                      :show-word-limit='item.showLimit'
                      :minlength='item.minlength'
                      :maxlength='item.maxlength'
                      :autosize='item.autosize || false'
                      :rows='item.rows'
                      :placeholder="item.placeholder || $t('message.pleaseEnter')">
            </el-input>
          </div>
          <!--
          输入框
          bind: 绑定值   --必填
          formType：date --必填
          显示类型： type  // 可选 year/month/date/week/ datetime/datetimerange/daterange
          范围选择时开始日期的占位内容 startPlaceholder
          范围选择时结束日期的占位内容 endPlaceholder
          是否使用箭头进行时间选择  timeArrowControl
          显示在输入框中的格式  format  // yyyy-MM-dd HH:mm:ss
          绑定值的格式。 valueFormat
          是否显示清除按钮  clearable
          禁用  disabled
          只读  readonly
        -->
          <div v-if="item.formType === 'date'">
            <el-date-picker
              v-model="formModel[item.bind]"
              :type="item.type || 'datetimerange'"
              :picker-options="item.pickerOptions || {}"
              :time-arrow-control="item.timeArrowControl || false"
              :format="item.format || 'yyyy-MM-dd HH:mm:ss'"
              :value-format="item.valueFormat || 'yyyy-MM-dd HH:mm:ss'"
              :clearable="item.clearable || true"
              :disabled="item.disabled || false"
              :readonly="item.readonly || false"
              range-separator="-"
              :start-placeholder="item.startPlaceholder || $t('message.StartDate')"
              :end-placeholder="item.endPlaceholder || $t('message.EndDate')">
            </el-date-picker>
          </div>
          <!--
          选择框
          -------------必填--------
          formType：select
          bind: 绑定值
          options: 数据源

          ------------可选----------
          尺寸： size
          多选 multiple
          多选时最多限制 multipleLimit  0不限制
          筛选 filterable
          清空 clearable
        -->
          <div v-if="item.formType === 'select'">
            <el-select v-model="formModel[item.bind]"
                       :placeholder="item.placeholder || $t('message.pleaseChoose')"
                       :disabled="item.disabled"
                       :multiple="item.multiple || false"
                       :filterable="item.filterable || false"
                       :clearable="item.clearable || false"
                       :multiple-limit="item.multipleLimit || 0">
              <el-option v-for="select in item.options"
                         :key="item.valueKey ? select[item.valueKey] : select.value"
                         :label="item.labelKey ? select[item.labelKey] : select.label"
                         :value="item.valueKey ? select[item.valueKey] : select.value">
              </el-option>
            </el-select>
          </div>
          <!--
            级联选择器
            -------------必填--------
            formType：cascader --必填
            bind: 绑定值   --必填
            options: 数据源  --必填

            ------------可选----------
            多选 multiple
            清空 clearable
            是否显示选中值的完整路径 showAllLevels
            是否严格的遵守父子节点不互相关联 checkStrictly
            emitPath 在选中节点改变时，是否返回由该节点所在的各级菜单的值所组成的数组，若设置 false，则只返回该节点的值
          -->
          <div v-if="item.formType === 'cascader'">
            <el-cascader v-model="formModel[item.bind]"
                         :options="item.options"
                         :clearable="item.clearable || false"
                         :show-all-levels="item.showAllLevels || false"
                         :placeholder="item.placeholder || $t('message.pleaseEnter')"
                         :disabled="item.disabled"
                         :props="{
                          expandTrigger: item.expandTrigger || 'click',
                          multiple: item.multiple || false,
                          checkStrictly: item.checkStrictly || false,
                          emitPath: item.emitPath || false,
                          value: item.valueKey || 'value',
                          label: item.labelKey || 'label',
                          children: item.childrenKey || 'children',
                          disabled: item.disabledKey || 'disabled'
                        }">
            </el-cascader>
          </div>
          <!--
            单选
            -------------必填--------
            formType：cascader --必填
            bind: 绑定值   --必填
            options: 数据源  --必填

            ------------可选----------
            禁用 disabled
            是否显示边框 border
          -->
          <div v-if="item.formType === 'radio'">
            <el-radio-group v-model="formModel[item.bind]" :disabled='item.disabled' :size="item.size || 'small'">
              <span v-if="item.button">
                <el-radio-button v-for="(radio, index) in item.options"
                                 :key="index"
                                 :label="item.valueKey ? radio[item.valueKey] : radio.value"
                                 :border='radio.border'>{{item.labelKey ? radio[item.labelKey] : radio.label}}</el-radio-button>
              </span>
              <span v-else>
                <el-radio v-for="(radio, index) in item.options"
                          :key="index"
                          :label="item.valueKey ? radio[item.valueKey] : radio.value"
                          :border='radio.border'>{{item.labelKey ? radio[item.labelKey] : radio.label}}</el-radio>
              </span>
            </el-radio-group>
          </div>

          <!--
            多选
            -------------必填--------
            formType：checkbox --必填
            bind: 绑定值   --必填
            options: 数据源  --必填

            ------------可选----------
            禁用 disabled
            是否显示边框 border
          -->
          <div v-if="item.formType === 'checkbox'">
            <el-checkbox-group v-model="formModel[item.bind]" :disabled='item.disabled'>
              <el-checkbox
              v-for="(check, index) in item.options"
              :key="index"
              :border='item.border'
              :label="item.valueKey ? check[item.valueKey] : check.value"
              :disabled="check.disabled">{{item.labelKey ? check[item.labelKey] : check.label}}</el-checkbox>
            </el-checkbox-group>
          </div>

          <!--
            开关
            -------------必填--------
            formType：switch --必填
            bind: 绑定值   --必填
            options: 数据源  --必填

            ------------可选----------
            禁用 disabled
            打开时的值 activeValue
            关闭时的值 inactiveValue
            打开时的文字描述 activeText
            关闭时的文字描述 inactiveText
          -->
          <div v-if="item.formType === 'switch'">
            <el-switch
              v-model="formModel[item.bind]"
              :disabled="item.disabled"
              :active-value="item.activeValue || true"
              :inactive-value="item.inactiveValue || false"
              :active-text="item.activeText"
              :inactive-text="item.inactiveText">
            </el-switch>
          </div>

          <!--
            select 分页
            -------------必填--------
            formType：switch --必填
            bind: 绑定值   --必填
            options: 数据源  --必填
          -->
          <div v-if="item.formType === 'selectLazy'">
            <q-select-lazy :form="formModel"
                           :model="item.bind"
                           :placeholder="item.placeholder || $t('message.pleaseChoose')"
                           :option="item.option || []"
                           :allData="item.allData || []"
                           :labelKey="item.labelKey"
                           :valueKey="item.valueKey"
                           :searchKey='item.searchKey'
                           :getData="item.getData"
                           :disabled='item.disabled'
                           @changeData='(parmas)=>{return changeData(parmas, item)}'></q-select-lazy>
          </div>

          <!--
            计数器
            -------------必填--------
            formType：inputNumber --必填
            bind: 绑定值   --必填
            options: 数据源  --必填

            ------------可选----------
            禁用 disabled
            设置计数器允许的最小值 min
            设置计数器允许的最大值 max
            计数器步长 step
            是否只能输入step的倍数 stepStrictly
          -->
          <div v-if="item.formType === 'inputNumber'">
            <el-input-number v-model="formModel[item.bind]"
                             :disabled="item.disabled"
                             :min="item.min"
                             :max="item.max"
                             :step="item.step"
                             :step-strictly="item.stepStrictly">
            </el-input-number>
          </div>
          <!-- 自定义render函数 -->
          <render-view v-if="item.render" :render='item.render' :index="index" :item='item'></render-view>
        </el-form-item>
      </el-form>
      <slot name="content-bottom"></slot>
      <span slot="footer" v-if="footer" class="dialog-footer">
         <el-button v-if="option.reset" @click="resetForm(option.formRef || 'ruleForm')">{{option.resetText || $t('message.reset')}}</el-button>
        <el-button @click="close" v-if="option.cancelBtn && String(option.cancelBtn) === 'false' ? false : true">{{option.cancelText || $t('message.cancel')}}</el-button>
        <el-button type="primary" v-if="option.confirmBtn && String(option.confirmBtn) === 'false' ? false : true" @click="saveDialog(option.formRef || 'ruleForm')">{{option.confirmText || $t('message.confirm')}}</el-button>
      </span>

    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'QFormDialog',
  props: {
    footer: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    loading: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    changeStyle: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    // 表单列表
    formList: {
      type: Array,
      default: () => {
        return []
      }
    },
    // 表单绑定的值
    formModel: {
      type: Object,
      default: () => {
        return {}
      }
    },
    // 表单校验
    rules: {
      type: Object,
      default: () => {
        return {}
      }
    },
    // 弹框的一些设置，包括表单通用设置
    option: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  computed: {
    // 获取显示的表单
    formArr() {
      if (this.formList && this.formList.length) {
        const arr = this.formList.filter(item => {
          return !item.hidden
        })
        return arr
      } else {
        return []
      }
      // console.log(arr)
    }
  },
  data() {
    return {
      fullscreen: false
    }
  },
  methods: {
    /**
     * select 分页 改变
    */
    changeData(params, item) {
    //  console.log('changeData')
      this.$emit('changeData', params, item)
    },
    /**
     * 分页 select
    */
    getData(parmas, item) {
      this.$emit('getData', parmas, item)
    },
    /**
     * 重置弹框
    */
    resetForm(name) {
      this.$refs[name].resetFields()
      if(this.formArr && this.formArr.length) {
        this.formArr.forEach(item => {
          if(item.formType === 'text') {
            this.formModel[item.bind] = ''
          }
        })
      }
      this.$emit('reset')
    },
    /**
     * 弹窗保存
    */
    saveDialog(name) {
      if(this.rules && this.$refs[name]) {
        this.$refs[name].validate((valid) => {
          if (valid) {
            this.$emit('save')
          } else {
            return false
          }
        })
      } else {
        this.$emit('save')
      }
    },
    close() {
      this.$emit('close')
    }
  },
  components: {
    renderView: {
      functional: true,
      props: {
        render: Function,
        index: Number,
        item: {
          type: Object,
          default: null
        }
      },
      render: (h, ctx) => {
        const params = ctx.props.item
        const index = ctx.props.index
        return ctx.props.render(h, params, index)
      }
    }
  }
}
</script>

<style lang='scss' scoped>

  .titleIcon{
    i + i{
      padding-left: 20px;
    }
  }

  .formContent{
    max-height: 500px;
    overflow-y: auto;
    padding-right: 10px;
  }
  /deep/.el-dialog__body{
    padding: 20px;
  }
  .changeStyle{
    /deep/.el-dialog__footer{
      padding: 14px 20px;
      // border-top: 10px solid rgba(42, 177, 232, 0.05);
      box-shadow:  0 0 10px rgba(0, 0, 0, 0.07);
      // background: rgba(42, 177, 232, 0.03);
    }
    /deep/.el-dialog__header{
      padding-bottom: 0;
      border: none;
    }
    /deep/.el-dialog{
      padding: 0;
      border-radius: 16px;
      // overflow: hidden;
    }
    .dialog-title{
      border-radius: 16px 16px 0 0;
      position: relative;
      padding: 10px 20px;
      color: $--color-white;
      box-shadow:  0 0 10px rgba(0, 0, 0, 0.3);
      background: linear-gradient(to right, $--color-primary, $--color-success);
      background-image: linear-gradient(90deg,$--color-primary 0%,$--color-success 100%);
      background-image: -webkit-linear-gradient(90deg,$--color-primary 0%,$--color-success 100%);
      background-image: -moz-linear-gradient(90deg,$--color-primary 0%,$--color-success 100%);
      background-image: -o-linear-gradient(90deg,$--color-primary 0%,$--color-success 100%);
      i{
        color: $--color-white;
      }
      .closeIcon{
        position: absolute;
        right: -36px;
        top: -26px;
        font-size: 30px;
        color: $--color-white;
        width: 34px;
        height: 34px;
        line-height: 34px;
        text-align: center;
        padding-left: 0;
        border-radius: 34px;
        font-weight: 900;
        transition: all 0.6s ease-in;
      }
    }
  }
  /deep/.el-select, .el-cascader, .el-date-editor, .el-input{
    width: 100%;
  }

</style>
