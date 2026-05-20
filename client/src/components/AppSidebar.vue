<template>
  <aside :class="['sidebar', { collapsed }]">

    <!-- Brand -->
    <div class="sidebar-brand">
      <div class="brand-content">
        <span class="brand-initial">F</span>
        <div class="brand-text">
          <span class="brand-name">FactoryOps</span>
          <span class="brand-sub">Inventory Management</span>
        </div>
      </div>
      <button class="toggle-btn" @click="toggle" :title="collapsed ? 'Expand sidebar' : 'Collapse sidebar'">
        <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
          <polyline v-if="!collapsed" points="13,4 7,10 13,16" />
          <polyline v-else points="7,4 13,10 7,16" />
        </svg>
      </button>
    </div>

    <!-- Nav -->
    <nav class="sidebar-nav">
      <router-link
        v-for="item in navItems"
        :key="item.path"
        :to="item.path"
        :class="['nav-item', { active: isActive(item) }]"
        :data-label="item.label"
        :title="collapsed ? item.label : ''"
      >
        <span class="nav-icon">
          <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path v-for="(d, i) in item.iconPaths" :key="i" :d="d" />
          </svg>
        </span>
        <span class="nav-label">{{ item.label }}</span>
      </router-link>
    </nav>

    <!-- Footer -->
    <div class="sidebar-footer">
      <div class="footer-items" :class="{ 'footer-collapsed': collapsed }">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="$emit('show-profile-details')"
          @show-tasks="$emit('show-tasks')"
        />
      </div>
    </div>

  </aside>
</template>

<script>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useRoute } from 'vue-router'
import { useI18n } from '../composables/useI18n'
import ProfileMenu from './ProfileMenu.vue'
import LanguageSwitcher from './LanguageSwitcher.vue'

export default {
  name: 'AppSidebar',
  components: { ProfileMenu, LanguageSwitcher },
  emits: ['show-profile-details', 'show-tasks'],
  setup() {
    const route = useRoute()
    const { t } = useI18n()

    const collapsed = ref(false)
    const manuallyCollapsed = ref(false)

    const toggle = () => {
      manuallyCollapsed.value = !collapsed.value
      collapsed.value = !collapsed.value
    }

    const handleResize = () => {
      if (window.innerWidth < 1024) {
        collapsed.value = true
      } else if (!manuallyCollapsed.value) {
        collapsed.value = false
      }
    }

    onMounted(() => {
      handleResize()
      window.addEventListener('resize', handleResize)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', handleResize)
    })

    const navItems = computed(() => [
      {
        path: '/',
        label: t('nav.overview'),
        iconPaths: ['M3 9l9-7 9 7v11a2 2 0 01-2 2H5a2 2 0 01-2-2z', 'M9 22V12h6v10'],
      },
      {
        path: '/inventory',
        label: t('nav.inventory'),
        iconPaths: ['M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4'],
      },
      {
        path: '/orders',
        label: t('nav.orders'),
        iconPaths: [
          'M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2',
          'M9 13l2 2 4-4',
        ],
      },
      {
        path: '/spending',
        label: t('nav.finance'),
        iconPaths: ['M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z'],
      },
      {
        path: '/demand',
        label: t('nav.demandForecast'),
        iconPaths: ['M13 7h8m0 0v8m0-8l-8 8-4-4-6 6'],
      },
      {
        path: '/reports',
        label: 'Reports',
        iconPaths: ['M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z'],
      },
    ])

    const isActive = (item) => {
      if (item.path === '/') return route.path === '/'
      return route.path.startsWith(item.path)
    }

    return { collapsed, toggle, navItems, isActive }
  }
}
</script>

<style scoped>
.sidebar {
  width: 240px;
  flex-shrink: 0;
  height: 100vh;
  background: #0f172a;
  border-right: 1px solid #1e293b;
  display: flex;
  flex-direction: column;
  overflow: visible; /* allow tooltip to escape */
  transition: width 0.25s ease;
  position: relative;
  z-index: 10;
}

