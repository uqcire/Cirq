<template>
  <div class="container mx-auto p-4">
    <div class="flex justify-between items-center mb-6">
      <h1 class="text-2xl font-bold">联系人管理</h1>
      <button class="btn btn-primary" @click="handleAdd">
        添加联系人
      </button>
    </div>

    <!-- 联系人列表 -->
    <ContactList @edit="handleEdit" />

    <!-- 联系人表单弹窗 -->
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
</template>

<script setup>
import ContactForm from '@/components/contacts/ContactForm.vue'
import ContactList from '@/components/contacts/ContactList.vue'
import { useContactsStore } from '@/store/contacts'
import { ref } from 'vue'

const contactsStore = useContactsStore()
const showForm = ref(false)
const editingContact = ref(null)

// 处理添加
function handleAdd() {
  editingContact.value = {
    firstName: '',
    lastName: '',
    company: '',
    phone: '',
    email: '',
    address: '',
    birthday: '',
    socialProfiles: {
      twitter: '',
      facebook: '',
      instagram: '',
      wechat: '',
      tiktok: ''
    },
    tags: [],
    customFields: {},
    notes: ''
  }
  showForm.value = true
}

// 处理编辑
function handleEdit(contact) {
  editingContact.value = {
    id: contact.id,
    firstName: contact.first_name,
    lastName: contact.last_name,
    company: contact.company,
    phone: contact.phone,
    email: contact.email,
    address: contact.address,
    birthday: contact.birthday,
    socialProfiles: contact.social_profiles,
    tags: contact.tags,
    customFields: contact.custom_fields,
    notes: contact.notes
  }
  showForm.value = true
}

// 处理表单提交
async function handleSubmit(formData) {
  // 修复 birthday 字段为空时传 null
  if (formData.birthday === '') {
    formData.birthday = null
  }
  try {
    if (editingContact.value?.id) {
      await contactsStore.updateContact(editingContact.value.id, formData)
    } else {
      await contactsStore.createContact(formData)
    }
    closeForm()
  } catch (e) {
    console.error('保存联系人失败:', e)
  }
}

// 关闭表单
function closeForm() {
  showForm.value = false
  editingContact.value = null
}
</script>
