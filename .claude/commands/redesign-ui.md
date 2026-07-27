---
description: Redesign the app layout from a horizontal top-nav to a modern SaaS-style vertical sidebar
---

Redesign the inventory management app UI from a horizontal top-navigation bar into a professional SaaS-style layout with a vertical left sidebar, consistent spacing, and a polished look.

## MANDATORY RULE
All `.vue` file changes MUST be delegated to the `vue-expert` subagent using the Agent tool. Do NOT edit any `.vue` files yourself.

---

## Step 1 — Redesign App.vue (delegate to vue-expert)

Use the Agent tool with `subagent_type: "vue-expert"` to make both the template and CSS changes to `client/src/App.vue`.

### Template change

Replace the entire `<template>` block with the following. The `<script>` block and all imports are completely unchanged — do not touch them.

```vue
<template>
  <div class="app">
    <aside class="sidebar">
      <div class="sidebar-top">
        <div class="logo">
          <h1>{{ t('nav.companyName') }}</h1>
          <span class="subtitle">{{ t('nav.subtitle') }}</span>
        </div>
        <nav class="sidebar-nav">
          <router-link to="/" :class="{ active: $route.path === '/' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <rect x="2" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="10" y="2" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="2" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
              <rect x="10" y="10" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            {{ t('nav.overview') }}
          </router-link>
          <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M9 2L16 5.5V12.5L9 16L2 12.5V5.5L9 2Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
              <path d="M9 2V16M2 5.5L9 9L16 5.5" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            {{ t('nav.inventory') }}
          </router-link>
          <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2.5 4H15.5L13.5 12H4.5L2.5 4Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
              <circle cx="6" cy="15" r="1.25" stroke="currentColor" stroke-width="1.5"/>
              <circle cx="12" cy="15" r="1.25" stroke="currentColor" stroke-width="1.5"/>
            </svg>
            {{ t('nav.orders') }}
          </router-link>
          <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2 13L6 9L9 12L13 6.5L16 8.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M2 16H16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
            {{ t('nav.finance') }}
          </router-link>
          <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <path d="M2 14L6 8L9 11L13 5L16 7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
              <path d="M14 5H16V7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            {{ t('nav.demandForecast') }}
          </router-link>
          <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
            <svg width="18" height="18" viewBox="0 0 18 18" fill="none" aria-hidden="true">
              <rect x="2" y="2" width="14" height="14" rx="2" stroke="currentColor" stroke-width="1.5"/>
              <path d="M5 7H13M5 10H10M5 13H8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
            Reports
          </router-link>
        </nav>
      </div>
      <div class="sidebar-bottom">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="main-area">
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
```

### CSS change

In the `<style>` block (not scoped), replace all layout-related rules — everything from `.app` through `.main-content` — with the rules below. Keep everything from `.page-header` onward exactly as-is.

Rules to remove: `.app`, `.top-nav`, `.nav-container`, `.nav-container > .nav-tabs`, `.nav-container > .language-switcher`, `.logo`, `.logo h1`, `.subtitle`, `.nav-tabs`, `.nav-tabs a`, `.nav-tabs a:hover`, `.nav-tabs a.active`, `.nav-tabs a.active::after`, `.main-content`

Replace them with:

```css
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

/* Logo area */
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

/* Sidebar nav links */
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

/* Override white button styles for dark sidebar context */
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

/* Dropdowns open upward from the sidebar bottom */
.sidebar-bottom :deep(.dropdown-menu) {
  top: auto;
  bottom: calc(100% + 0.5rem);
  right: 0;
  left: 0;
  min-width: 0;
  width: 100%;
}

/* ── Main area ── */
.main-area {
  flex: 1;
  margin-left: 240px;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* Sticky topbar — replaces the old sticky header */
.topbar {
  position: sticky;
  top: 0;
  z-index: 90;
  background: #ffffff;
  border-bottom: 1px solid #e2e8f0;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.05);
}

.page-content {
  flex: 1;
  padding: 1.5rem 2rem;
}
```

---

## Step 2 — Fix FilterBar.vue sticky CSS (delegate to vue-expert)

Use the Agent tool with `subagent_type: "vue-expert"` to make the following CSS-only change to `client/src/components/FilterBar.vue`.

In the `<style scoped>` block:

1. Update `.filters-bar` — remove `position: sticky`, `top: 70px`, `z-index: 90`, `background: #f8fafc`, and `border-bottom: 1px solid #e2e8f0`. The parent `.topbar` in App.vue now provides the sticky positioning and background. The updated rule should be:

```css
.filters-bar {
  padding: 0.75rem 0;
}
```

2. Update `.filters-container` — remove `max-width: 1600px` and `margin: 0 auto`. The sidebar layout no longer needs centered max-width. The updated rule should be:

```css
.filters-container {
  padding: 0 2rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}
```

Do not change any other rules in FilterBar.vue.

---

## Step 3 — Verify

After both steps complete, confirm:
- `client/src/App.vue` template contains `<aside class="sidebar">` and `<div class="main-area">`
- `client/src/App.vue` CSS contains `.sidebar`, `.sidebar-nav`, `.topbar`, `.page-content`
- `client/src/components/FilterBar.vue` `.filters-bar` no longer has `position: sticky` or `top: 70px`
- The 6 view files in `client/src/views/` were not modified

Report a summary of all changes made.
