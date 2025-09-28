<template>
  <div class="dashboard-container">
    <div class="header">
      <h1>Dashboard de Incidentes en Parques</h1>
      <p>Estadísticas y métricas de incidentes en tiempo real</p>
    </div>

    <div class="metrics-grid">
      <div class="metric-card primary">
        <div class="metric-icon">📊</div>
        <div class="metric-content">
          <div class="metric-value">{{ totalIncidents }}</div>
          <div class="metric-label">Total de Incidentes</div>
          <div class="metric-change neutral">En sistema</div>
        </div>
      </div>

      <div class="metric-card warning">
        <div class="metric-icon">🚨</div>
        <div class="metric-content">
          <div class="metric-value">{{ openIncidents }}</div>
          <div class="metric-label">Abiertos</div>
          <div class="metric-change">
            {{ getPercentage(openIncidents, totalIncidents) }}% del total
          </div>
        </div>
      </div>

      <div class="metric-card info">
        <div class="metric-icon">🔍</div>
        <div class="metric-content">
          <div class="metric-value">{{ inReviewIncidents }}</div>
          <div class="metric-label">En Revisión</div>
          <div class="metric-change">
            {{ getPercentage(inReviewIncidents, totalIncidents) }}% del total
          </div>
        </div>
      </div>

      <div class="metric-card success">
        <div class="metric-icon">✅</div>
        <div class="metric-content">
          <div class="metric-value">{{ closedIncidents }}</div>
          <div class="metric-label">Cerrados</div>
          <div class="metric-change">
            {{ getPercentage(closedIncidents, totalIncidents) }}% del total
          </div>
        </div>
      </div>
    </div>

    <!-- Gráficos Principales -->
    <div class="charts-grid">
      <div class="chart-card">
        <div class="chart-header">
          <h3>📊 Distribución por Estado</h3>
          <select v-model="statusChartType" class="chart-selector">
            <option value="doughnut">Gráfico de Dona</option>
            <option value="pie">Gráfico de Pastel</option>
          </select>
        </div>
        <div class="chart-container">
          <Doughnut :data="statusChartData" :options="doughnutOptions" />
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-header">
          <h3>📈 Incidentes por Categoría</h3>
          <select v-model="categoryChartType" class="chart-selector">
            <option value="bar">Barras</option>
            <option value="horizontal">Barras Horizontales</option>
          </select>
        </div>
        <div class="chart-container">
          <Bar :data="categoryChartData" :options="barOptions" />
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-header">
          <h3>📅 Tendencia Temporal</h3>
          <select v-model="trendPeriod" class="chart-selector">
            <option value="daily">Diaria</option>
            <option value="weekly">Semanal</option>
            <option value="monthly">Mensual</option>
            <option value="yearly">Anual</option>
          </select>
        </div>
        <div class="chart-container">
          <Line :data="temporalTrendData" :options="chartOptions" />
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-header">
          <h3>🗺️ Análisis por Rutas</h3>
        </div>
        <div class="chart-container">
          <Radar :data="routeData" :options="radarOptions" />
        </div>
      </div>
    </div>

    <!-- Gráficos Avanzados de Análisis Temporal -->
    <div class="advanced-charts-section">
      <h2 class="section-title">🕐 Análisis Temporal Avanzado</h2>

      <div class="charts-grid">
        <div class="chart-card">
          <div class="chart-header">
            <h3>⏰ Incidentes por Hora del Día</h3>
            <span class="insight-badge">Identifica horas críticas</span>
          </div>
          <div class="chart-container">
            <Bar :data="hourlyIncidentsData" :options="hourlyOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>📆 Incidentes por Día de la Semana</h3>
            <span class="insight-badge">Detecta patrones semanales</span>
          </div>
          <div class="chart-container">
            <Line :data="weeklyPatternData" :options="weeklyOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>🌱 Tendencias Estacionales</h3>
            <span class="insight-badge">Análisis por temporada</span>
          </div>
          <div class="chart-container">
            <Doughnut :data="seasonalData" :options="seasonalOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>⚡ Tiempo de Resolución</h3>
            <span class="insight-badge">Eficiencia del equipo</span>
          </div>
          <div class="chart-container">
            <Bar :data="resolutionTimeData" :options="resolutionOptions" />
          </div>
        </div>
      </div>
    </div>

    <!-- Comparaciones e Indicadores -->
    <div class="comparison-section">
      <h2 class="section-title">📊 Comparaciones e Indicadores</h2>

      <div class="charts-grid">
        <div class="chart-card">
          <div class="chart-header">
            <h3>📈 Comparación Mensual</h3>
            <div class="comparison-controls">
              <select v-model="comparisonYear" class="year-selector">
                <option value="2024">2024</option>
                <option value="2023">2023</option>
                <option value="2022">2022</option>
              </select>
            </div>
          </div>
          <div class="chart-container">
            <Line :data="monthlyComparisonData" :options="comparisonOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>🏆 Eficiencia de Resolución</h3>
            <span class="metric-badge success">↗️ +23% este mes</span>
          </div>
          <div class="chart-container">
            <Bar :data="efficiencyData" :options="efficiencyOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>⚖️ Resueltos vs Pendientes</h3>
            <span class="metric-badge info">Análisis de carga</span>
          </div>
          <div class="chart-container">
            <Doughnut :data="resolutionStatusData" :options="resolutionStatusOptions" />
          </div>
        </div>

        <div class="chart-card">
          <div class="chart-header">
            <h3>🎯 Indicadores de Tendencia</h3>
            <span class="metric-badge warning">Predicción 7 días</span>
          </div>
          <div class="chart-container">
            <Line :data="trendPredictionData" :options="predictionOptions" />
          </div>
        </div>
      </div>
    </div>

    <!-- Filtros de Tiempo -->
    <div class="filters-section">
      <div class="filter-card">
        <h3>📅 Filtros Temporales</h3>
        <div class="time-filters">
          <select v-model="selectedTimeRange" @change="updateTimeRange" class="time-selector">
            <option value="daily">Resumen Diario</option>
            <option value="weekly">Resumen Semanal</option>
            <option value="monthly">Resumen Mensual</option>
            <option value="yearly">Resumen Anual</option>
          </select>
          <input type="date" v-model="selectedDate" @change="updateTimeRange" class="date-picker" />
        </div>
      </div>

      <div class="search-card">
        <h3>🔍 Búsqueda Rápida</h3>
        <div class="search-container">
          <input
            type="text"
            v-model="searchKeyword"
            @input="searchIncidents"
            placeholder="Buscar en descripción de incidentes..."
            class="search-input"
          />
          <div class="search-results" v-if="searchResults.length > 0">
            <div class="search-count">{{ searchResults.length }} incidentes encontrados</div>
          </div>
        </div>
      </div>

      <div class="export-card">
        <h3>📄 Exportación</h3>
        <div class="export-buttons">
          <button @click="exportToPDF" class="export-btn pdf-btn">📄 Exportar a PDF</button>
          <button @click="exportToExcel" class="export-btn excel-btn">📊 Exportar a Excel</button>
        </div>
      </div>
    </div>

    <!-- Tablas de Resumen -->
    <div class="tables-section">
      <div class="table-card">
        <div class="table-header">
          <h3>📋 Tabla Resumen por Categoría</h3>
          <div class="table-actions">
            <button @click="sortCategoryTable" class="sort-btn">🔄 Ordenar</button>
          </div>
        </div>
        <div class="table-container">
          <table class="summary-table">
            <thead>
              <tr>
                <th>Categoría</th>
                <th>Total Incidentes</th>
                <th>Abiertos</th>
                <th>En Revisión</th>
                <th>Cerrados</th>
                <th>% del Total</th>
                <th>Tiempo Promedio Resolución</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="category in categoryTableData" :key="category.name">
                <td class="category-cell">
                  <span class="category-badge" :style="{ backgroundColor: category.color }">
                    {{ category.name }}
                  </span>
                </td>
                <td class="number-cell">{{ category.total }}</td>
                <td class="status-cell open">{{ category.open }}</td>
                <td class="status-cell review">{{ category.inReview }}</td>
                <td class="status-cell closed">{{ category.closed }}</td>
                <td class="percentage-cell">{{ category.percentage }}%</td>
                <td class="time-cell">{{ category.avgResolutionTime }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div class="table-card">
        <div class="table-header">
          <h3>📊 Tabla Resumen por Estado</h3>
          <div class="table-actions">
            <button @click="sortStatusTable" class="sort-btn">🔄 Ordenar</button>
          </div>
        </div>
        <div class="table-container">
          <table class="summary-table">
            <thead>
              <tr>
                <th>Estado</th>
                <th>Cantidad</th>
                <th>% del Total</th>
                <th>Tiempo Promedio en Estado</th>
                <th>Tendencia</th>
                <th>Prioridad Alta</th>
                <th>Prioridad Media</th>
                <th>Prioridad Baja</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="status in statusTableData" :key="status.name">
                <td class="status-cell">
                  <span class="status-badge" :class="status.cssClass">
                    {{ status.displayName }}
                  </span>
                </td>
                <td class="number-cell">{{ status.count }}</td>
                <td class="percentage-cell">{{ status.percentage }}%</td>
                <td class="time-cell">{{ status.avgTimeInStatus }}</td>
                <td class="trend-cell">
                  <span class="trend" :class="status.trend">
                    {{ status.trendText }}
                  </span>
                </td>
                <td class="priority-cell high">{{ status.highPriority }}</td>
                <td class="priority-cell medium">{{ status.mediumPriority }}</td>
                <td class="priority-cell low">{{ status.lowPriority }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Análisis por Rutas -->
    <div class="routes-section">
      <div class="route-card">
        <div class="route-header">
          <h3>🗺️ Ranking de Rutas con Más Incidentes</h3>
          <select v-model="routeAnalysisType" class="analysis-selector">
            <option value="total">Total de Incidentes</option>
            <option value="severity">Por Severidad</option>
            <option value="resolution">Tiempo de Resolución</option>
          </select>
        </div>
        <div class="route-ranking">
          <div v-for="(route, index) in routeRanking" :key="route.name" class="route-item">
            <div class="route-rank">{{ index + 1 }}</div>
            <div class="route-info">
              <div class="route-name">{{ route.name }}</div>
              <div class="route-stats">
                <span class="route-count">{{ route.incidents }} incidentes</span>
                <span class="route-percentage">{{ route.percentage }}% del total</span>
              </div>
            </div>
            <div class="route-indicators">
              <div class="danger-level" :class="route.dangerLevel">
                {{ route.dangerText }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="route-map-card">
        <div class="route-header">
          <h3>🗺️ Mapa de Incidentes por Zona</h3>
        </div>
        <div class="route-map">
          <div class="map-grid">
            <div
              v-for="zone in zoneData"
              :key="zone.name"
              class="zone-cell"
              :class="zone.riskLevel"
              :title="`${zone.name}: ${zone.incidents} incidentes`"
            >
              <div class="zone-name">{{ zone.name }}</div>
              <div class="zone-count">{{ zone.incidents }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="activity-section">
      <div class="activity-card">
        <h3>🔄 Actividad Reciente</h3>
        <div class="activity-list">
          <div v-for="activity in recentActivities" :key="activity.id" class="activity-item">
            <div class="activity-icon" :class="activity.type">
              {{ activity.icon }}
            </div>
            <div class="activity-content">
              <div class="activity-title">{{ activity.title }}</div>
              <div class="activity-time">{{ activity.time }}</div>
            </div>
          </div>
        </div>
      </div>

      <div class="insights-card">
        <h3>🧠 Insights y Tendencias</h3>
        <div class="insights-list">
          <div v-for="insight in intelligentInsights" :key="insight.id" class="insight-item">
            <div class="insight-icon" :class="insight.type">{{ insight.icon }}</div>
            <div class="insight-content">
              <div class="insight-title">{{ insight.title }}</div>
              <div class="insight-description">{{ insight.description }}</div>
              <div class="insight-confidence">Confianza: {{ insight.confidence }}%</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  Title,
  Tooltip,
  Legend,
  ArcElement,
  RadialLinearScale,
} from 'chart.js'
import { Line, Doughnut, Bar, Radar } from 'vue-chartjs'

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  Title,
  Tooltip,
  Legend,
  ArcElement,
  RadialLinearScale,
)

interface Incident {
  id: number
  title: string
  description: string
  category: string
  status: 'abierto' | 'en_revision' | 'cerrado'
  priority: 'alta' | 'media' | 'baja'
  route: string
  reporter: string
  dateReported: string
  lastUpdate: string
  coordinates?: {
    lat: number
    lng: number
  }
}

// Data loading
const incidents = ref<Incident[]>([])

const loadIncidents = () => {
  const stored = localStorage.getItem('incidents')
  if (stored) {
    incidents.value = JSON.parse(stored)
  } else {
    // Generar datos de muestra si no hay datos guardados
    const sampleIncidents: Incident[] = [
      {
        id: 1,
        title: 'Avistamiento de oso en Sendero El Bosque',
        description:
          'Un grupo de visitantes reportó el avistamiento de un oso cerca del área de descanso principal del sendero. El animal parecía estar buscando comida.',
        category: 'Fauna Salvaje',
        status: 'abierto',
        priority: 'alta',
        route: 'Sendero El Bosque',
        reporter: 'Juan Pérez',
        dateReported: '2024-09-25T14:30:00',
        lastUpdate: '2024-09-25T14:30:00',
      },
      {
        id: 2,
        title: 'Árbol caído bloqueando el camino',
        description:
          'Un árbol de gran tamaño cayó durante la tormenta de anoche y está bloqueando completamente el acceso al mirador principal.',
        category: 'Senderos',
        status: 'en_revision',
        priority: 'alta',
        route: 'Ruta del Mirador',
        reporter: 'María González',
        dateReported: '2024-09-24T08:15:00',
        lastUpdate: '2024-09-25T10:00:00',
      },
      {
        id: 3,
        title: 'Visitante perdido en zona norte',
        description:
          'Un excursionista no regresó a la hora prevista. Último avistamiento fue cerca del lago a las 15:00.',
        category: 'Emergencia',
        status: 'cerrado',
        priority: 'alta',
        route: 'Ruta Norte',
        reporter: 'Carlos Ruiz',
        dateReported: '2024-09-23T18:00:00',
        lastUpdate: '2024-09-24T12:00:00',
      },
      {
        id: 4,
        title: 'Tormenta eléctrica intensa',
        description:
          'Se registraron rayos frecuentes y vientos fuertes. Se recomienda cerrar temporalmente las rutas expuestas.',
        category: 'Clima',
        status: 'cerrado',
        priority: 'media',
        route: 'Ruta del Mirador',
        reporter: 'Ana López',
        dateReported: '2024-09-23T16:45:00',
        lastUpdate: '2024-09-24T09:00:00',
      },
      {
        id: 5,
        title: 'Puente de madera dañado',
        description:
          'Se observaron grietas en la estructura del puente que cruza el río. Requiere inspección y posible reparación.',
        category: 'Infraestructura',
        status: 'en_revision',
        priority: 'media',
        route: 'Camino al Lago',
        reporter: 'Pedro Morales',
        dateReported: '2024-09-22T11:20:00',
        lastUpdate: '2024-09-25T08:30:00',
      },
      {
        id: 6,
        title: 'Grupo de visitantes haciendo ruido excesivo',
        description:
          'Un grupo grande está perturbando la tranquilidad del parque con música alta y comportamiento inadecuado.',
        category: 'Visitantes',
        status: 'cerrado',
        priority: 'baja',
        route: 'Área de Picnic',
        reporter: 'Laura Jiménez',
        dateReported: '2024-09-22T15:30:00',
        lastUpdate: '2024-09-22T17:00:00',
      },
      {
        id: 7,
        title: 'Serpiente venenosa en sendero principal',
        description:
          'Se avistó una serpiente coral cerca del inicio del sendero de la cascada. Area acordonada temporalmente.',
        category: 'Fauna Salvaje',
        status: 'abierto',
        priority: 'alta',
        route: 'Sendero de la Cascada',
        reporter: 'Miguel Torres',
        dateReported: '2024-09-25T09:15:00',
        lastUpdate: '2024-09-25T09:15:00',
      },
      {
        id: 8,
        title: 'Erosión severa en el camino',
        description:
          'Las lluvias han causado erosión significativa en una sección de 50 metros del camino, creando zanjas peligrosas.',
        category: 'Senderos',
        status: 'abierto',
        priority: 'media',
        route: 'Ruta Norte',
        reporter: 'Sofia Vargas',
        dateReported: '2024-09-24T12:45:00',
        lastUpdate: '2024-09-24T12:45:00',
      },
      {
        id: 9,
        title: 'Granizada dañó señalización',
        description:
          'El granizo de ayer dañó varias señales informativas y de seguridad a lo largo de la ruta principal.',
        category: 'Clima',
        status: 'en_revision',
        priority: 'baja',
        route: 'Entrada Principal',
        reporter: 'Roberto Silva',
        dateReported: '2024-09-24T07:30:00',
        lastUpdate: '2024-09-25T11:15:00',
      },
      {
        id: 10,
        title: 'Baño público fuera de servicio',
        description:
          'El sistema de plomería del baño principal se averió. Requiere reparación urgente para los visitantes.',
        category: 'Infraestructura',
        status: 'abierto',
        priority: 'media',
        route: 'Zona de Camping',
        reporter: 'Diana Castro',
        dateReported: '2024-09-25T06:00:00',
        lastUpdate: '2024-09-25T06:00:00',
      },
      {
        id: 11,
        title: 'Manada de jabalíes cerca del camping',
        description:
          'Una familia de jabalíes se ha establecido cerca del área de camping, causando preocupación entre los visitantes.',
        category: 'Fauna Salvaje',
        status: 'en_revision',
        priority: 'alta',
        route: 'Zona de Camping',
        reporter: 'Fernando Ramos',
        dateReported: '2024-09-23T19:45:00',
        lastUpdate: '2024-09-24T14:20:00',
      },
      {
        id: 12,
        title: 'Basura abandonada en mirador',
        description:
          'Los visitantes dejaron gran cantidad de basura en el área del mirador principal, afectando el ecosistema.',
        category: 'Visitantes',
        status: 'cerrado',
        priority: 'media',
        route: 'Mirador Principal',
        reporter: 'Elena Herrera',
        dateReported: '2024-09-21T17:00:00',
        lastUpdate: '2024-09-22T08:00:00',
      },
      {
        id: 13,
        title: 'Niebla densa reduce visibilidad',
        description:
          'Condiciones de niebla muy densa han reducido la visibilidad a menos de 10 metros en las rutas altas.',
        category: 'Clima',
        status: 'cerrado',
        priority: 'media',
        route: 'Ruta del Mirador',
        reporter: 'Alejandro Cruz',
        dateReported: '2024-09-20T06:30:00',
        lastUpdate: '2024-09-20T14:00:00',
      },
      {
        id: 14,
        title: 'Escalador herido en zona rocosa',
        description:
          'Un escalador sufrió una caída y requirió asistencia médica. Ya fue evacuado pero la zona sigue siendo peligrosa.',
        category: 'Emergencia',
        status: 'cerrado',
        priority: 'alta',
        route: 'Zona Este',
        reporter: 'Claudia Mendoza',
        dateReported: '2024-09-19T14:20:00',
        lastUpdate: '2024-09-20T10:00:00',
      },
      {
        id: 15,
        title: 'Sistema de agua potable contaminado',
        description:
          'Se detectaron niveles anómalos de bacterias en el suministro de agua potable de la estación de servicios.',
        category: 'Infraestructura',
        status: 'en_revision',
        priority: 'alta',
        route: 'Zona Central',
        reporter: 'Raúl Figueroa',
        dateReported: '2024-09-24T10:00:00',
        lastUpdate: '2024-09-25T13:45:00',
      },
      {
        id: 16,
        title: 'Incendio forestal controlado',
        description:
          'Se detectó un pequeño incendio que fue controlado rápidamente. Area en monitoreo para prevenir rebrotes.',
        category: 'Emergencia',
        status: 'cerrado',
        priority: 'alta',
        route: 'Zona Oeste',
        reporter: 'Valeria Soto',
        dateReported: '2024-09-18T15:30:00',
        lastUpdate: '2024-09-19T18:00:00',
      },
      {
        id: 17,
        title: 'Visitantes acampando ilegalmente',
        description:
          'Se encontró un campamento no autorizado en área protegida. Los visitantes fueron reubicados.',
        category: 'Visitantes',
        status: 'cerrado',
        priority: 'baja',
        route: 'Zona Sur',
        reporter: 'Gonzalo Paredes',
        dateReported: '2024-09-21T08:45:00',
        lastUpdate: '2024-09-21T16:30:00',
      },
      {
        id: 18,
        title: 'Deslizamiento menor en sendero',
        description:
          'Un pequeño deslizamiento de tierra bloqueó parcialmente el sendero después de las lluvias intensas.',
        category: 'Senderos',
        status: 'abierto',
        priority: 'baja',
        route: 'Sendero de la Cascada',
        reporter: 'Mónica Aguilar',
        dateReported: '2024-09-25T11:00:00',
        lastUpdate: '2024-09-25T11:00:00',
      },
      {
        id: 19,
        title: 'Temperaturas extremas registradas',
        description:
          'Se registraron temperaturas superiores a 40°C durante la tarde, superando los niveles seguros para caminatas.',
        category: 'Clima',
        status: 'cerrado',
        priority: 'media',
        route: 'Todas las rutas',
        reporter: 'Javier Montoya',
        dateReported: '2024-09-17T16:00:00',
        lastUpdate: '2024-09-18T06:00:00',
      },
      {
        id: 20,
        title: 'Torre de observación dañada',
        description:
          'Fuertes vientos dañaron la estructura de la torre de observación. Escaleras y plataforma necesitan reparación.',
        category: 'Infraestructura',
        status: 'abierto',
        priority: 'media',
        route: 'Mirador Principal',
        reporter: 'Beatriz Navarro',
        dateReported: '2024-09-25T07:20:00',
        lastUpdate: '2024-09-25T07:20:00',
      },
    ]

    incidents.value = sampleIncidents
    localStorage.setItem('incidents', JSON.stringify(sampleIncidents))
  }
}

// Computed statistics
const totalIncidents = computed(() => incidents.value.length)
const openIncidents = computed(() => incidents.value.filter((i) => i.status === 'abierto').length)
const inReviewIncidents = computed(
  () => incidents.value.filter((i) => i.status === 'en_revision').length,
)
const closedIncidents = computed(() => incidents.value.filter((i) => i.status === 'cerrado').length)

const categoryStats = computed(() => {
  const stats: Record<string, number> = {}
  incidents.value.forEach((incident) => {
    stats[incident.category] = (stats[incident.category] || 0) + 1
  })
  return stats
})

// Utility functions
const getPercentage = (value: number, total: number): string => {
  if (total === 0) return '0'
  return Math.round((value / total) * 100).toString()
}

// New functionality variables
const selectedTimeRange = ref('monthly')
const selectedDate = ref(new Date().toISOString().split('T')[0])
const searchKeyword = ref('')
const searchResults = ref<Incident[]>([])
const routeAnalysisType = ref('total')

// Time range and search functions
const updateTimeRange = () => {
  // Filter incidents based on selected time range and date
  loadIncidents()
}

const searchIncidents = () => {
  if (!searchKeyword.value.trim()) {
    searchResults.value = []
    return
  }

  searchResults.value = incidents.value.filter(
    (incident) =>
      incident.description.toLowerCase().includes(searchKeyword.value.toLowerCase()) ||
      incident.title.toLowerCase().includes(searchKeyword.value.toLowerCase()),
  )
}

// Export functions
const exportToPDF = async () => {
  try {
    const { jsPDF } = await import('jspdf')
    const html2canvas = (await import('html2canvas')).default

    const pdf = new jsPDF('p', 'mm', 'a4')
    const pageWidth = pdf.internal.pageSize.getWidth()
    const pageHeight = pdf.internal.pageSize.getHeight()

    // Title
    pdf.setFontSize(20)
    pdf.text('Reporte de Incidentes - Parques Nacionales', pageWidth / 2, 20, { align: 'center' })

    // Date
    pdf.setFontSize(12)
    pdf.text(`Fecha: ${new Date().toLocaleDateString('es-ES')}`, 20, 35)

    // Summary statistics
    pdf.setFontSize(14)
    pdf.text('Resumen Ejecutivo:', 20, 50)
    pdf.setFontSize(11)
    pdf.text(`Total de Incidentes: ${totalIncidents.value}`, 25, 60)
    pdf.text(`Incidentes Abiertos: ${openIncidents.value}`, 25, 70)
    pdf.text(`En Revisión: ${inReviewIncidents.value}`, 25, 80)
    pdf.text(`Cerrados: ${closedIncidents.value}`, 25, 90)

    // Category breakdown
    pdf.setFontSize(14)
    pdf.text('Incidentes por Categoría:', 20, 110)
    let yPos = 120
    Object.entries(categoryStats.value).forEach(([category, count]) => {
      pdf.setFontSize(11)
      pdf.text(`${category}: ${count} (${getPercentage(count, totalIncidents.value)}%)`, 25, yPos)
      yPos += 10
    })

    pdf.save('reporte-incidentes.pdf')
  } catch (error) {
    console.error('Error generating PDF:', error)
  }
}

const exportToExcel = async () => {
  try {
    const XLSX = await import('xlsx')

    // Create workbook
    const wb = XLSX.utils.book_new()

    // Summary sheet
    const summaryData = [
      ['Métrica', 'Valor', 'Porcentaje'],
      ['Total de Incidentes', totalIncidents.value, '100%'],
      [
        'Incidentes Abiertos',
        openIncidents.value,
        `${getPercentage(openIncidents.value, totalIncidents.value)}%`,
      ],
      [
        'En Revisión',
        inReviewIncidents.value,
        `${getPercentage(inReviewIncidents.value, totalIncidents.value)}%`,
      ],
      [
        'Cerrados',
        closedIncidents.value,
        `${getPercentage(closedIncidents.value, totalIncidents.value)}%`,
      ],
    ]
    const summaryWs = XLSX.utils.aoa_to_sheet(summaryData)
    XLSX.utils.book_append_sheet(wb, summaryWs, 'Resumen')

    // Category breakdown sheet
    const categoryData = [['Categoría', 'Cantidad', 'Porcentaje']]
    Object.entries(categoryStats.value).forEach(([category, count]) => {
      categoryData.push([category, count, `${getPercentage(count, totalIncidents.value)}%`])
    })
    const categoryWs = XLSX.utils.aoa_to_sheet(categoryData)
    XLSX.utils.book_append_sheet(wb, categoryWs, 'Por Categoría')

    // Route analysis sheet
    const routeData = [['Ruta', 'Incidentes', 'Nivel de Peligro']]
    routeRanking.value.forEach((route) => {
      routeData.push([route.name, route.incidents, route.dangerText])
    })
    const routeWs = XLSX.utils.aoa_to_sheet(routeData)
    XLSX.utils.book_append_sheet(wb, routeWs, 'Por Rutas')

    // Save file
    XLSX.writeFile(wb, 'reporte-incidentes.xlsx')
  } catch (error) {
    console.error('Error generating Excel:', error)
  }
}

// Table sorting functions
const sortCategoryTable = () => {
  console.log('Sorting category table...')
}

const sortStatusTable = () => {
  console.log('Sorting status table...')
}

// Advanced computed data for tables
const categoryTableData = computed(() => {
  const categories = [
    'Fauna Salvaje',
    'Senderos',
    'Clima',
    'Infraestructura',
    'Visitantes',
    'Emergencia',
  ]
  const colors = ['#e74c3c', '#3498db', '#2ecc71', '#f39c12', '#9b59b6', '#e67e22']

  return categories.map((category, index) => {
    const categoryIncidents = incidents.value.filter((i) => i.category === category)
    const total = categoryIncidents.length
    const open = categoryIncidents.filter((i) => i.status === 'abierto').length
    const inReview = categoryIncidents.filter((i) => i.status === 'en_revision').length
    const closed = categoryIncidents.filter((i) => i.status === 'cerrado').length

    return {
      name: category,
      color: colors[index],
      total,
      open,
      inReview,
      closed,
      percentage: getPercentage(total, totalIncidents.value),
      avgResolutionTime: `${Math.floor(Math.random() * 72) + 12}h`, // Mock data
    }
  })
})

const statusTableData = computed(() => {
  const statuses = [
    { name: 'abierto', displayName: 'Abierto', cssClass: 'status-open' },
    { name: 'en_revision', displayName: 'En Revisión', cssClass: 'status-review' },
    { name: 'cerrado', displayName: 'Cerrado', cssClass: 'status-closed' },
  ]

  return statuses.map((status) => {
    const statusIncidents = incidents.value.filter((i) => i.status === status.name)
    const count = statusIncidents.length
    const highPriority = statusIncidents.filter((i) => i.priority === 'alta').length
    const mediumPriority = statusIncidents.filter((i) => i.priority === 'media').length
    const lowPriority = statusIncidents.filter((i) => i.priority === 'baja').length

    return {
      name: status.name,
      displayName: status.displayName,
      cssClass: status.cssClass,
      count,
      percentage: getPercentage(count, totalIncidents.value),
      avgTimeInStatus: `${Math.floor(Math.random() * 48) + 6}h`, // Mock data
      trend: Math.random() > 0.5 ? 'up' : 'down',
      trendText: Math.random() > 0.5 ? '↗️ +12%' : '↘️ -8%',
      highPriority,
      mediumPriority,
      lowPriority,
    }
  })
})

// Route analysis data
const routeRanking = computed(() => {
  const routes = [
    'Sendero El Bosque',
    'Ruta del Mirador',
    'Camino al Lago',
    'Sendero de la Cascada',
    'Ruta Norte',
  ]
  const routeStats: Record<string, number> = {}

  incidents.value.forEach((incident) => {
    routeStats[incident.route] = (routeStats[incident.route] || 0) + 1
  })

  return routes
    .map((route) => {
      const incidents = routeStats[route] || Math.floor(Math.random() * 25) + 5
      const percentage = getPercentage(incidents, totalIncidents.value)
      const dangerLevel =
        incidents > 20 ? 'high-danger' : incidents > 10 ? 'medium-danger' : 'low-danger'
      const dangerText =
        incidents > 20 ? 'Alto Riesgo' : incidents > 10 ? 'Riesgo Medio' : 'Bajo Riesgo'

      return {
        name: route,
        incidents,
        percentage,
        dangerLevel,
        dangerText,
      }
    })
    .sort((a, b) => b.incidents - a.incidents)
})

const zoneData = computed(() => {
  const zones = [
    'Zona Norte',
    'Zona Sur',
    'Zona Este',
    'Zona Oeste',
    'Zona Central',
    'Entrada Principal',
    'Zona de Camping',
    'Área de Picnic',
    'Mirador Principal',
  ]

  return zones.map((zone) => {
    const incidents = Math.floor(Math.random() * 30) + 1
    const riskLevel = incidents > 20 ? 'high-risk' : incidents > 10 ? 'medium-risk' : 'low-risk'

    return {
      name: zone,
      incidents,
      riskLevel,
    }
  })
})

// Chart type selectors
const statusChartType = ref('doughnut')
const categoryChartType = ref('bar')
const trendPeriod = ref('monthly')
const comparisonYear = ref('2024')

// Advanced chart data
const temporalTrendData = computed(() => {
  const periods = {
    daily: {
      labels: ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sáb', 'Dom'],
      data: [8, 12, 6, 15, 18, 25, 22],
    },
    weekly: { labels: ['Sem 1', 'Sem 2', 'Sem 3', 'Sem 4'], data: [45, 52, 38, 61] },
    monthly: { labels: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun'], data: [12, 19, 8, 15, 22, 18] },
    yearly: { labels: ['2020', '2021', '2022', '2023', '2024'], data: [89, 125, 156, 201, 187] },
  }

  const period = periods[trendPeriod.value as keyof typeof periods]

  return {
    labels: period.labels,
    datasets: [
      {
        label: 'Incidentes Reportados',
        data: period.data,
        borderColor: '#34a853',
        backgroundColor: 'rgba(52, 168, 83, 0.15)',
        borderWidth: 3,
        fill: true,
        tension: 0.4,
      },
    ],
  }
})

const hourlyIncidentsData = ref({
  labels: [
    '00-02',
    '02-04',
    '04-06',
    '06-08',
    '08-10',
    '10-12',
    '12-14',
    '14-16',
    '16-18',
    '18-20',
    '20-22',
    '22-00',
  ],
  datasets: [
    {
      label: 'Incidentes por Hora',
      data: [2, 1, 1, 4, 8, 15, 18, 22, 19, 12, 8, 4],
      backgroundColor: [
        '#3498db',
        '#2ecc71',
        '#f39c12',
        '#e74c3c',
        '#9b59b6',
        '#1abc9c',
        '#34495e',
        '#e67e22',
        '#95a5a6',
        '#27ae60',
        '#8e44ad',
        '#2980b9',
      ],
      borderRadius: 6,
    },
  ],
})

const weeklyPatternData = ref({
  labels: ['Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes', 'Sábado', 'Domingo'],
  datasets: [
    {
      label: 'Promedio de Incidentes',
      data: [8, 12, 9, 14, 16, 28, 24],
      borderColor: '#e74c3c',
      backgroundColor: 'rgba(231, 76, 60, 0.1)',
      borderWidth: 3,
      fill: true,
      tension: 0.4,
    },
    {
      label: 'Incidentes Críticos',
      data: [2, 3, 1, 4, 5, 12, 9],
      borderColor: '#f39c12',
      backgroundColor: 'rgba(243, 156, 18, 0.1)',
      borderWidth: 2,
      fill: false,
    },
  ],
})

const seasonalData = ref({
  labels: ['Primavera', 'Verano', 'Otoño', 'Invierno'],
  datasets: [
    {
      data: [28, 45, 32, 18],
      backgroundColor: ['#2ecc71', '#e74c3c', '#f39c12', '#3498db'],
      borderWidth: 0,
    },
  ],
})

const resolutionTimeData = ref({
  labels: ['< 2h', '2-6h', '6-12h', '12-24h', '1-3 días', '> 3 días'],
  datasets: [
    {
      label: 'Cantidad de Incidentes',
      data: [15, 28, 22, 18, 12, 8],
      backgroundColor: ['#27ae60', '#2ecc71', '#f39c12', '#e67e22', '#e74c3c', '#8e44ad'],
      borderRadius: 6,
    },
  ],
})

const monthlyComparisonData = computed(() => ({
  labels: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct', 'Nov', 'Dic'],
  datasets: [
    {
      label: `${comparisonYear.value}`,
      data: [12, 19, 8, 15, 22, 18, 25, 28, 20, 16, 14, 21],
      borderColor: '#3498db',
      backgroundColor: 'rgba(52, 152, 219, 0.1)',
      borderWidth: 3,
      fill: true,
    },
    {
      label: `${parseInt(comparisonYear.value) - 1}`,
      data: [8, 14, 11, 12, 18, 15, 21, 24, 17, 13, 11, 18],
      borderColor: '#95a5a6',
      backgroundColor: 'rgba(149, 165, 166, 0.1)',
      borderWidth: 2,
      fill: false,
    },
  ],
}))

const efficiencyData = ref({
  labels: ['Enero', 'Febrero', 'Marzo', 'Abril', 'Mayo', 'Junio'],
  datasets: [
    {
      label: 'Tiempo Promedio (horas)',
      data: [36, 32, 28, 24, 22, 18],
      backgroundColor: '#27ae60',
      borderRadius: 6,
    },
    {
      label: 'Meta (horas)',
      data: [24, 24, 24, 24, 24, 24],
      backgroundColor: '#e67e22',
      borderRadius: 6,
    },
  ],
})

const resolutionStatusData = ref({
  labels: ['Resueltos', 'En Progreso', 'Pendientes', 'Escalados'],
  datasets: [
    {
      data: [65, 20, 12, 3],
      backgroundColor: ['#27ae60', '#3498db', '#f39c12', '#e74c3c'],
      borderWidth: 0,
    },
  ],
})

const trendPredictionData = ref({
  labels: ['Día 1', 'Día 2', 'Día 3', 'Día 4', 'Día 5', 'Día 6', 'Día 7'],
  datasets: [
    {
      label: 'Incidentes Actuales',
      data: [12, 15, 8, 18, 22, 16, 14],
      borderColor: '#3498db',
      backgroundColor: 'rgba(52, 152, 219, 0.2)',
      borderWidth: 3,
      fill: true,
    },
    {
      label: 'Predicción IA',
      data: [null, null, null, null, null, 18, 21],
      borderColor: '#e74c3c',
      backgroundColor: 'rgba(231, 76, 60, 0.1)',
      borderWidth: 2,
      borderDash: [5, 5],
      fill: false,
    },
  ],
})

// Intelligent insights
const intelligentInsights = computed(() => [
  {
    id: 1,
    title: 'Patrón Detectado: Incremento en Fines de Semana',
    description: 'Los incidentes aumentan 340% durante fines de semana largos',
    confidence: 92,
    type: 'trend',
    icon: '📈',
  },
  {
    id: 2,
    title: 'Ruta Crítica Identificada',
    description: 'El Sendero El Bosque concentra el 28% de todos los incidentes',
    confidence: 87,
    type: 'location',
    icon: '🗺️',
  },
  {
    id: 3,
    title: 'Horario de Mayor Riesgo',
    description: 'Entre 2PM-4PM se registran el 45% más incidentes por temperatura',
    confidence: 94,
    type: 'time',
    icon: '🕐',
  },
  {
    id: 4,
    title: 'Eficiencia de Resolución',
    description: 'Tiempo promedio de cierre ha mejorado 23% este mes',
    confidence: 78,
    type: 'performance',
    icon: '⚡',
  },
])

// Chart data
const statusChartData = computed(() => ({
  labels: ['Abiertos', 'En Revisión', 'Cerrados'],
  datasets: [
    {
      data: [openIncidents.value, inReviewIncidents.value, closedIncidents.value],
      backgroundColor: ['#e74c3c', '#3498db', '#2ecc71'],
      borderWidth: 0,
    },
  ],
}))

const categoryChartData = computed(() => {
  const categories = Object.keys(categoryStats.value)
  const counts = Object.values(categoryStats.value)

  return {
    labels: categories,
    datasets: [
      {
        label: 'Incidentes por Categoría',
        data: counts,
        backgroundColor: '#34a853',
        borderRadius: 8,
      },
    ],
  }
})

const monthlyTrendData = ref({
  labels: ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun'],
  datasets: [
    {
      label: 'Incidentes Reportados',
      data: [12, 19, 8, 15, 22, 18],
      borderColor: '#34a853',
      backgroundColor: 'rgba(52, 168, 83, 0.1)',
      borderWidth: 3,
      fill: true,
      tension: 0.4,
    },
  ],
})

const routeData = computed(() => {
  const routeStats: Record<string, number> = {}
  incidents.value.forEach((incident) => {
    routeStats[incident.route] = (routeStats[incident.route] || 0) + 1
  })

  const routes = Object.keys(routeStats).slice(0, 5) // Top 5 routes
  const counts = routes.map((route) => routeStats[route])

  return {
    labels: routes,
    datasets: [
      {
        label: 'Incidentes por Ruta',
        data: counts,
        backgroundColor: 'rgba(52, 168, 83, 0.2)',
        borderColor: '#34a853',
        borderWidth: 2,
      },
    ],
  }
})

// Chart options
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false,
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const doughnutOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'bottom' as const,
    },
  },
}

const barOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false,
    },
  },
  scales: {
    y: {
      beginAtZero: true,
    },
  },
}

const radarOptions = {
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    r: {
      beginAtZero: true,
    },
  },
}

// Advanced chart options
const hourlyOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false,
    },
    tooltip: {
      callbacks: {
        label: (context: any) => `${context.parsed.y} incidentes`,
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const weeklyOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'top' as const,
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const seasonalOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'bottom' as const,
    },
    tooltip: {
      callbacks: {
        label: (context: any) => `${context.label}: ${context.parsed}% del año`,
      },
    },
  },
}

const resolutionOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false,
    },
    tooltip: {
      callbacks: {
        label: (context: any) => `${context.parsed.y} incidentes resueltos`,
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const comparisonOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'top' as const,
    },
    tooltip: {
      mode: 'index' as const,
      intersect: false,
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const efficiencyOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'top' as const,
    },
    tooltip: {
      callbacks: {
        label: (context: any) => `${context.dataset.label}: ${context.parsed.y}h`,
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
      title: {
        display: true,
        text: 'Horas',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

const resolutionStatusOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'bottom' as const,
    },
    tooltip: {
      callbacks: {
        label: (context: any) => `${context.label}: ${context.parsed}%`,
      },
    },
  },
}

const predictionOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'top' as const,
    },
    tooltip: {
      mode: 'index' as const,
      intersect: false,
      callbacks: {
        label: (context: any) => {
          if (context.dataset.label === 'Predicción IA') {
            return `${context.dataset.label}: ${context.parsed.y} (estimado)`
          }
          return `${context.dataset.label}: ${context.parsed.y}`
        },
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(0, 0, 0, 0.1)',
      },
    },
    x: {
      grid: {
        display: false,
      },
    },
  },
}

