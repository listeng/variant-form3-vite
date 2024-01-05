<template>
  <el-form-item label-width="0">
    <el-divider class="custom-divider-margin-top">{{i18nt('designer.setting.optionsSetting')}}</el-divider>
    
  </el-form-item>
  <el-form-item :label="i18nt('designer.setting.remoteDatasource')">
    <el-switch v-model="optionModel.remoteDatasource"></el-switch>
  </el-form-item>
  <el-form-item :label="i18nt('designer.setting.remoteUrl')" v-if="optionModel.remoteDatasource">
    <el-input v-model="optionModel.remoteUrl"></el-input>
  </el-form-item>
  <el-form-item :label="i18nt('designer.setting.labelName')" v-if="optionModel.remoteDatasource">
    <el-input v-model="optionModel.labelName"></el-input>
  </el-form-item>
  <el-form-item :label="i18nt('designer.setting.labelValue')" v-if="optionModel.remoteDatasource">
    <el-input v-model="optionModel.labelValue"></el-input>
  </el-form-item>
  <el-form-item label-width="0" v-if="!optionModel.remoteDatasource">
    <option-items-setting :designer="designer" :selected-widget="selectedWidget"></option-items-setting>
  </el-form-item>
</template>

<script>
  import i18n from "@/utils/i18n"
  import OptionItemsSetting from "@/components/form-designer/setting-panel/option-items-setting"

  export default {
    name: "optionItems-editor",
    mixins: [i18n],
    props: {
      designer: Object,
      selectedWidget: Object,
      optionModel: Object,
    },
    components: {
      OptionItemsSetting,
    }
  }
</script>

<style scoped>

</style>
