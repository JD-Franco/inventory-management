<template>
  <div class="app">
    <aside class="sidebar" :class="{ collapsed: sidebarCollapsed }">
      <div class="sidebar-top">
        <button class="sidebar-toggle" @click="toggleSidebar" :title="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'" aria-label="Toggle sidebar">
          <svg v-if="!sidebarCollapsed" width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
            <path d="M3 4h12M3 9h12M3 14h12" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <svg v-else width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
            <path d="M3 4h12M3 9h8M3 14h10" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
        </button>
        <div class="logo">
          <h1>{{ t('nav.companyName') }}</h1>
          <span class="subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <nav class="sidebar-nav">
          <router-link to="/" :class="{ active: $route.path === '/' }" :title="sidebarCollapsed ? t('nav.overview') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <rect x="2" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="10" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="2" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="10" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            <span class="nav-label">{{ t('nav.overview') }}</span>
          </router-link>
          <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }" :title="sidebarCollapsed ? t('nav.inventory') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M9 2L16 5.5V12.5L9 16L2 12.5V5.5L9 2Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
              <path d="M9 2V16M2 5.5L9 9L16 5.5" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            <span class="nav-label">{{ t('nav.inventory') }}</span>
          </router-link>
          <router-link to="/orders" :class="{ active: $route.path === '/orders' }" :title="sidebarCollapsed ? t('nav.orders') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2.5 4H15.5L13.5 12H4.5L2.5 4Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
              <circle cx="6" cy="15" r="1.25" stroke="currentColor" stroke-width="1.5"/>
              <circle cx="12" cy="15" r="1.25" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            <span class="nav-label">{{ t('nav.orders') }}</span>
          </router-link>
          <router-link to="/spending" :class="{ active: $route.path === '/spending' }" :title="sidebarCollapsed ? t('nav.finance') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2 13L6 9L9 12L13 6.5L16 8.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M2 16H16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
            <span class="nav-label">{{ t('nav.finance') }}</span>
          </router-link>
          <router-link to="/demand" :class="{ active: $route.path === '/demand' }" :title="sidebarCollapsed ? t('nav.demandForecast') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2 14L6 8L9 11L13 5L16 7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M14 5H16V7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <span class="nav-label">{{ t('nav.demandForecast') }}</span>
          </router-link>
          <router-link to="/reports" :class="{ active: $route.path === '/reports' }" :title="sidebarCollapsed ? t('nav.reports') : ''">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <rect x="2" y="2" width="14" height="14" rx="2" stroke="currentColor" stroke-width="1.5"/>
              <path d="M5 7H13M5 10H10M5 13H8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
            <span class="nav-label">{{ t('nav.reports') }}</span>
          </router-link>
        </nav>
      </div>
      <div class="sidebar-bottom" v-show="!sidebarCollapsed">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="main-area" :class="{ 'sidebar-collapsed': sidebarCollapsed }">
      <div class="topbar">
        <FilterBar />
      </div>
      <main class="page-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)

    // Sidebar collapse state — auto-collapse on narrow viewports
    const sidebarCollapsed = ref(false)

    const toggleSidebar = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value
    }
    const apiTasks = ref([])

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(() => {
      loadTasks()
      // Auto-collapse on screens narrower than 1024px
      if (window.innerWidth < 1024) {
        sidebarCollapsed.value = true
      }
      window.addEventListener('resize', () => {
        if (window.innerWidth < 1024) {
          sidebarCollapsed.value = true
        }
      })
    })

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      sidebarCollapsed,
      toggleSidebar
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: #0f172a;
  color: #e2e8f0;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* ── App shell ── */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}

/* ── Sidebar ── */
.sidebar {
  width: 240px;
  min-width: 240px;
  background: #0f172a;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  z-index: 100;
  overflow-y: auto;
  transition: width 0.2s ease;
}

.sidebar.collapsed {
  width: 60px;
  min-width: 60px;
}

.sidebar.collapsed .logo {
  padding: 0 0 1rem;
  align-items: center;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  margin-bottom: 0.75rem;
}

.sidebar.collapsed .logo h1,
.sidebar.collapsed .logo .subtitle {
  display: none;
}

.sidebar.collapsed .sidebar-nav {
  padding: 0 0.5rem;
}

.sidebar.collapsed .sidebar-nav a {
  justify-content: center;
  padding: 0.75rem;
}

/* Hide nav label text when collapsed */
.sidebar.collapsed .nav-label {
  display: none;
}

.sidebar-top {
  display: flex;
  flex-direction: column;
  padding: 1.5rem 0 1rem;
}

.sidebar-bottom {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: 1rem 1rem 1.25rem;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
}

/* Logo — stacked vertically in the narrow sidebar */
.logo {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  padding: 0 1.25rem 1.25rem;
  margin-bottom: 0.75rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
}