// Activity data
const recentActivities = ref([
  {
    id: 1,
    title: 'Estado cambiado a "Cerrado"',
    time: 'Hace 5 minutos',
    icon: '✅',
    type: 'status-change',
  },
  {
    id: 2,
    title: 'Nuevo incidente reportado',
    time: 'Hace 15 minutos',
    icon: '🚨',
    type: 'new-incident',
  },
  {
    id: 3,
    title: 'Estado cambiado a "En Revisión"',
    time: 'Hace 1 hora',
    icon: '🔍',
    type: 'status-change',
  },
  {
    id: 4,
    title: 'Incidente actualizado',
    time: 'Hace 2 horas',
    icon: '📝',
    type: 'update',
  },
])

onMounted(() => {
  loadIncidents()
  // Update data periodically
  setInterval(loadIncidents, 30000) // Update every 30 seconds
})
</script>

<style scoped>
.dashboard-container {
  width: 100vw;
  max-width: 100vw;
  margin: 0;
  padding: 2rem 5%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  box-sizing: border-box;
  overflow-x: hidden;
}

.header {
  text-align: center;
  margin-bottom: 2rem;
  color: white;
}

.header h1 {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.header p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.metrics-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.metric-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  border-left: 4px solid;
  transition: transform 0.3s ease;
}

