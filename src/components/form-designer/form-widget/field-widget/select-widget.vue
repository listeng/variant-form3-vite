<template>
  <form-item-wrapper :designer="designer" :field="field" :rules="rules" :design-state="designState"
    :parent-widget="parentWidget" :parent-list="parentList" :index-of-parent-list="indexOfParentList"
    :sub-form-row-index="subFormRowIndex" :sub-form-col-index="subFormColIndex" :sub-form-row-id="subFormRowId">
    <el-select ref="fieldEditor" v-model="fieldModel" class="full-width-input" :disabled="field.options.disabled"
      :size="widgetSize" :clearable="field.options.clearable" :filterable="field.options.filterable"
      :allow-create="field.options.allowCreate" :default-first-option="allowDefaultFirstOption"
      :automatic-dropdown="field.options.automaticDropdown" :multiple="field.options.multiple"
      :multiple-limit="field.options.multipleLimit"
      :placeholder="field.options.placeholder || i18nt('render.hint.selectPlaceholder')" :remote="field.options.remote"
      :remote-method="remoteMethod" @focus="handleFocusCustomEvent" @blur="handleBlurCustomEvent"
      @change="handleChangeEvent">
      <el-option v-for="item in field.options.optionItems" :key="item.value" :label="item.label" :value="item.value"
        :disabled="item.disabled">
      </el-option>
    </el-select>
  </form-item-wrapper>
</template>

<script>
import FormItemWrapper from './form-item-wrapper'
import emitter from '@/utils/emitter'
import i18n, { translate } from "@/utils/i18n";
import axios from 'axios'
import fieldMixin from "@/components/form-designer/form-widget/field-widget/fieldMixin";

export default {
  name: "select-widget",
  componentName: 'FieldWidget',  //必须固定为FieldWidget，用于接收父级组件的broadcast事件
  mixins: [emitter, fieldMixin, i18n],
  props: {
    field: Object,
    parentWidget: Object,
    parentList: Array,
    indexOfParentList: Number,
    designer: Object,

    designState: {
      type: Boolean,
      default: false
    },

    subFormRowIndex: { /* 子表单组件行索引，从0开始计数 */
      type: Number,
      default: -1
    },
    subFormColIndex: { /* 子表单组件列索引，从0开始计数 */
      type: Number,
      default: -1
    },
    subFormRowId: { /* 子表单组件行Id，唯一id且不可变 */
      type: String,
      default: ''
    },

  },
  components: {
    FormItemWrapper,
  },
  data() {
    return {
      oldFieldValue: null, //field组件change之前的值
      fieldModel: null,
      rules: [],
    }
  },
  computed: {
    allowDefaultFirstOption() {
      return (!!this.field.options.filterable && !!this.field.options.allowCreate)
    },

    remoteMethod() {
      if (!!this.field.options.remote && !!this.field.options.onRemoteQuery) {
        return this.remoteQuery
      } else {
        return undefined
      }
    },

  },
  beforeCreate() {
    /* 这里不能访问方法和属性！！ */
  },

  created() {
    /* 注意：子组件mounted在父组件created之后、父组件mounted之前触发，故子组件mounted需要用到的prop
       需要在父组件created中初始化！！ */
    this.initOptionItems()
    this.initFieldModel()
    this.registerToRefList()
    this.initEventHandler()
    this.buildFieldRules()

    this.handleOnCreated()
  },

  mounted() {
    if (this.field.options.remoteDatasource) {
      this.getRemoteOptions()
    }
    this.handleOnMounted()
  },

  beforeUnmount() {
    this.unregisterFromRefList()
  },

  methods: {
    getToken() {
      // 从LocalStorage获取v1@CacheToken
      const json = localStorage.getItem('v1@CacheToken');

      // 检查是否存在这个值
      if (json) {
        try {
          // 解析JSON字符串
          const obj = JSON.parse(json);

          // 返回token
          return obj.token;
        } catch (e) {
          // 如果解析失败，可能因为JSON格式不正确
          console.error("Parsing error:", e);
        }
      } else {
        // 如果在LocalStorage中没有找到v1@CacheToken
        console.error("v1@CacheToken is not found in the localStorage.");
      }

      // 如果没有找到token或者有任何错误发生，返回undefined
      return undefined;
    },

    getRemoteOptions() {
      const token = this.getToken();

      axios.get(this.field.options.remoteUrl, {
        headers: {
          Token: token
        }
      }).then(res => {
        console.log(res.data)

        this.field.options.optionItems.length = 0;
        for (let i = 0; i < res.data.length; i++) {
          this.field.options.optionItems.push({
            label: res.data[i][this.field.options.labelName],
            value: res.data[i][this.field.options.labelValue]
          })
        }

      }).catch(error => {
      })
    },
  }
}
</script>

<style lang="scss" scoped>
@import "../../../../styles/global.scss";
/* form-item-wrapper已引入，还需要重复引入吗？ */

.full-width-input {
  width: 100% !important;
}
</style>
