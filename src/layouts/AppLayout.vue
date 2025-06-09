<script setup>
import { ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
const route = useRoute()
const router = useRouter()
const search = ref(route.query.search || '')

function handleInputSearch() {
  if (search.value.trim()) {
    router.replace({ path: '/contacts', query: { search: search.value.trim() } })
  } else {
    router.replace('/contacts')
  }
}
</script>

<template>
  <div class="grid grid-cols-[220px_1fr] min-h-screen">
    <!-- Sidebar -->
    <aside class="bg-base-200 border-r border-base-300 flex flex-col py-6 px-4 w-[220px]">
      <div class="mb-8 flex items-center justify-center">
        <div class="text-2xl font-bold tracking-widest text-primary">Cirq</div>
      </div>
      <nav class="flex flex-col gap-2">
        <router-link to="/" class="flex items-center gap-3 px-3 py-2 rounded-lg hover:bg-base-300 transition"
          :class="{ 'bg-base-300 font-bold text-primary': route.path === '/' }">
          <span class="i-lucide-sun w-5 h-5"></span>
          Today
        </router-link>
        <router-link to="/reminders" class="flex items-center gap-3 px-3 py-2 rounded-lg hover:bg-base-300 transition"
          :class="{ 'bg-base-300 font-bold text-primary': route.path === '/reminders' }">
          <span class="i-lucide-bell w-5 h-5"></span>
          Reminders
        </router-link>
        <router-link to="/contacts" class="flex items-center gap-3 px-3 py-2 rounded-lg hover:bg-base-300 transition"
          :class="{ 'bg-base-300 font-bold text-primary': route.path.startsWith('/contacts') }">
          <span class="i-lucide-users w-5 h-5"></span>
          Contacts
        </router-link>
        <router-link to="/timeline" class="flex items-center gap-3 px-3 py-2 rounded-lg hover:bg-base-300 transition"
          :class="{ 'bg-base-300 font-bold text-primary': route.path === '/timeline' }">
          <span class="i-lucide-clock w-5 h-5"></span>
          Timeline
        </router-link>
      </nav>
    </aside>
    <div class="relative flex flex-col min-h-screen">
      <!-- 固定顶部navbar -->
      <header class="sticky top-0 z-20 bg-base-100 border-b border-base-200 flex items-center px-8 py-2 gap-4">
        <!-- 搜索框 -->
        <div class="flex-1 max-w-xl">
          <input v-model="search" type="text" placeholder="Search contacts..." class="input input-bordered w-full"
            @input="handleInputSearch" />
        </div>
        <!-- 添加按钮dropdown -->
        <div class="dropdown dropdown-end">
          <label tabindex="0" class="btn btn-circle btn-primary text-xl flex items-center justify-center">
            <span class="i-lucide-plus"></span>
          </label>
          <ul tabindex="0" class="dropdown-content menu p-2 shadow bg-base-100 rounded-box w-56 mt-2">
            <li><a @click.prevent="router.push('/contacts/add')">添加 Contact</a></li>
            <li><a @click.prevent="router.push('/groups/add')">添加 Group</a></li>
            <li><a @click.prevent="router.push('/notes/add')">添加 Note</a></li>
            <li><a @click.prevent="router.push('/reminders/add')">添加 Reminder</a></li>
            <li><a @click.prevent="router.push('/import')">Import Contacts</a></li>
          </ul>
        </div>
        <!-- 右侧下拉菜单 -->
        <div class="dropdown dropdown-end">
          <label tabindex="0" class="btn btn-circle btn-ghost text-xl flex items-center justify-center">
            <span class="i-lucide-chevron-down"></span>
          </label>
          <ul tabindex="0" class="dropdown-content menu p-2 shadow bg-base-100 rounded-box w-44 mt-2">
            <li><a @click.prevent="router.push('/settings')">Settings</a></li>
            <li><a @click.prevent="router.push('/import')">Import Contacts</a></li>
          </ul>
        </div>
      </header>
      <main class="flex-1 px-8 py-6">
        <router-view />
      </main>
    </div>
  </div>
</template>

<style scoped>
.i-lucide-sun::before {
  content: "☀️";
}

.i-lucide-bell::before {
  content: "🔔";
}

.i-lucide-users::before {
  content: "👥";
}

.i-lucide-clock::before {
  content: "🕒";
}

.i-lucide-plus::before {
  content: "+";
}

.i-lucide-chevron-down::before {
  content: "▼";
}
</style>