.metric-card:hover {
  transform: translateY(-5px);
}

.metric-card.primary {
  border-left-color: #3498db;
}
.metric-card.success {
  border-left-color: #2ecc71;
}
.metric-card.warning {
  border-left-color: #f39c12;
}
.metric-card.info {
  border-left-color: #9b59b6;
}

.metric-icon {
  font-size: 2.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background: rgba(52, 168, 83, 0.1);
}

.metric-content {
  flex: 1;
}

.metric-value {
  font-size: 2rem;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 0.25rem;
}

.metric-label {
  color: #7f8c8d;
  font-weight: 500;
  margin-bottom: 0.25rem;
}

.metric-change {
  font-size: 0.9rem;
  font-weight: 600;
  color: #7f8c8d;
}

.metric-change.positive {
  color: #27ae60;
}

.metric-change.negative {
  color: #e74c3c;
}

.metric-change.neutral {
  color: #7f8c8d;
}

.charts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.chart-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.chart-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.chart-header h3 {
  color: #2c3e50;
  font-size: 1.25rem;
  margin: 0;
}

.chart-container {
  height: 300px;
  position: relative;
}

.activity-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.activity-card,
.top-incidents-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.activity-card h3,
.top-incidents-card h3 {
  color: #2c3e50;
  margin-bottom: 1rem;
  font-size: 1.25rem;
}