.sidebar.collapsed {
  width: 64px;
}

/* Brand */
.sidebar-brand {
  padding: 1.25rem 0.75rem 1.25rem 1.25rem;
  border-bottom: 1px solid #1e293b;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  min-height: 64px;
  overflow: hidden;
}

.brand-content {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  min-width: 0;
}

.brand-initial {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  background: #2563eb;
  color: #fff;
  font-size: 0.9rem;
  font-weight: 700;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.brand-text {
  display: flex;
  flex-direction: column;
  gap: 0.1rem;
  overflow: hidden;
  /* fade out with width so brand area doesn't reflow */
  max-width: 160px;
  opacity: 1;
  transition: opacity 0.2s ease, max-width 0.25s ease;
}

.sidebar.collapsed .brand-text {
  max-width: 0;
  opacity: 0;
}

.brand-name {
  font-size: 0.95rem;
  font-weight: 700;
  color: #f8fafc;
  letter-spacing: -0.01em;
  white-space: nowrap;
}

.brand-sub {
  font-size: 0.68rem;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  white-space: nowrap;
}

.toggle-btn {
  width: 28px;
  height: 28px;
  border-radius: 6px;
  border: 1px solid #334155;
  background: transparent;
  color: #64748b;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  flex-shrink: 0;
  transition: background 0.15s ease, color 0.15s ease;
}

.toggle-btn:hover {
  background: #1e293b;
  color: #e2e8f0;
}

.toggle-btn svg {
  width: 14px;
  height: 14px;
}

.sidebar.collapsed .toggle-btn {
  /* Keep button accessible in collapsed state */
  margin: 0 auto;
}

/* Nav */
.sidebar-nav {
  flex: 1;
  padding: 0.75rem 0.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  overflow-y: auto;
  overflow-x: visible;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  border-radius: 6px;
  font-size: 0.875rem;
  font-weight: 500;
  color: #94a3b8;
  text-decoration: none;
  border-left: 3px solid transparent;
  transition: color 0.15s ease, background 0.15s ease;
  position: relative;
  white-space: nowrap;
  overflow: hidden; /* clip label when collapsing */
}

.nav-item:hover {
  color: #e2e8f0;
  background: #1e293b;
}

.nav-item.active {
  color: #f8fafc;
  background: #1e3a5f;
  border-left-color: #3b82f6;
  padding-left: calc(0.75rem - 3px);
}

/* Icon */
.nav-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 20px;
  height: 20px;
}

.nav-icon svg {
  width: 18px;
  height: 18px;
}

/* Label fade */
.nav-label {
  flex: 1;
  max-width: 160px;
  opacity: 1;
  transition: opacity 0.15s ease, max-width 0.25s ease;
}

.sidebar.collapsed .nav-label {
  max-width: 0;
  opacity: 0;
}

/* CSS tooltip in collapsed mode */
.sidebar.collapsed .nav-item::after {
  content: attr(data-label);
  position: absolute;
  left: calc(100% + 0.75rem);
  top: 50%;
  transform: translateY(-50%);
  background: #1e293b;
  color: #f8fafc;
  border: 1px solid #334155;
  padding: 0.375rem 0.75rem;
  border-radius: 6px;
  font-size: 0.8rem;
  white-space: nowrap;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.15s ease;
  z-index: 200;
}

.sidebar.collapsed .nav-item:hover::after {
  opacity: 1;
}

/* Centre icons when collapsed */
.sidebar.collapsed .nav-item {
  justify-content: center;
  padding-left: 0.75rem;
  padding-right: 0.75rem;
}

.sidebar.collapsed .nav-item.active {
  padding-left: calc(0.75rem - 3px);
}

/* Footer */
.sidebar-footer {
  padding: 0.875rem 0.75rem;
  border-top: 1px solid #1e293b;
}

.footer-items {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.footer-collapsed {
  align-items: center;
}
</style>
