<template>
  <div class="container mx-auto p-4">
    <div v-if="loading" class="flex justify-center items-center h-64">
      <span class="loading loading-spinner loading-lg"></span>
    </div>

    <div v-else-if="error" class="alert alert-error">
      <span>{{ error }}</span>
    </div>

    <div v-else-if="contact" class="max-w-4xl mx-auto">
      <!-- 返回按钮 -->
      <button class="btn btn-ghost mb-4" @click="$router.push('/contacts')">
        ← 返回列表
      </button>

      <!-- 联系人卡片 -->
      <div class="card bg-base-100 shadow-xl">
        <div class="card-body">
          <!-- 头部信息 -->
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
              <a v-if="contact.social_profiles?.twitter"
                :href="`https://twitter.com/${contact.social_profiles.twitter}`" target="_blank" class="btn btn-sm">
                Twitter
              </a>
              <a v-if="contact.social_profiles?.facebook"
                :href="`https://facebook.com/${contact.social_profiles.facebook}`" target="_blank" class="btn btn-sm">
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
            <button class="btn btn-primary" @click="handleEdit">
              编辑
            </button>
            <button class="btn btn-error" @click="handleDelete">
              删除
            </button>
          </div>
        </div>
      </div>

      <!-- 编辑表单弹窗 -->
      <div v-if="showForm" class="fixed inset-0 bg-black bg-opacity-30 flex items-center justify-center z-50">
        <div class="bg-white rounded-lg shadow-lg p-6 w-full max-w-2xl relative">
          <button class="btn btn-sm btn-circle absolute right-2 top-2" @click="closeForm">
            ✕
          </button>
          <ContactForm :initial-data="editingContact" :loading="contactsStore.loading" @submit="handleSubmit"
            @cancel="closeForm" />
        </div>
      </div>
    </div>

    <div v-else class="text-center p-8">
      <div class="text-gray-500">未找到联系人</div>
      <button class="btn btn-primary mt-4" @click="$router.push('/contacts')">
        返回列表
      </button>
    </div>
  </div>
</template>

<script setup>
import ContactForm from '@/components/contacts/ContactForm.vue'
import { useContactsStore } from '@/store/contacts'
import { computed, onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const contactsStore = useContactsStore()

const loading = ref(true)
const error = ref(null)
const contact = ref(null)
const showForm = ref(false)
const editingContact = ref(null)

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
  const profiles = contact.value?.social_profiles || {}
  return Object.values(profiles).some(value => value)
})

// 检查是否有自定义字段
const hasCustomFields = computed(() => {
  const fields = contact.value?.custom_fields || {}
  return Object.keys(fields).length > 0
})

// 获取联系人详情
async function fetchContact() {
  loading.value = true
  error.value = null
  try {
    const contactId = route.params.id
    const contactData = await contactsStore.getContactById(contactId)
    if (contactData) {
      contact.value = contactData
    }
  } catch (e) {
    error.value = '加载联系人失败'
    console.error(e)
  } finally {
    loading.value = false
  }
}

// 处理编辑
function handleEdit() {
  editingContact.value = {
    id: contact.value.id,
    firstName: contact.value.first_name,
    lastName: contact.value.last_name,
    company: contact.value.company,
    phone: contact.value.phone,
    email: contact.value.email,
    address: contact.value.address,
    birthday: contact.value.birthday,
    socialProfiles: contact.value.social_profiles,
    tags: contact.value.tags,
    customFields: contact.value.custom_fields,
    notes: contact.value.notes
  }
  showForm.value = true
}

// 处理删除
async function handleDelete() {
  if (confirm('确定要删除这个联系人吗？')) {
    try {
      await contactsStore.deleteContact(contact.value.id)
      router.push('/contacts')
    } catch (e) {
      console.error('删除联系人失败:', e)
    }
  }
}

// 处理表单提交
async function handleSubmit(formData) {
  try {
    await contactsStore.updateContact(contact.value.id, formData)
    await fetchContact()
    closeForm()
  } catch (e) {
    console.error('更新联系人失败:', e)
  }
}

// 关闭表单
function closeForm() {
  showForm.value = false
  editingContact.value = null
}

onMounted(fetchContact)
</script>
