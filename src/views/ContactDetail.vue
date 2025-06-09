<template>
  <div class="container mx-auto p-4">
    <div v-if="loading" class="flex justify-center items-center h-64">
      <span class="loading loading-spinner loading-lg"></span>
    </div>

    <div v-else-if="error" class="alert alert-error">
      <span>{{ error }}</span>
    </div>

    <div v-else-if="contact" class="max-w-4xl mx-auto">

      <!-- 使用ContactCard组件展示联系人详细信息 -->
      <ContactCard :contact="contact" @edit="handleEdit" @delete="handleDelete" />

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
import ContactCard from '@/components/contacts/ContactCard.vue'
import ContactForm from '@/components/contacts/ContactForm.vue'
import { useContactsStore } from '@/store/contacts'
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const contactsStore = useContactsStore()

const loading = ref(true)
const error = ref(null)
const contact = ref(null)
const showForm = ref(false)
const editingContact = ref(null)

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
