<template>
    <EmployeeEditor
      :defaultData="{
        first_name: null,
        last_name: null,
        work_mail: null,
        personal_mail: null,
        gender: 'female',
        status: 1
      }"
    />
    <div class="flex flex-col justify-center pt-10 px-5 gap-2">
        <el-form
            ref="formRef"
            label-width="auto"
            label-position="right"
            label-suffix=":"
            :model="formData"
            :rules="rules"
            class="self-center"
        >
            <el-form-item :label="$t('first_name')" prop="first_name">
                <el-input
                    v-model="formData.first_name"
                    :placeholder="$t('default_place_holder')"
                />
            </el-form-item>
            <el-form-item :label="$t('last_name')" prop="last_name">
                <el-input
                    v-model="formData.last_name"
                    :placeholder="$t('default_place_holder')"
                />
            </el-form-item>
            <el-form-item :label="$t('work_mail')" prop="work_mail">
                <el-input
                    v-model="formData.work_mail"
                    :placeholder="$t('default_place_holder')"
                />
            </el-form-item>
            <el-form-item :label="$t('personal_mail')" prop="personal_mail">
                <el-input
                    v-model="formData.personal_mail"
                    :placeholder="$t('default_place_holder')"
                />
            </el-form-item>
            <el-form-item :label="$t('gender')" prop="gender">
                <el-select v-model="formData.gender" :placeholder="$t('default_place_holder')">
                    <el-option label="Male" value="male" />
                    <el-option label="Female" value="female" />
                </el-select>
            </el-form-item>
            <el-form-item :label="$t('status')" prop="status">
                <el-switch
                    v-model="formData.status"
                    :active-value="1"
                    :inactive-value="0"
                />
            </el-form-item>
        </el-form>
        <el-button class="self-center mb-6" @click="submit()">Add Employee</el-button>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import EmployeeService from '@/services/hrm/employees';
import { getErrorMessage } from '@/utils/error';
import { useI18n } from 'vue-i18n';

definePageMeta({
  layout: 'hrm'
})

const { t } = useI18n();
const router = useRouter();
const formRef = ref(null);
const formData = ref({
  first_name: null,
  last_name: null,
  work_mail: null,
  personal_mail: null,
  gender: 'female',
  status: 1
});
const error = ref(null);

const rules = {
  first_name: [
    { required: true, message: t('validate_error_required'), trigger: 'blur' },
    { min: 1, max: 20, message: t('validate_error_min_max', { min: 1, max: 20 }), trigger: 'blur' },
  ],
  last_name: [
    { required: true, message: t('validate_error_required'), trigger: 'blur' },
    { min: 1, max: 30, message: t('validate_error_min_max', { min: 1, max: 30 }), trigger: 'blur' },
  ],
  work_mail: [
    { required: true, message: t('validate_error_required'), trigger: 'blur' },
    { type: 'email', message: t('validate_error_email'), trigger: 'blur' },
  ],
  personal_mail: [
    { type: 'email', message: t('validate_error_email'), trigger: 'blur' },
  ],
};

const submit = () => {
  if (!formRef.value) {
    return;
  }

  formRef.value.validate(async (valid, fields) => {
    if (!valid) {
      return;
    }
    error.value = null;
    EmployeeService.create(formData.value)
      .then((response) => {
        router.push('/employees');
      })
      .catch((e) => {
        error.value = getErrorMessage(e, t('an_error_occurred'));
      });
  });
};
</script>