<template>
  <div class="url-picture-upload">
    <a-input-group compact>
      <a-input
        v-model:value="fileUrl"
        style="width: calc(100% - 120px)"
        placeholder="请输入图片地址"
      />
      <a-button type="primary" style="width: 120px" :loading="loading" @click="handleUpload"
        >提交
      </a-button>
    </a-input-group>
    <div class="image-wrapper">
      <img v-if="picture?.url" :src="picture?.url" alt="avatar" />
    </div>
  </div>
</template>
<script lang="ts" setup>
import { ref } from 'vue'
import { PlusOutlined, LoadingOutlined } from '@ant-design/icons-vue'
import { message } from 'ant-design-vue'
import type { UploadChangeParam, UploadProps } from 'ant-design-vue'
import { uploadPictureByUrlUsingPost, uploadPictureUsingPost } from '@/api/tupianshangchuan.ts'

// 定义 props 结构和类型 用来约束这个 Vue 组件接收到的 props 数据结构
interface Props {
  picture?: API.PictureVO // 可选参数，类型是 API.PictureVO
  onSuccess?: (newPicture: API.PictureVO) => void // 可选方法，参数是 API.PictureVO，返回值是 void
}

const props = defineProps<Props>()
const fileUrl = ref<string>()
const loading = ref<boolean>(false)
/**
 * 上传图片
 * @param file
 */
const handleUpload = async () => {
  loading.value = true

  try {
    const params: API.PictureUploadRequest = { fileUrl: fileUrl.value }
    if (props.picture) {
      params.id = props.picture.id
    }
    const res = await uploadPictureByUrlUsingPost(params)
    if (res.data.code === 0 && res.data.data) {
      message.success('图片上传成功')
      props.onSuccess?.(res.data.data)
    } else {
      message.error('图片上传失败，' + res.data.message)
    }
  } catch (error) {
    console.error('图片上传失败，' + res.data.message)
    message.error('图片上传失败，' + error.messsage)
  }
  loading.value = false
}
</script>
<style scoped>
.url-picture-upload {
  margin-bottom: 16px;
}

.url-picture-upload img {
  max-width: 100%;
  max-height: 480px;
  margin: 0 auto;
}

.url-picture-upload .image-wrapper {
  text-align: center;
  margin-top: 16px;
}
</style>
