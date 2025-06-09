<template>
  <div class="flex flex-col md:flex-row gap-8 w-full">
    <!-- 左侧：头像、姓名、公司、社交媒体 -->
    <div class="flex flex-col items-center md:w-1/4 w-full mb-6 md:mb-0">
      <div class="avatar placeholder mb-4">
        <div class="bg-neutral text-neutral-content rounded-full w-24 h-24 flex items-center justify-center text-4xl">
          <span>{{ getInitials(contact) }}</span>
        </div>
      </div>
      <div class="text-2xl font-bold mb-1">{{ contact.first_name }} {{ contact.last_name }}</div>
      <div class="text-gray-500 mb-4 text-center">{{ contact.company }}</div>
      <div class="flex flex-row gap-3 mb-4">
        <a v-if="contact.social_profiles?.linkedin" :href="contact.social_profiles.linkedin" target="_blank"
          class="text-blue-600"><i class="i-lucide-linkedin"></i></a>
        <a v-if="contact.social_profiles?.twitter" :href="contact.social_profiles.twitter" target="_blank"
          class="text-blue-400"><i class="i-lucide-twitter"></i></a>
        <a v-if="contact.social_profiles?.facebook" :href="contact.social_profiles.facebook" target="_blank"
          class="text-blue-700"><i class="i-lucide-facebook"></i></a>
        <a v-if="contact.social_profiles?.instagram" :href="contact.social_profiles.instagram" target="_blank"
          class="text-pink-500"><i class="i-lucide-instagram"></i></a>
        <a v-if="contact.social_profiles?.wechat" class="text-green-500"><i class="i-lucide-message-circle"></i></a>
        <a v-if="contact.social_profiles?.tiktok" :href="contact.social_profiles.tiktok" target="_blank"
          class="text-black"><i class="i-lucide-music"></i></a>
      </div>
    </div>

    <!-- 中间：详细区块 -->
    <div class="flex-1 flex flex-col gap-6">
      <div class="flex items-center gap-2 border-b pb-2">
        <span class="font-semibold">Description</span>
        <span class="text-gray-400 cursor-pointer">Add description</span>
      </div>
      <div class="flex items-center gap-2 border-b pb-2">
        <i class="i-lucide-clock text-lg"></i>
        <span class="font-semibold">Last Interaction</span>
        <span class="text-gray-400 ml-2">No last interaction</span>
      </div>
      <div class="flex items-center gap-2 border-b pb-2">
        <i class="i-lucide-repeat text-lg"></i>
        <span class="font-semibold">Frequency</span>
        <span class="text-gray-400 ml-2">Set keep in touch...</span>
      </div>
      <div class="border-b pb-2">
        <div class="font-semibold mb-1">Experience</div>
        <div class="text-gray-500">暂无数据</div>
      </div>
      <div>
        <div class="font-semibold mb-1">Education</div>
        <div class="text-gray-500">暂无数据</div>
      </div>
    </div>

    <!-- 右侧：详细表单区 -->
    <div class="md:w-1/4 w-full flex flex-col gap-4 p-4 border border-gray-100 bg-transparent rounded-none">
      <div>
        <div class="font-semibold text-sm mb-1">Groups</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.groups || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Tags</div>
        <div class="flex flex-wrap gap-1">
          <span v-for="tag in contact.tags" :key="tag" class="badge badge-outline text-xs">{{ tag }}</span>
          <span v-if="!contact.tags || !contact.tags.length" class="text-gray-400">无</span>
        </div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Email</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.email || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Phone</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.phone || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Address</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.address || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Location</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.location || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Birthday</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.birthday ? formatDate(contact.birthday) : '无'
          }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Website</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.website || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Company</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.company || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Education</div>
        <div class="bg-white rounded px-2 py-1 border text-xs">{{ contact.education || '无' }}</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Custom Fields</div>
        <div v-if="contact.custom_fields && Object.keys(contact.custom_fields).length">
          <div v-for="(value, key) in contact.custom_fields" :key="key" class="text-xs">
            <span class="font-semibold">{{ key }}:</span> {{ value }}
          </div>
        </div>
        <div v-else class="text-gray-400 text-xs">无</div>
      </div>
      <div>
        <div class="font-semibold text-sm mb-1">Social Links</div>
        <div class="flex flex-wrap gap-2">
          <a v-if="contact.social_profiles?.linkedin" :href="contact.social_profiles.linkedin" target="_blank"
            class="text-blue-600"><i class="i-lucide-linkedin"></i></a>
          <a v-if="contact.social_profiles?.twitter" :href="contact.social_profiles.twitter" target="_blank"
            class="text-blue-400"><i class="i-lucide-twitter"></i></a>
          <a v-if="contact.social_profiles?.facebook" :href="contact.social_profiles.facebook" target="_blank"
            class="text-blue-700"><i class="i-lucide-facebook"></i></a>
          <a v-if="contact.social_profiles?.instagram" :href="contact.social_profiles.instagram" target="_blank"
            class="text-pink-500"><i class="i-lucide-instagram"></i></a>
          <a v-if="contact.social_profiles?.wechat" class="text-green-500"><i class="i-lucide-message-circle"></i></a>
          <a v-if="contact.social_profiles?.tiktok" :href="contact.social_profiles.tiktok" target="_blank"
            class="text-black"><i class="i-lucide-music"></i></a>
        </div>
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

<style scoped>
.i-lucide-linkedin::before {
  content: "\f0e1";
  font-family: 'Font Awesome 5 Brands';
}

.i-lucide-twitter::before {
  content: "\f099";
  font-family: 'Font Awesome 5 Brands';
}

.i-lucide-facebook::before {
  content: "\f09a";
  font-family: 'Font Awesome 5 Brands';
}

.i-lucide-instagram::before {
  content: "\f16d";
  font-family: 'Font Awesome 5 Brands';
}

.i-lucide-message-circle::before {
  content: "\f27a";
  font-family: 'Font Awesome 5 Free';
}

.i-lucide-music::before {
  content: "\f8ff";
  font-family: 'Font Awesome 5 Free';
}
</style>
