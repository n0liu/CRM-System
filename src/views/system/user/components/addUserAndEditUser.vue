<template>
  <el-dialog
    v-model="dialogVisible"
    :title="title"
    width="550"
    :before-close="handleClose"
  >
    <el-row :gutter="20" class="flex-col">
      <el-form
        ref="ruleFormRef"
        :model="ruleForm"
        :rules="rules"
        label-width="80px"
        class="box-border relative flex flex-wrap"
        :size="formSize"
      >
        <el-col :span="12">
          <el-form-item label="员工编码" prop="id">
            <el-input v-model="ruleForm.id" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="员工姓名" prop="username">
            <el-input v-model="ruleForm.username" />
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="性别" prop="sex">
            <el-radio-group v-model="ruleForm.sex">
              <el-radio label="男" />
              <el-radio label="女" />
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="状态" prop="status">
            <el-radio-group v-model="ruleForm.status">
              <el-radio label="正常" value="1" />
              <el-radio label="禁用" value="0" />
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="所属部门" prop="dept">
            <el-select v-model="ruleForm.dept" placeholder="所属部门">
              <el-option label="开发" value="1" />
              <el-option label="运维" value="2" />
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="角色身份" prop="role">
            <el-select v-model="ruleForm.role" placeholder="角色身份">
              <el-option label="管理员" value="1" />
              <el-option label="测试" value="2" />
            </el-select>
          </el-form-item>
        </el-col>

        <el-col :span="24">
          <el-form-item label="备注信息" prop="desc">
            <el-input v-model="ruleForm.desc" type="textarea" />
          </el-form-item>
        </el-col>
      </el-form>
    </el-row>
    <template #footer>
      <span class="dialog-footer">
        <el-button type="primary" @click="submitForm(ruleFormRef)">
          确定
        </el-button>
        <el-button @click="resetForm(ruleFormRef)">取消</el-button>
      </span>
    </template>
  </el-dialog>
</template>
<script setup lang="ts">
import type { FormInstance, FormRules } from "element-plus";
import { isEmpty } from "lodash-es";
const formSize: Ref = ref("default");
const ruleFormRef = ref<FormInstance>();
const params = {
  username: "",
  dept: "",
  desc: "",
  role: "",
  sex: "男",
  status: "正常",
  id: ""
};
const ruleForm = reactive(params);
const dialogVisible = ref(false);
// TODO: 获取弹窗信息
const emit = defineEmits(["update:modelValue"]);
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  },
  title: {
    type: String,
    default: "新增用户"
  },
  userInfo: {
    type: Object,
    default: () => {}
  }
});
watch(
  () => props.modelValue,
  newVal => {
    dialogVisible.value = newVal;
    !isEmpty(props.userInfo) && newVal && editUserInit(props.userInfo);
  }
);
// TODO: 设置表单验证信息
const rules = reactive<FormRules>({
  id: [{ required: true, message: "请输入员工编号", trigger: "blur" }],
  username: [{ required: true, message: "请输入员工姓名", trigger: "blur" }],
  role: [{ required: true, message: "请选择角色", trigger: "change" }],
  dept: [{ required: true, message: "请选择部门", trigger: "change" }],
  desc: [{ required: true, message: "请填写备注", trigger: "blur" }]
});
// 编辑状态数据初始化
const editUserInit = async (data: object) => {
  // 让 form 创建完成后再赋值, 解决编辑后一直有值不能被初始化的原因
  await nextTick();
  for (const key in ruleForm) {
    ruleForm[key] = data[key];
  }
};
// 提交表单
const submitForm = async (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  await formEl.validate((valid, fields) => {
    if (valid) {
      console.log("submit!");
    } else {
      console.log("error submit!", fields);
    }
  });
};
// 点击左上角关闭
const handleClose = () => {
  dialogVisible.value = false;
  emit("update:modelValue", false);
};
// 重置表单
const resetForm = (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  formEl.resetFields();
  handleClose();
};
</script>
<style lang="scss" scoped></style>