.activity-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.activity-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem;
  background: #f8f9fa;
  border-radius: 10px;
  transition: background 0.3s ease;
}

.activity-item:hover {
  background: #e9ecef;
}

.activity-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
}

.activity-icon.status-change {
  background: rgba(52, 168, 83, 0.2);
}
.activity-icon.new-incident {
  background: rgba(231, 76, 60, 0.2);
}
.activity-icon.update {
  background: rgba(52, 152, 219, 0.2);
}

.activity-content {
  flex: 1;
}

.activity-title {
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 0.25rem;
}

.activity-time {
  font-size: 0.9rem;
  color: #7f8c8d;
}

.category-stats {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.category-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem;
  background: #f8f9fa;
  border-radius: 10px;
  transition: background 0.3s ease;
}

.category-item:hover {
  background: #e9ecef;
}

.category-name {
  flex: 1;
  font-weight: 600;
  color: #2c3e50;
}

.category-count {
  font-size: 0.9rem;
  color: #7f8c8d;
}

.category-percentage {
  font-weight: 600;
  color: #34a853;
  min-width: 50px;
  text-align: right;
}

@media (max-width: 768px) {
  .dashboard-container {
    padding: 1rem 3%;
  }

  .metrics-grid {
    grid-template-columns: 1fr;
  }

  .charts-grid {
    grid-template-columns: 1fr;
  }

  .activity-section {
    grid-template-columns: 1fr;
  }

  .chart-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 1rem;
  }

  .filters-section {
    flex-direction: column;
    gap: 1rem;
  }

  .tables-section {
    overflow-x: auto;
  }

  .summary-table {
    font-size: 0.8rem;
  }

  .routes-section {
    grid-template-columns: 1fr;
  }

  .map-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

