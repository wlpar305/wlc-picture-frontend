<template>
  <div class="my-space-container">
    <a-card title="个人信息" style="width: 600px; margin: 20px auto">
      <a-form
        :model="formState"
        name="basic"
        :label-col="{ span: 6 }"
        :wrapper-col="{ span: 16 }"
        autocomplete="off"
        @finish="onSubmit"
      >
        <a-form-item
          label="用户头像"
          name="userAvatar"
        >
          <a-upload
            v-model:file-list="fileList"
            list-type="picture-card"
            :show-upload-list="false"
            :before-upload="beforeUpload"
          >
            <img
              v-if="formState.userAvatar"
              :src="formState.userAvatar"
              alt="avatar"
              style="width: 100%"
            />
            <div v-else>
              <plus-outlined />
              <div class="ant-upload-text">上传头像</div>
            </div>
          </a-upload>
        </a-form-item>

        <a-form-item
          label="用户名"
          name="userName"
          :rules="[{ required: true, message: '请输入用户名!' }]"
        >
          <a-input v-model:value="formState.userName" />
        </a-form-item>

        <a-form-item
          label="简介"
          name="userProfile"
        >
          <a-textarea
            v-model:value="formState.userProfile"
            :rows="4"
          />
        </a-form-item>

        <a-form-item :wrapper-col="{ offset: 6, span: 16 }">
          <a-button type="primary" html-type="submit">保存修改</a-button>
        </a-form-item>
      </a-form>
    </a-card>
  </div>
</template>

<script lang="ts" setup>
import { message } from 'ant-design-vue';
import { PlusOutlined } from '@ant-design/icons-vue';
import { reactive, ref, onMounted } from 'vue';
import { useLoginUserStore } from '@/stores/useLoginUserStore';
import { getUserByIdUsingGet, updateUserUsingPost } from '@/api/userController'; // 请确保这些API存在

const loginUserStore = useLoginUserStore();
const fileList = ref([]);

interface FormState {
  id?: number;
  userName: string;
  userAvatar: string;
  email: string;
  userProfile: string;
}

const formState = reactive<FormState>({
  userName: '',
  userAvatar: '',
  email: '',
  userProfile: ''
});

// 获取用户信息
const fetchUserInfo = async () => {
  try {
    const res = await getUserByIdUsingGet({
      id: loginUserStore.loginUser.id
    });
    if (res.data.code === 0) {
      Object.assign(formState, res.data.data);
    }
  } catch (e) {
    message.error('获取用户信息失败');
  }
};

// 头像上传处理
const beforeUpload = (file: File) => {
  const reader = new FileReader();
  reader.readAsDataURL(file);
  reader.onload = () => {
    formState.userAvatar = reader.result as string;
  };
  return false; // 阻止默认上传
};

// 提交表单
const onSubmit = async () => {
  try {
    const res = await updateUserUsingPost(formState);
    if (res.data.code === 0) {
      message.success('更新成功');
      loginUserStore.setLoginUser({
        ...loginUserStore.loginUser,
        ...formState
      });
    }
  } catch (e) {
    message.error('更新失败');
  }
};

onMounted(() => {
  fetchUserInfo();
});
</script>

<style scoped>
.my-space-container {
  padding: 20px;
  min-height: calc(100vh - 64px);
}
</style>
