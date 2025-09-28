<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'

const showNavigation = true // Mostrar navegación en todas las páginas
</script>

<template>
  <div class="app-container">
    <nav v-if="showNavigation" class="main-nav">
      <div class="nav-container">
        <div class="nav-brand">
          <RouterLink to="/" class="brand-link">
            <span class="brand-icon">🏞️</span>
            <span class="brand-text">Sistema de Incidentes</span>
          </RouterLink>
        </div>

        <div class="nav-links">
          <RouterLink to="/" class="nav-link">
            <span class="nav-icon">🏠</span>
            Inicio
          </RouterLink>
          <RouterLink to="/lista" class="nav-link">
            <span class="nav-icon">📋</span>
            Incidentes
          </RouterLink>
          <RouterLink to="/dashboard" class="nav-link">
            <span class="nav-icon">📊</span>
            Dashboard
          </RouterLink>
          <RouterLink to="/about" class="nav-link">
            <span class="nav-icon">ℹ️</span>
            Acerca de
          </RouterLink>
        </div>
      </div>
    </nav>

    <main class="main-content" :class="{ 'with-nav': showNavigation }">
      <RouterView />
    </main>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: column;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

.main-nav {
  background: white;
  border-bottom: 1px solid #e0e6ed;
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.nav-container {
  width: 100%;
  margin: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 5%;
  box-sizing: border-box;
}

.nav-brand {
  display: flex;
  align-items: center;
}

.brand-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  text-decoration: none;
  color: #2c3e50;
  font-weight: 700;
  font-size: 1.25rem;
}

.brand-icon {
  font-size: 1.5rem;
}

.brand-text {
  color: #2c3e50;
}

.nav-links {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  text-decoration: none;
  color: #7f8c8d;
  font-weight: 500;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.nav-link:hover {
  color: #3498db;
  background: #f8f9fa;
}

.nav-link.router-link-active {
  color: #3498db;
  background: #e3f2fd;
}

.nav-icon {
  font-size: 1.1rem;
}

.main-content {
  flex: 1;
  width: 100vw;
  max-width: 100vw;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
  box-sizing: border-box;
}

.main-content.with-nav {
  margin-top: 0;
}

@media (max-width: 768px) {
  .nav-container {
    padding: 1rem 3%;
    flex-direction: column;
    gap: 1rem;
  }

  .nav-links {
    gap: 1rem;
    flex-wrap: wrap;
    justify-content: center;
  }

  .nav-link {
    padding: 0.75rem 1rem;
    min-width: 80px;
    justify-content: center;
  }
}

@media (max-width: 480px) {
  .nav-links {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0.5rem;
    width: 100%;
  }

  .nav-link {
    text-align: center;
  }
}
</style>