/* New Styles for Advanced Dashboard */

/* Filters Section */
.filters-section {
  display: flex;
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.filter-card,
.search-card,
.export-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  flex: 1;
}

.filter-card h3,
.search-card h3,
.export-card h3 {
  color: #2c3e50;
  margin-bottom: 1rem;
  font-size: 1.1rem;
}

.time-filters {
  display: flex;
  gap: 1rem;
  align-items: center;
}

.time-selector,
.date-picker,
.analysis-selector {
  padding: 0.75rem;
  border: 2px solid #e0e6ed;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.time-selector:focus,
.date-picker:focus,
.analysis-selector:focus {
  border-color: #34a853;
  outline: none;
}

.search-container {
  position: relative;
}

.search-input {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e0e6ed;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.search-input:focus {
  border-color: #34a853;
  outline: none;
}

.search-results {
  margin-top: 0.5rem;
  padding: 0.5rem;
  background: #f8f9fa;
  border-radius: 6px;
  font-size: 0.9rem;
  color: #666;
}

.export-buttons {
  display: flex;
  gap: 1rem;
}

.export-btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
  flex: 1;
}

.pdf-btn {
  background: #e74c3c;
  color: white;
}

.pdf-btn:hover {
  background: #c0392b;
  transform: translateY(-2px);
}

.excel-btn {
  background: #27ae60;
  color: white;
}

.excel-btn:hover {
  background: #219a52;
  transform: translateY(-2px);
}

/* Tables Section */
.tables-section {
  display: grid;
  gap: 2rem;
  margin-bottom: 2rem;
}

.table-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.table-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.table-header h3 {
  color: #2c3e50;
  margin: 0;
  font-size: 1.25rem;
}

.sort-btn {
  padding: 0.5rem 1rem;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: background 0.3s ease;
}

.sort-btn:hover {
  background: #2980b9;
}

.table-container {
  overflow-x: auto;
}

.summary-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1rem;
}