.logo h1 {
  font-size: 1.125rem;
  font-weight: 700;
  color: #f8fafc;
  letter-spacing: -0.025em;
}

.subtitle {
  font-size: 0.75rem;
  color: #64748b;
  font-weight: 400;
}

/* Vertical nav links with icons */
.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  padding: 0 0.75rem;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  color: #94a3b8;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.875rem;
  border-radius: 6px;
  transition: all 0.15s ease;
  white-space: nowrap;
}

.sidebar-nav a svg {
  flex-shrink: 0;
  opacity: 0.7;
  transition: opacity 0.15s ease;
}

.sidebar-nav a:hover {
  color: #e2e8f0;
  background: rgba(255, 255, 255, 0.06);
}

.sidebar-nav a:hover svg {
  opacity: 1;
}

.sidebar-nav a.active {
  color: #ffffff;
  background: #2563eb;
}

.sidebar-nav a.active svg {
  opacity: 1;
}

/* Sidebar toggle button */
.sidebar-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 0.75rem 1.25rem;
  background: none;
  border: none;
  color: #64748b;
  cursor: pointer;
  transition: color 0.15s ease;
  text-align: left;
}

.sidebar-toggle:hover {
  color: #94a3b8;
}

.sidebar.collapsed .sidebar-toggle {
  justify-content: center;
  padding: 0.75rem;
}

/* Override white button styles for the dark sidebar context */
.sidebar-bottom :deep(.language-button),
.sidebar-bottom :deep(.profile-button) {
  background: rgba(255, 255, 255, 0.06);
  border-color: rgba(255, 255, 255, 0.1);
  color: #e2e8f0;
  width: 100%;
  justify-content: flex-start;
}

.sidebar-bottom :deep(.language-button:hover),
.sidebar-bottom :deep(.profile-button:hover) {
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.15);
}

.sidebar-bottom :deep(.globe-icon),
.sidebar-bottom :deep(.chevron) {
  color: #94a3b8;
}

.sidebar-bottom :deep(.profile-name) {
  color: #e2e8f0;
}

/* Dropdowns open upward and stay within the sidebar width */
.sidebar .sidebar-bottom :deep(.dropdown-menu) {
  top: auto;
  bottom: calc(100% + 0.5rem);
  right: 0;
  left: 0;
  min-width: 0;
  width: 100%;
}

/* ── Main content area ── */
.main-area {
  flex: 1;
  margin-left: 240px; /* offset for the fixed sidebar */
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  transition: margin-left 0.2s ease;
}

.main-area.sidebar-collapsed {
  margin-left: 60px;
}

/* Sticky topbar housing the FilterBar — replaces the old sticky header */
.topbar {
  position: sticky;
  top: 0;
  z-index: 90;
  background: #1e293b;
  border-bottom: 1px solid #334155;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.2);
}

.page-content {
  flex: 1;
  padding: 1.5rem 2rem;
}

.page-header {
  margin-bottom: 1.5rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid #1e293b;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: #f1f5f9;
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: #94a3b8;
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: #1e293b;
  padding: 1.25rem;
  border-radius: 10px;
  border: 1px solid #334155;
  transition: border-color 0.2s ease;
}

.stat-card:hover {
  border-color: #475569;
}

.stat-label {
  color: #94a3b8;
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: #ea580c;
}

.stat-card.success .stat-value {
  color: #059669;
}

.stat-card.danger .stat-value {
  color: #dc2626;
}

.stat-card.info .stat-value {
  color: #2563eb;
}

.card {
  background: #1e293b;
  border-radius: 10px;
  padding: 1.25rem;
  border: 1px solid #334155;
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid #334155;
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.025em;
}

.table-container {
  background: #1e293b;
  border: 1px solid #334155;
  border-radius: 10px;
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #263148;
  border-top: 1px solid #334155;
  border-bottom: 1px solid #334155;
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #94a3b8;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #1e293b;
  color: #cbd5e1;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: rgba(255, 255, 255, 0.03);
}

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: #d1fae5;
  color: #065f46;
}

.badge.warning {
  background: #fed7aa;
  color: #92400e;
}

.badge.danger {
  background: #fecaca;
  color: #991b1b;
}

.badge.info {
  background: #dbeafe;
  color: #1e40af;
}

.badge.increasing {
  background: #d1fae5;
  color: #065f46;
}

.badge.decreasing {
  background: #fecaca;
  color: #991b1b;
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: #fecaca;
  color: #991b1b;
}

.badge.medium {
  background: #fed7aa;
  color: #92400e;
}

.badge.low {
  background: #dbeafe;
  color: #1e40af;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: #94a3b8;
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #f87171;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
