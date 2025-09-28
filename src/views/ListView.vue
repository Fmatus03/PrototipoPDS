<template>
  <div class="list-container">
    <div class="header">
      <h1>Visualización de Incidentes</h1>
      <p>Consulta, filtra y monitorea incidentes del parque nacional</p>
    </div>

    <div class="controls">
      <!-- Buscador por título -->
      <div class="search-section">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="🔍 Buscar por título del incidente..."
          class="search-field"
        />
      </div>

      <!-- Filtros -->
      <div class="filters-grid">
        <div class="filter-group">
          <label>Categoría:</label>
          <select v-model="categoryFilter" class="filter-select">
            <option value="all">Todas las categorías</option>
            <option value="Accidente">Accidente</option>
            <option value="Basura">Basura</option>
            <option value="Animal muerto">Animal muerto</option>
          </select>
        </div>

        <div class="filter-group">
          <label>Estado:</label>
          <select v-model="statusFilter" class="filter-select">
            <option value="all">Todos los estados</option>
            <option value="Abierto">Abierto</option>
            <option value="En revisión">En revisión</option>
            <option value="Cerrado">Cerrado</option>
          </select>
        </div>

        <div class="filter-group">
          <label>Ruta:</label>
          <select v-model="routeFilter" class="filter-select">
            <option value="all">Todas las rutas</option>
            <option v-for="route in availableRoutes" :key="route" :value="route">
              {{ route }}
            </option>
          </select>
        </div>

        <div class="filter-group">
          <label>Fecha de reporte:</label>
          <input v-model="dateFilter" type="date" class="filter-select" />
        </div>

        <div class="filter-group">
          <button @click="clearFilters" class="btn-clear-filters">
            <i class="icon">↺</i>
            Limpiar Filtros
          </button>
        </div>
      </div>
    </div>

    <div class="list-content">
      <div class="results-header">
        <h3>Incidentes Encontrados: {{ filteredIncidents.length }}</h3>
      </div>

      <transition-group name="list" tag="div" class="incidents-list">
        <div
          v-for="incident in filteredIncidents"
          :key="incident.id"
          class="incident-card"
          :class="{
            'status-open': incident.status === 'Abierto',
            'status-review': incident.status === 'En revisión',
            'status-closed': incident.status === 'Cerrado',
          }"
          @click="openIncidentDetail(incident)"
        >
          <div class="incident-header">
            <div
              class="incident-category"
              :class="'category-' + incident.category.toLowerCase().replace(' ', '-')"
            >
              {{ incident.category }}
            </div>
            <div
              class="incident-status clickable-status"
              :class="'status-' + incident.status.toLowerCase().replace(' ', '-')"
              @click.stop="changeIncidentStatus(incident)"
              :title="'Clic para cambiar estado: ' + getNextStatus(incident.status)"
            >
              {{ incident.status }}
            </div>
          </div>

          <div class="incident-content">
            <h4 class="incident-title">{{ incident.title }}</h4>
            <div class="incident-meta">
              <div class="meta-item">
                <span class="meta-icon">📍</span>
                <span class="meta-text">{{ incident.location }}</span>
              </div>
              <div class="meta-item">
                <span class="meta-icon">🛤️</span>
                <span class="meta-text">{{ incident.route }}</span>
              </div>
              <div class="meta-item">
                <span class="meta-icon">📅</span>
                <span class="meta-text">{{ formatDate(incident.reportDate) }}</span>
              </div>
              <div class="meta-item">
                <span class="meta-icon">👤</span>
                <span class="meta-text">{{ incident.reporter }}</span>
              </div>
            </div>
          </div>
        </div>
      </transition-group>

      <div v-if="filteredIncidents.length === 0" class="empty-state">
        <div class="empty-icon">📋</div>
        <h3>{{ getEmptyMessage() }}</h3>
        <p>{{ getEmptySubMessage() }}</p>
      </div>
    </div>

    <!-- Modal de detalle del incidente -->
    <div v-if="selectedIncident" class="modal-overlay" @click="closeIncidentDetail">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <div class="modal-title">
            <h2>{{ selectedIncident.title }}</h2>
            <div class="modal-badges">
              <span
                class="incident-category"
                :class="'category-' + selectedIncident.category.toLowerCase().replace(' ', '-')"
              >
                {{ selectedIncident.category }}
              </span>
              <span
                class="incident-status clickable-status"
                :class="'status-' + selectedIncident.status.toLowerCase().replace(' ', '-')"
                @click="changeIncidentStatus(selectedIncident)"
                :title="'Clic para cambiar estado: ' + getNextStatus(selectedIncident.status)"
              >
                {{ selectedIncident.status }}
              </span>
            </div>
          </div>
          <button @click="closeIncidentDetail" class="btn-close">
            <i class="icon">×</i>
          </button>
        </div>

        <div class="modal-body">
          <div class="detail-section">
            <h4>Información General</h4>
            <div class="detail-grid">
              <div class="detail-item">
                <span class="detail-label">📅 Fecha de Reporte:</span>
                <span class="detail-value">{{
                  formatDetailDate(selectedIncident.reportDate)
                }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">👤 Reportado por:</span>
                <span class="detail-value">{{ selectedIncident.reporter }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">📍 Ubicación:</span>
                <span class="detail-value">{{ selectedIncident.location }}</span>
              </div>
              <div class="detail-item">
                <span class="detail-label">🛤️ Ruta:</span>
                <span class="detail-value">{{ selectedIncident.route }}</span>
              </div>
            </div>
          </div>

          <div class="detail-section" v-if="selectedIncident.description">
            <h4>Descripción del Incidente</h4>
            <div class="detail-description">
              {{ selectedIncident.description }}
            </div>
          </div>

          <div class="detail-section" v-if="selectedIncident.image">
            <h4>Imagen</h4>
            <div class="detail-image">
              <img :src="selectedIncident.image" :alt="selectedIncident.title" />
            </div>
          </div>

          <div class="detail-section" v-if="selectedIncident.audio">
            <h4>Audio</h4>
            <div class="detail-audio">
              <audio controls>
                <source :src="selectedIncident.audio" type="audio/mpeg" />
                Tu navegador no soporta el elemento audio.
              </audio>
            </div>
          </div>
        </div>

        <div class="modal-footer">
          <button @click="closeIncidentDetail" class="btn-close-modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

interface Incident {
  id: number
  title: string
  category: 'Accidente' | 'Basura' | 'Animal muerto'
  status: 'Abierto' | 'En revisión' | 'Cerrado'
  route: string
  description: string
  reportDate: Date
  location: string
  reporter: string
  priority?: string
  image?: string
  audio?: string
}

const incidents = ref<Incident[]>([])
const searchQuery = ref('')
const categoryFilter = ref<'all' | 'Accidente' | 'Basura' | 'Animal muerto'>('all')
const statusFilter = ref<'all' | 'Abierto' | 'En revisión' | 'Cerrado'>('all')
const routeFilter = ref<'all' | string>('all')
const dateFilter = ref('')
const availableRoutes = ref<string[]>([])
const selectedIncident = ref<Incident | null>(null)

// Computed properties
const filteredIncidents = computed(() => {
  let filtered = incidents.value

  // Filter by search query (title)
  if (searchQuery.value.trim()) {
    filtered = filtered.filter((incident) =>
      incident.title.toLowerCase().includes(searchQuery.value.toLowerCase()),
    )
  }

  // Filter by category
  if (categoryFilter.value !== 'all') {
    filtered = filtered.filter((incident) => {
      return incident.category.trim() === categoryFilter.value.trim()
    })
  }

  // Filter by status
  if (statusFilter.value !== 'all') {
    filtered = filtered.filter((incident) => {
      return incident.status.trim() === statusFilter.value.trim()
    })
  }

  // Filter by route
  if (routeFilter.value !== 'all') {
    filtered = filtered.filter((incident) => incident.route === routeFilter.value)
  }

  // Filter by date
  if (dateFilter.value) {
    const filterDate = new Date(dateFilter.value)
    console.log('Date filter value:', dateFilter.value)
    console.log('Filter date object:', filterDate)

    filtered = filtered.filter((incident) => {
      // Normalizar ambas fechas a solo la parte de fecha (sin hora)
      const incidentDate = new Date(
        incident.reportDate.getFullYear(),
        incident.reportDate.getMonth(),
        incident.reportDate.getDate(),
      )
      const targetDate = new Date(
        filterDate.getFullYear(),
        filterDate.getMonth(),
        filterDate.getDate(),
      )

      console.log(
        `Incident ${incident.id} date: ${incident.reportDate} → normalized: ${incidentDate}`,
      )
      console.log(`Target date: ${targetDate}`)
      console.log(`Match: ${incidentDate.getTime() === targetDate.getTime()}`)

      return incidentDate.getTime() === targetDate.getTime()
    })
  }

  return filtered
})

// Methods
const openIncidentDetail = (incident: Incident) => {
  selectedIncident.value = incident
}

const closeIncidentDetail = () => {
  selectedIncident.value = null
}

const changeIncidentStatus = (incident: Incident) => {
  const statuses: Array<'Abierto' | 'En revisión' | 'Cerrado'> = [
    'Abierto',
    'En revisión',
    'Cerrado',
  ]
  const currentIndex = statuses.indexOf(incident.status)
  const nextIndex = (currentIndex + 1) % statuses.length
  incident.status = statuses[nextIndex]
  saveToLocalStorage()
}

const clearFilters = () => {
  searchQuery.value = ''
  categoryFilter.value = 'all'
  statusFilter.value = 'all'
  routeFilter.value = 'all'
  dateFilter.value = ''
}

const updateAvailableRoutes = () => {
  const routes = [...new Set(incidents.value.map((incident) => incident.route))]
  availableRoutes.value = routes.sort()
}

const formatDate = (date: Date) => {
  return new Intl.DateTimeFormat('es-ES', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
  }).format(date)
}

const formatDetailDate = (date: Date) => {
  return new Intl.DateTimeFormat('es-ES', {
    weekday: 'long',
    day: '2-digit',
    month: 'long',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
  }).format(date)
}

const getNextStatus = (currentStatus: 'Abierto' | 'En revisión' | 'Cerrado') => {
  const statuses: Array<'Abierto' | 'En revisión' | 'Cerrado'> = [
    'Abierto',
    'En revisión',
    'Cerrado',
  ]
  const currentIndex = statuses.indexOf(currentStatus)
  const nextIndex = (currentIndex + 1) % statuses.length
  return statuses[nextIndex]
}

const getEmptyMessage = () => {
  if (searchQuery.value.trim()) return 'No se encontraron incidentes'
  if (categoryFilter.value !== 'all') return `No hay incidentes de tipo ${categoryFilter.value}`
  if (statusFilter.value !== 'all') return `No hay incidentes con estado ${statusFilter.value}`
  if (routeFilter.value !== 'all') return `No hay incidentes en ${routeFilter.value}`
  if (dateFilter.value) return 'No hay incidentes en la fecha seleccionada'
  return 'No hay incidentes registrados'
}

const getEmptySubMessage = () => {
  if (
    searchQuery.value.trim() ||
    categoryFilter.value !== 'all' ||
    statusFilter.value !== 'all' ||
    routeFilter.value !== 'all' ||
    dateFilter.value
  ) {
    return 'Intenta ajustar los filtros de búsqueda.'
  }
  return 'Comienza agregando el primer incidente usando el formulario de arriba.'
}

// Local storage
const saveToLocalStorage = () => {
  localStorage.setItem('incidents', JSON.stringify(incidents.value))
}

const loadFromLocalStorage = () => {
  const stored = localStorage.getItem('incidents')
  if (stored) {
    try {
      const parsed = JSON.parse(stored)
      incidents.value = parsed.map(
        (item: {
          id: number
          title: string
          category: string
          status: string
          route: string
          description: string
          dateReported: string
          location?: string
          reporter: string
          priority?: string
          lastUpdate: string
        }) => ({
          ...item,
          category: item.category as 'Accidente' | 'Basura' | 'Animal muerto',
          status: item.status as 'Abierto' | 'En revisión' | 'Cerrado',
          location: item.location || 'Ubicación no especificada',
          reportDate: new Date(item.dateReported),
        }),
      )
    } catch (error) {
      console.error('Error loading from localStorage:', error)
      createSampleData()
    }
  } else {
    createSampleData()
  }
}

const createSampleData = () => {
  const sampleIncidents = [
    {
      id: 1,
      title: 'Árbol caído bloquea Sendero Los Huerquenes',
      category: 'Basura',
      status: 'Abierto',
      route: 'Sendero El Bosque',
      description:
        'Un árbol grande ha caído después de la tormenta de anoche, bloqueando completamente el paso en el kilómetro 2.5 del sendero.',
      dateReported: '2024-09-28',
      location: 'Km 2.5 Sendero Los Huerquenes',
      lastUpdate: '2024-09-28',
      reporter: 'Guardaparque Juan Pérez',
      priority: 'alta',
    },
    {
      id: 2,
      title: 'Animal muerto encontrado en el sendero',
      category: 'Animal muerto',
      status: 'En revisión',
      route: 'Ruta del Mirador',
      description:
        'Se encontró un animal muerto que debe ser removido del sendero principal por motivos sanitarios.',
      dateReported: '2024-09-27',
      location: 'Cerro San Sebastián - Mirador',
      lastUpdate: '2024-09-27',
      reporter: 'María González',
      priority: 'alta',
    },
    {
      id: 3,
      title: 'Excursionista con tobillo torcido en Laguna Verde',
      category: 'Accidente',
      status: 'Cerrado',
      route: 'Camino al Lago',
      description:
        'Rescate exitoso de excursionista con lesión en tobillo. Fue evacuado por equipo de rescate.',
      dateReported: '2024-09-26',
      location: 'Laguna Verde - Sector Norte',
      lastUpdate: '2024-09-26',
      reporter: 'Carlos Ramírez',
      priority: 'alta',
    },
    {
      id: 4,
      title: 'Acumulación de basura en área de picnic',
      category: 'Basura',
      status: 'Abierto',
      route: 'Área de Picnic',
      description:
        'Se ha acumulado una cantidad considerable de basura dejada por visitantes del fin de semana.',
      dateReported: '2024-09-25',
      location: 'Área de Picnic - Entrada Principal',
      lastUpdate: '2024-09-25',
      reporter: 'Ana Silva',
      priority: 'media',
    },
    {
      id: 5,
      title: 'Caída durante caminata nocturna',
      category: 'Accidente',
      status: 'En revisión',
      route: 'Sendero del Río',
      description:
        'Visitante sufrió caída leve durante caminata nocturna. Se brindó primeros auxilios.',
      dateReported: '2024-09-24',
      location: 'Sendero Quinchol - Puente del Arroyo',
      lastUpdate: '2024-09-24',
      reporter: 'Pedro Morales',
      priority: 'media',
    },
  ]

  // Convertir a la estructura esperada por la interfaz
  incidents.value = sampleIncidents.map((item) => ({
    ...item,
    category: item.category as 'Accidente' | 'Basura' | 'Animal muerto',
    status: item.status as 'Abierto' | 'En revisión' | 'Cerrado',
    reportDate: new Date(item.dateReported),
  }))

  // Guardar en localStorage usando la estructura original
  localStorage.setItem('incidents', JSON.stringify(sampleIncidents))
}

// Lifecycle
onMounted(() => {
  loadFromLocalStorage()
  updateAvailableRoutes()
})
</script>

<style scoped>
.list-container {
  width: 100vw;
  max-width: 100vw;
  margin: 0;
  padding: 2rem 0;
  min-height: 100vh;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow-x: hidden;
}

.header {
  text-align: center;
  margin-bottom: 2rem;
  width: 100%;
  max-width: 1200px;
  padding: 0 2rem;
}

.header h1 {
  font-size: 2.5rem;
  color: #2c3e50;
  margin-bottom: 0.5rem;
}

.header p {
  color: #7f8c8d;
  font-size: 1.1rem;
}

.controls,
.list-content {
  width: 100%;
  max-width: 1200px;
  margin-bottom: 2rem;
  padding-left: 2rem;
  padding-right: 2rem;
  box-sizing: border-box;
}

.controls {
  background: white;
  border-radius: 15px;
  padding: 2rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.search-section {
  margin-bottom: 1.5rem;
}

.search-field {
  width: 100%;
  padding: 1rem;
  border: 2px solid #e0e6ed;
  border-radius: 10px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.search-field:focus {
  outline: none;
  border-color: #3498db;
}

.add-item-form {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.input-field {
  flex: 1;
  padding: 1rem;
  border: 2px solid #e0e6ed;
  border-radius: 10px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.input-field:focus {
  outline: none;
  border-color: #3498db;
}

.btn-add {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 1rem 1.5rem;
  background: #27ae60;
  color: white;
  border: none;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-add:hover:not(:disabled) {
  background: #229954;
  transform: translateY(-2px);
}

.btn-add:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.filters-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  align-items: end;
}

.filter-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.filter-group label {
  font-weight: 600;
  color: #2c3e50;
  font-size: 0.9rem;
}

.filter-select {
  padding: 0.75rem;
  border: 2px solid #e0e6ed;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.filter-select:focus {
  outline: none;
  border-color: #3498db;
}

.btn-clear-filters {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1rem;
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s ease;
  font-weight: 600;
}

.btn-clear-filters:hover {
  background: #c0392b;
}

.list-content {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.results-header {
  margin-bottom: 1.5rem;
  padding-bottom: 1rem;
  border-bottom: 2px solid #f8f9fa;
}

.results-header h3 {
  color: #2c3e50;
  font-size: 1.25rem;
}

.incidents-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.incident-card {
  background: #f8f9fa;
  border-radius: 12px;
  padding: 1.5rem;
  border-left: 4px solid;
  transition: all 0.3s ease;
  position: relative;
}

.incident-card.status-open {
  border-left-color: #e74c3c;
}
.incident-card.status-review {
  border-left-color: #f39c12;
}
.incident-card.status-closed {
  border-left-color: #27ae60;
}

.incident-card:hover {
  transform: translateX(5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.incident-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.incident-category,
.incident-status {
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  text-transform: uppercase;
}

.incident-category.category-accidente {
  background: #fdf2f2;
  color: #e74c3c;
}
.incident-category.category-basura {
  background: #fef9e7;
  color: #f39c12;
}
.incident-category.category-animal-muerto {
  background: #f3e8ff;
  color: #9b59b6;
}

.incident-status.status-abierto {
  background: #fdf2f2;
  color: #e74c3c;
}
.incident-status.status-en-revisión {
  background: #fef9e7;
  color: #f39c12;
}
.incident-status.status-cerrado {
  background: #eafaf1;
  color: #27ae60;
}

/* Estilos para el estado clickeable */
.clickable-status {
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  user-select: none;
}

.clickable-status:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.clickable-status.status-abierto:hover {
  background: #e74c3c;
  color: white;
}

.clickable-status.status-en-revisión:hover {
  background: #f39c12;
  color: white;
}

.clickable-status.status-cerrado:hover {
  background: #27ae60;
  color: white;
}

.clickable-status:active {
  transform: scale(0.95);
}

.incident-title {
  font-size: 1.2rem;
  color: #2c3e50;
  margin-bottom: 1rem;
  font-weight: 600;
}

.incident-meta {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.meta-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.9rem;
  color: #6c757d;
}

.meta-icon {
  font-size: 1rem;
}

.incident-actions {
  position: absolute;
  top: 1rem;
  right: 1rem;
  display: flex;
  gap: 0.5rem;
}

.btn-status,
.btn-edit,
.btn-delete {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
  border-radius: 5px;
  transition: all 0.3s ease;
  width: 35px;
  height: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-status:hover {
  background: #3498db;
  color: white;
}
.btn-edit:hover {
  background: #f39c12;
  color: white;
}
.btn-delete:hover {
  background: #e74c3c;
  color: white;
}

.empty-state {
  text-align: center;
  padding: 3rem 1rem;
  color: #7f8c8d;
}

.empty-icon {
  font-size: 4rem;
  margin-bottom: 1rem;
}

.empty-state h3 {
  margin-bottom: 0.5rem;
  color: #2c3e50;
}

/* Animations */
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.icon {
  font-size: 1rem;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(4px);
}

.modal-content {
  background: white;
  border-radius: 20px;
  width: 90%;
  max-width: 800px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
  animation: modalSlideIn 0.3s ease-out;
}

@keyframes modalSlideIn {
  from {
    opacity: 0;
    transform: translateY(-50px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 2rem 2rem 1rem 2rem;
  border-bottom: 2px solid #f8f9fa;
}

.modal-title h2 {
  color: #2c3e50;
  font-size: 1.8rem;
  margin-bottom: 1rem;
  line-height: 1.3;
}

.modal-badges {
  display: flex;
  gap: 0.75rem;
}

.btn-close {
  background: #e74c3c;
  color: white;
  border: none;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
  transition: all 0.3s ease;
}

.btn-close:hover {
  background: #c0392b;
  transform: scale(1.1);
}

.modal-body {
  padding: 1rem 2rem;
}

.detail-section {
  margin-bottom: 2rem;
}

.detail-section h4 {
  color: #2c3e50;
  font-size: 1.2rem;
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid #f8f9fa;
}

.detail-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.detail-label {
  font-weight: 600;
  color: #7f8c8d;
  font-size: 0.9rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.detail-value {
  color: #2c3e50;
  font-size: 1rem;
  padding: 0.5rem 0;
}

.detail-description {
  background: #f8f9fa;
  padding: 1.5rem;
  border-radius: 10px;
  line-height: 1.6;
  color: #2c3e50;
  border-left: 4px solid #3498db;
}

.detail-image {
  text-align: center;
}

.detail-image img {
  max-width: 100%;
  height: auto;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  max-height: 400px;
  object-fit: cover;
}

.detail-audio {
  background: #f8f9fa;
  padding: 1rem;
  border-radius: 10px;
  text-align: center;
}

.detail-audio audio {
  width: 100%;
  max-width: 400px;
}

.modal-footer {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem 2rem 2rem 2rem;
  border-top: 2px solid #f8f9fa;
  gap: 1rem;
}

.btn-change-status {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.5rem;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
}

.btn-change-status:hover {
  background: #2980b9;
  transform: translateY(-2px);
}

.btn-close-modal {
  padding: 0.75rem 1.5rem;
  background: #95a5a6;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
}

.btn-close-modal:hover {
  background: #7f8c8d;
}

/* Hacer las tarjetas clickeables */
.incident-card {
  cursor: pointer;
  transition: all 0.3s ease;
}

.incident-card:hover {
  transform: translateX(8px) scale(1.02);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
}

@media (max-width: 768px) {
  .list-container {
    padding: 1rem 0;
  }

  .header,
  .controls,
  .list-content {
    padding-left: 1rem;
    padding-right: 1rem;
  }

  .filters-grid {
    grid-template-columns: 1fr;
  }

  .incident-meta {
    grid-template-columns: 1fr;
  }

  .incident-actions {
    position: relative;
    top: auto;
    right: auto;
    justify-content: center;
    margin-top: 1rem;
  }

  /* Modal responsive */
  .modal-content {
    width: 95%;
    max-height: 95vh;
  }

  .modal-header,
  .modal-body,
  .modal-footer {
    padding: 1rem;
  }

  .modal-title h2 {
    font-size: 1.4rem;
  }

  .detail-grid {
    grid-template-columns: 1fr;
  }

  .modal-footer {
    flex-direction: column;
    gap: 0.5rem;
  }

  .btn-change-status,
  .btn-close-modal {
    width: 100%;
    justify-content: center;
  }
}
</style>