.summary-table th {
  background: #f8f9fa;
  padding: 1rem 0.75rem;
  text-align: left;
  font-weight: 600;
  color: #2c3e50;
  border-bottom: 2px solid #e9ecef;
}

.summary-table td {
  padding: 0.75rem;
  border-bottom: 1px solid #e9ecef;
}

.category-cell .category-badge {
  padding: 0.25rem 0.75rem;
  border-radius: 15px;
  color: white;
  font-size: 0.85rem;
  font-weight: 500;
}

.number-cell,
.percentage-cell,
.time-cell {
  font-weight: 600;
  text-align: center;
}

.status-cell.open {
  color: #e74c3c;
  font-weight: 600;
}

.status-cell.review {
  color: #3498db;
  font-weight: 600;
}

.status-cell.closed {
  color: #27ae60;
  font-weight: 600;
}

.status-badge {
  padding: 0.25rem 0.5rem;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: 600;
}

.status-badge.status-open {
  background: rgba(231, 76, 60, 0.1);
  color: #e74c3c;
}

.status-badge.status-review {
  background: rgba(52, 152, 219, 0.1);
  color: #3498db;
}

.status-badge.status-closed {
  background: rgba(39, 174, 96, 0.1);
  color: #27ae60;
}

.trend {
  font-size: 0.9rem;
  font-weight: 600;
}

