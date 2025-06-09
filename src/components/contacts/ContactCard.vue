<template>
  <div class="card bg-base-100 shadow-xl">
    <!-- 头部信息 -->
    <div class="card-body">
      <div class="flex items-center space-x-4 mb-6">
        <div class="avatar placeholder">
          <div class="bg-neutral text-neutral-content rounded-full w-16">
            <span class="text-2xl">{{ getInitials(contact) }}</span>
          </div>
        </div>
        <div>
          <h2 class="card-title text-2xl">
            {{ contact.first_name }} {{ contact.last_name }}
          </h2>
          <p v-if="contact.company" class="text-lg text-gray-600">
            {{ contact.company }}
          </p>
        </div>
      </div>

      <!-- 基本信息 -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <div class="space-y-2">
          <div class="flex items-center">
            <span class="text-gray-500 w-24">电话：</span>
            <span>{{ contact.phone || '未设置' }}</span>
          </div>
          <div class="flex items-center">
            <span class="text-gray-500 w-24">邮箱：</span>
            <span>{{ contact.email || '未设置' }}</span>
          </div>
          <div class="flex items-center">
            <span class="text-gray-500 w-24">地址：</span>
            <span>{{ contact.address || '未设置' }}</span>
          </div>
          <div class="flex items-center">
            <span class="text-gray-500 w-24">生日：</span>
            <span>{{ formatDate(contact.birthday) || '未设置' }}</span>
          </div>
        </div>
      </div>

      <!-- 社交媒体 -->
      <div v-if="hasSocialProfiles" class="mb-6">
        <h3 class="text-lg font-semibold mb-2">社交媒体</h3>
        <div class="flex flex-wrap gap-2">
          <a v-if="contact.social_profiles?.twitter" :href="`https://twitter.com/${contact.social_profiles.twitter}`"
            target="_blank" class="btn btn-sm">
            Twitter
          </a>
          <a v-if="contact.social_profiles?.facebook" :href="`https://facebook.com/${contact.social_profiles.facebook}`"
            target="_blank" class="btn btn-sm">
            Facebook
          </a>
          <a v-if="contact.social_profiles?.instagram"
            :href="`https://instagram.com/${contact.social_profiles.instagram}`" target="_blank" class="btn btn-sm">
            Instagram
          </a>
          <a v-if="contact.social_profiles?.wechat" class="btn btn-sm">
            WeChat: {{ contact.social_profiles.wechat }}
          </a>
          <a v-if="contact.social_profiles?.tiktok" :href="`https://tiktok.com/@${contact.social_profiles.tiktok}`"
            target="_blank" class="btn btn-sm">
            TikTok
          </a>
        </div>
      </div>

      <!-- 标签 -->
      <div v-if="contact.tags?.length" class="mb-6">
        <h3 class="text-lg font-semibold mb-2">标签</h3>
        <div class="flex flex-wrap gap-2">
          <span v-for="tag in contact.tags" :key="tag" class="badge badge-lg">
            {{ tag }}
          </span>
        </div>
      </div>

      <!-- 自定义字段 -->
      <div v-if="hasCustomFields" class="mb-6">
        <h3 class="text-lg font-semibold mb-2">自定义信息</h3>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div v-for="(value, key) in contact.custom_fields" :key="key" class="flex items-center">
            <span class="text-gray-500 w-24">{{ key }}：</span>
            <span>{{ value }}</span>
          </div>
        </div>
      </div>

      <!-- 备注 -->
      <div v-if="contact.notes" class="mb-6">
        <h3 class="text-lg font-semibold mb-2">备注</h3>
        <p class="text-gray-600">{{ contact.notes }}</p>
      </div>

      <!-- 操作按钮 -->
      <div class="card-actions justify-end mt-4">
        <button class="btn btn-primary" @click="$emit('edit')">
          编辑
        </button>
        <button class="btn btn-error" @click="handleDelete">
          删除
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  contact: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['edit', 'delete'])

// 获取姓名首字母
function getInitials(contact) {
  const first = contact.first_name?.[0] || ''
  const last = contact.last_name?.[0] || ''
  return (first + last).toUpperCase()
}

// 格式化日期
function formatDate(date) {
  if (!date) return ''
  return new Date(date).toLocaleDateString()
}

// 检查是否有社交媒体信息
const hasSocialProfiles = computed(() => {
  const profiles = props.contact.social_profiles || {}
  return Object.values(profiles).some(value => value)
})

// 检查是否有自定义字段
const hasCustomFields = computed(() => {
  const fields = props.contact.custom_fields || {}
  return Object.keys(fields).length > 0
})

// 处理删除
function handleDelete() {
  if (confirm('确定要删除这个联系人吗？')) {
    emit('delete')
  }
}
</script>