.trend.up {
  color: #27ae60;
}

.trend.down {
  color: #e74c3c;
}

.priority-cell {
  text-align: center;
  font-weight: 600;
}

.priority-cell.high {
  color: #e74c3c;
}

.priority-cell.medium {
  color: #f39c12;
}

.priority-cell.low {
  color: #95a5a6;
}

/* Routes Section */
.routes-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  margin-bottom: 2rem;
}

.route-card,
.route-map-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.route-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.route-header h3 {
  color: #2c3e50;
  margin: 0;
  font-size: 1.25rem;
}

.route-ranking {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.route-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  background: #f8f9fa;
  border-radius: 10px;
  transition: all 0.3s ease;
}

.route-item:hover {
  background: #e9ecef;
  transform: translateY(-2px);
}

.route-rank {
  width: 35px;
  height: 35px;
  background: #34a853;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 1.1rem;
}

.route-info {
  flex: 1;
}

.route-name {
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 0.25rem;
}

.route-stats {
  display: flex;
  gap: 1rem;
  font-size: 0.9rem;
  color: #7f8c8d;
}

.route-indicators {
  text-align: right;
}

.danger-level {
  padding: 0.25rem 0.75rem;
  border-radius: 15px;
  font-size: 0.8rem;
  font-weight: 600;
}

.danger-level.high-danger {
  background: rgba(231, 76, 60, 0.1);
  color: #e74c3c;
}

.danger-level.medium-danger {
  background: rgba(243, 156, 18, 0.1);
  color: #f39c12;
}

.danger-level.low-danger {
  background: rgba(39, 174, 96, 0.1);
  color: #27ae60;
}

/* Map Grid */
.route-map {
  height: 400px;
  overflow: hidden;
}

.map-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.5rem;
  height: 100%;
}

.zone-cell {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 8px;
  padding: 1rem;
  transition: all 0.3s ease;
  cursor: pointer;
}

.zone-cell:hover {
  transform: scale(1.05);
}

.zone-cell.high-risk {
  background: linear-gradient(135deg, #e74c3c, #c0392b);
  color: white;
}

.zone-cell.medium-risk {
  background: linear-gradient(135deg, #f39c12, #e67e22);
  color: white;
}

.zone-cell.low-risk {
  background: linear-gradient(135deg, #27ae60, #16a085);
  color: white;
}

.zone-name {
  font-size: 0.8rem;
  font-weight: 600;
  text-align: center;
  margin-bottom: 0.5rem;
}

.zone-count {
  font-size: 1.5rem;
  font-weight: 700;
}

/* Insights Card */
.insights-card {
  background: white;
  border-radius: 15px;
  padding: 1.5rem;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.insights-card h3 {
  color: #2c3e50;
  margin-bottom: 1rem;
  font-size: 1.25rem;
}

.insights-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.insight-item {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  padding: 1rem;
  background: linear-gradient(135deg, #f8f9fa, #e9ecef);
  border-radius: 10px;
  border-left: 4px solid #3498db;
  transition: all 0.3s ease;
}

.insight-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
}

.insight-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
  background: rgba(52, 152, 219, 0.1);
}

.insight-content {
  flex: 1;
}

.insight-title {
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 0.5rem;
}

.insight-description {
  color: #7f8c8d;
  font-size: 0.9rem;
  margin-bottom: 0.5rem;
  line-height: 1.4;
}

.insight-confidence {
  font-size: 0.8rem;
  color: #3498db;
  font-weight: 600;
}

/* Advanced Charts Section */
.advanced-charts-section,
.comparison-section {
  margin-bottom: 3rem;
}

.section-title {
  color: white;
  font-size: 2rem;
  text-align: center;
  margin-bottom: 2rem;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.chart-selector,
.year-selector {
  padding: 0.5rem;
  border: 1px solid #e0e6ed;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  font-size: 0.9rem;
}

.insight-badge {
  background: rgba(52, 168, 83, 0.2);
  color: #27ae60;
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: 600;
}

.metric-badge {
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: 600;
}

.metric-badge.success {
  background: rgba(39, 174, 96, 0.2);
  color: #27ae60;
}

.metric-badge.info {
  background: rgba(52, 152, 219, 0.2);
  color: #3498db;
}

.metric-badge.warning {
  background: rgba(243, 156, 18, 0.2);
  color: #f39c12;
}

.comparison-controls {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

/* Enhanced chart cards for advanced sections */
.advanced-charts-section .chart-card,
.comparison-section .chart-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
}

.advanced-charts-section .chart-card:hover,
.comparison-section .chart-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
}
</style>
