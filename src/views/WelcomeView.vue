<template>
  <div class="welcome-container">
    <div class="header">
      <h1>🗺️ Parque Nacional Huerquehue - Mapa de Incidentes</h1>
      <p>
        Visualización geográfica en tiempo real de incidentes en este hermoso parque de La Araucanía
      </p>
    </div>

    <div class="map-section">
      <div class="map-controls">
        <div class="legend">
          <h3>🏷️ Leyenda</h3>
          <div class="legend-items">
            <div class="legend-category">
              <strong>Por Estado:</strong>
              <div class="legend-item">
                <span class="legend-icon status-open">!</span>
                <span>Abierto</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon status-review">?</span>
                <span>En Revisión</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon status-closed">✓</span>
                <span>Cerrado</span>
              </div>
            </div>

            <div class="legend-category">
              <strong>Por Categoría (borde):</strong>
              <div class="legend-item">
                <span class="legend-icon category-fauna"></span>
                <span>Fauna Salvaje</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon category-senderos"></span>
                <span>Senderos</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon category-clima"></span>
                <span>Clima</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon category-infraestructura"></span>
                <span>Infraestructura</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon category-visitantes"></span>
                <span>Visitantes</span>
              </div>
              <div class="legend-item">
                <span class="legend-icon category-emergencia"></span>
                <span>Emergencia</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="map-container" ref="mapContainer">
        <div v-if="isMapLoading" class="map-loading">
          <div class="loading-spinner"></div>
          <p>Cargando mapa...</p>
        </div>
        <div v-if="mapError" class="map-error">
          <p>{{ mapError }}</p>
          <button @click="initMap" class="retry-button">🔄 Reintentar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'

interface Incident {
  id: number
  title: string
  description: string
  category: string
  status: string
  priority?: string
  route: string
  reporter: string
  dateReported: string
  lastUpdate: string
  coordinates?: {
    lat: number
    lng: number
  }
}

const mapContainer = ref<HTMLElement>()
const incidents = ref<Incident[]>([])
const isMapLoading = ref(true)
const mapError = ref('')
let map: L.Map | null = null

// Cargar incidentes con coordenadas geográficas
const loadIncidentsWithCoordinates = () => {
  const stored = localStorage.getItem('incidents')

  if (stored) {
    const storedIncidents = JSON.parse(stored)

    // Asignar coordenadas a los incidentes si no las tienen
    incidents.value = storedIncidents.map((incident: Incident) => {
      if (!incident.coordinates) {
        // Coordenadas reales del Parque Nacional Huerquehue, Chile
        const routeCoordinates: { [key: string]: { lat: number; lng: number } } = {
          'Sendero El Bosque': { lat: -39.1456, lng: -71.7589 }, // Sendero Los Huerquenes
          'Ruta del Mirador': { lat: -39.1398, lng: -71.7634 }, // Mirador Lago Tinquilco
          'Ruta Norte': { lat: -39.1334, lng: -71.7578 }, // Zona norte del parque
          'Camino al Lago': { lat: -39.1467, lng: -71.7612 }, // Lago Chico
          'Área de Picnic': { lat: -39.1423, lng: -71.7601 }, // Área de camping y picnic
          'Sendero Principal': { lat: -39.1445, lng: -71.7595 }, // Sendero principal del parque
          'Zona de Camping': { lat: -39.1434, lng: -71.7588 }, // Camping Tinquilco
          'Mirador Sur': { lat: -39.1489, lng: -71.7623 }, // Mirador hacia el sur
          'Ruta de los Pinos': { lat: -39.1412, lng: -71.7556 }, // Sendero entre araucarias
          'Sendero del Río': { lat: -39.1478, lng: -71.7645 }, // Sendero Río Blanco
        }

        incident.coordinates = routeCoordinates[incident.route] || {
          lat: -39.1445 + (Math.random() - 0.5) * 0.02,
          lng: -71.7595 + (Math.random() - 0.5) * 0.02,
        }
      }
      return incident
    })

    // Guardar incidentes actualizados con coordenadas
    localStorage.setItem('incidents', JSON.stringify(incidents.value))
  } else {
    // Crear datos de muestra cuando no hay datos en localStorage
    createSampleData()
  }
}

const createSampleData = () => {
  const sampleIncidents: Incident[] = [
    {
      id: 1,
      title: 'Árbol caído bloquea Sendero Los Huerquenes',
      category: 'Basura',
      status: 'Abierto',
      route: 'Sendero El Bosque',
      description:
        'Un árbol grande ha caído después de la tormenta de anoche, bloqueando completamente el paso en el kilómetro 2.5 del sendero.',
      dateReported: '2024-01-15',
      lastUpdate: '2024-01-15',
      reporter: 'Guardaparque Juan Pérez',
      coordinates: { lat: -39.1456, lng: -71.7589 },
    },
    {
      id: 2,
      title: 'Animal muerto encontrado en el sendero',
      category: 'Animal muerto',
      status: 'En revisión',
      route: 'Ruta del Mirador',
      description:
        'Se encontró un animal muerto que debe ser removido del sendero principal por motivos sanitarios.',
      dateReported: '2024-01-14',
      lastUpdate: '2024-01-14',
      reporter: 'María González',
      coordinates: { lat: -39.1398, lng: -71.7634 },
    },
    {
      id: 3,
      title: 'Excursionista con tobillo torcido en Laguna Verde',
      category: 'Accidente',
      status: 'Cerrado',
      route: 'Camino al Lago',
      description:
        'Rescate exitoso de excursionista con lesión en tobillo. Fue evacuado por equipo de rescate.',
      dateReported: '2024-01-13',
      lastUpdate: '2024-01-13',
      reporter: 'Carlos Ramírez',
      coordinates: { lat: -39.1467, lng: -71.7612 },
    },
    {
      id: 4,
      title: 'Acumulación de basura en área de picnic',
      category: 'Basura',
      status: 'Abierto',
      route: 'Área de Picnic',
      description:
        'Se ha acumulado una cantidad considerable de basura dejada por visitantes del fin de semana.',
      dateReported: '2024-01-16',
      lastUpdate: '2024-01-16',
      reporter: 'Ana Silva',
      coordinates: { lat: -39.1423, lng: -71.7601 },
    },
    {
      id: 5,
      title: 'Puente dañado en Sendero Quinchol',
      category: 'Basura',
      status: 'En revisión',
      route: 'Sendero del Río',
      description:
        'El puente de madera sobre el arroyo presenta daños en las tablas. Necesita reparación urgente.',
      dateReported: '2024-01-12',
      lastUpdate: '2024-01-12',
      reporter: 'Pedro Morales',
      coordinates: { lat: -39.1478, lng: -71.7645 },
    },
    {
      id: 6,
      title: 'Sendero bloqueado por caída de rocas',
      category: 'Basura',
      status: 'Abierto',
      route: 'Ruta Norte',
      description:
        'Desprendimiento de rocas ha bloqueado el acceso. Se requiere maquinaria para limpiar.',
      dateReported: '2024-01-17',
      lastUpdate: '2024-01-17',
      reporter: 'Luis Herrera',
      coordinates: { lat: -39.1334, lng: -71.7578 },
    },
  ]

  incidents.value = sampleIncidents
  localStorage.setItem('incidents', JSON.stringify(incidents.value))
}

// Colores por categoría
const getCategoryColor = (category: string): string => {
  const colors: { [key: string]: string } = {
    Accidente: '#FF4757',
    Basura: '#96CEB4',
    'Animal muerto': '#FF6B35',
  }
  return colors[category] || '#6C5CE7'
}

// Inicializar el mapa
const initMap = () => {
  if (!mapContainer.value) {
    mapError.value = 'No se pudo encontrar el contenedor del mapa'
    isMapLoading.value = false
    return
  }

  try {
    // Crear el mapa centrado en Parque Nacional Huerquehue
    map = L.map(mapContainer.value).setView([-39.1445, -71.7595], 13)

    // Añadir tiles de OpenStreetMap
    L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '© OpenStreetMap contributors',
    }).addTo(map)

    addMarkersToMap()
    isMapLoading.value = false
  } catch (error) {
    mapError.value = `Error al cargar el mapa: ${error}`
    isMapLoading.value = false
    return
  }
}

// Añadir marcadores al mapa
const addMarkersToMap = () => {
  if (!map) return

  console.log(`🗺️ Añadiendo ${incidents.value.length} marcadores al mapa`)

  // Añadir marcadores para cada incidente
  incidents.value.forEach((incident) => {
    if (incident.coordinates) {
      console.log(
        `📍 Creando marcador para: ${incident.title} en [${incident.coordinates.lat}, ${incident.coordinates.lng}]`,
      )
      try {
        // Crear icono personalizado según la categoría
        const iconColor = getCategoryColor(incident.category)

        // Determinar el símbolo según el estado
        let statusSymbol = '!'
        if (incident.status === 'en_revision') statusSymbol = '?'
        if (incident.status === 'cerrado') statusSymbol = '✓'

        // Crear HTML para el icono personalizado - Circular MÁS GRANDE
        const iconHtml = `
          <div style="
            background-color: ${iconColor};
            width: 50px;
            height: 50px;
            border-radius: 50%;
            border: 4px solid ${iconColor};
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            font-weight: bold;
            color: white;
            box-shadow: 0 4px 12px rgba(0,0,0,0.4);
            cursor: pointer;
          ">
            ${statusSymbol}
          </div>
        `

        const customIcon = L.divIcon({
          html: iconHtml,
          className: 'custom-incident-marker',
          iconSize: [58, 58],
          iconAnchor: [29, 29],
          popupAnchor: [0, -29],
        })

        // Crear popup con información del incidente
        const statusBg =
          incident.status === 'abierto'
            ? '#FF4757'
            : incident.status === 'en_revision'
              ? '#3498db'
              : '#2ECC71'
        const statusText =
          incident.status === 'abierto'
            ? 'ABIERTO'
            : incident.status === 'en_revision'
              ? 'EN REVISIÓN'
              : 'CERRADO'

        const popupContent = `
          <div class="incident-popup" style="min-width: 280px; font-family: Arial, sans-serif;">
            <div style="border-left: 4px solid ${iconColor}; padding-left: 10px; margin-bottom: 10px;">
              <h3 style="margin: 0 0 5px 0; color: ${iconColor}; font-size: 16px; font-weight: bold;">
                ${incident.title}
              </h3>
            </div>

            <div style="display: grid; grid-template-columns: 1fr; gap: 6px; margin-bottom: 10px;">
              <div style="display: flex; align-items: center; gap: 8px;">
                <strong style="color: #333; font-size: 12px;">Estado:</strong>
                <span style="
                  background: ${statusBg};
                  color: white;
                  padding: 3px 8px;
                  border-radius: 12px;
                  font-size: 11px;
                  font-weight: bold;
                ">${statusText}</span>
              </div>

              <div style="display: flex; align-items: center; gap: 8px;">
                <strong style="color: #333; font-size: 12px;">Categoría:</strong>
                <span style="color: #666; font-size: 12px;">${incident.category}</span>
              </div>

              <div style="display: flex; align-items: center; gap: 8px;">
                <strong style="color: #333; font-size: 12px;">Ubicación:</strong>
                <span style="color: #666; font-size: 12px;">${incident.route}</span>
              </div>

              <div style="display: flex; align-items: center; gap: 8px;">
                <strong style="color: #333; font-size: 12px;">Reportado por:</strong>
                <span style="color: #666; font-size: 12px;">${incident.reporter}</span>
              </div>
            </div>

            <div style="background: #f8f9fa; padding: 8px; border-radius: 6px; margin: 8px 0;">
              <strong style="color: #333; font-size: 11px; display: block; margin-bottom: 4px;">Descripción:</strong>
              <p style="margin: 0; font-size: 11px; line-height: 1.4; color: #666;">
                ${incident.description.length > 120 ? incident.description.substring(0, 120) + '...' : incident.description}
              </p>
            </div>

            <div style="text-align: right; font-size: 10px; color: #999; margin-top: 8px;">
              📅 ${new Date(incident.dateReported).toLocaleDateString('es-ES', {
                year: 'numeric',
                month: 'long',
                day: 'numeric',
              })}
            </div>
          </div>
        `

        // Añadir marcador al mapa con popup
        const marker = L.marker([incident.coordinates.lat, incident.coordinates.lng], {
          icon: customIcon,
          title: incident.title, // Tooltip al hacer hover
        })
          .addTo(map!)
          .bindPopup(popupContent, {
            maxWidth: 300,
            className: 'custom-popup',
          })

        // Abrir popup al hacer clic
        marker.on('click', () => {
          marker.openPopup()
        })
      } catch (error) {
        console.error(`Error adding marker for incident ${incident.id}:`, error)
      }
    }
  })

  // Ajustar vista para mostrar todos los marcadores
  if (incidents.value.length > 0) {
    const group = new L.FeatureGroup(
      incidents.value
        .filter((incident) => incident.coordinates)
        .map((incident) => L.marker([incident.coordinates!.lat, incident.coordinates!.lng])),
    )
    if (group.getBounds().isValid()) {
      map!.fitBounds(group.getBounds().pad(0.1))
    }
  }
}

onMounted(() => {
  // Forzar actualización de coordenadas eliminando las existentes
  const stored = localStorage.getItem('incidents')
  if (stored) {
    const storedIncidents = JSON.parse(stored)
    // Limpiar coordenadas existentes para forzar regeneración con Huerquehue
    const cleanedIncidents = storedIncidents.map((incident: Incident) => {
      delete incident.coordinates
      return incident
    })
    localStorage.setItem('incidents', JSON.stringify(cleanedIncidents))
  }

  loadIncidentsWithCoordinates()
  setTimeout(() => {
    initMap()
  }, 300)
})
</script>

<style scoped>
.welcome-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #2c5530 0%, #4a7c59 50%, #6b8e23 100%);
  color: white;
  padding: 20px;
}

.header {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px;
}

.header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.header p {
  font-size: 1.2rem;
  margin-bottom: 20px;
  opacity: 0.9;
}

.map-section {
  display: flex;
  gap: 20px;
  max-width: 1400px;
  margin: 0 auto;
  min-height: 600px;
}

.map-controls {
  flex: 0 0 250px;
}

.legend {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  height: fit-content;
  color: #333;
}

.legend h3 {
  margin: 0 0 15px 0;
  color: #2c3e50;
  font-size: 1.2rem;
}

.legend-category {
  margin-bottom: 20px;
}

.legend-category strong {
  display: block;
  margin-bottom: 8px;
  color: #34495e;
  font-size: 0.9rem;
}

.legend-item {
  display: flex;
  align-items: center;
  margin-bottom: 6px;
  font-size: 0.85rem;
  color: #666;
}

.legend-icon {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  margin-right: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  font-weight: bold;
  color: white;
  flex-shrink: 0;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.status-open {
  background: #ff4757;
}

.status-review {
  background: #3498db;
}

.status-closed {
  background: #2ecc71;
}

.category-fauna {
  background: #ccc;
  border: 3px solid #ff6b35;
}

.category-senderos {
  background: #ccc;
  border: 3px solid #4ecdc4;
}

.category-clima {
  background: #ccc;
  border: 3px solid #45b7d1;
}

.category-infraestructura {
  background: #ccc;
  border: 3px solid #96ceb4;
}

.category-visitantes {
  background: #ccc;
  border: 3px solid #feca57;
}

.category-emergencia {
  background: #ccc;
  border: 3px solid #ff4757;
}

.map-container {
  flex: 1;
  height: 600px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  position: relative;
  background: #f8f9fa;
}

.map-loading,
.map-error {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.9);
  z-index: 1000;
  color: #333;
}

.loading-spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #34a853;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 10px;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.map-error p {
  color: #e74c3c;
  margin-bottom: 10px;
}

.retry-button {
  background: #34a853;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
}

.retry-button:hover {
  background: #2d8f47;
}

/* Estilos globales para Leaflet */
:global(.leaflet-popup-content) {
  margin: 8px 12px;
  line-height: 1.4;
}

:global(.leaflet-popup-content-wrapper) {
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

:global(.custom-popup .leaflet-popup-content-wrapper) {
  background: white;
  border-radius: 12px;
}

:global(.custom-popup .leaflet-popup-tip) {
  background: white;
}

:global(.custom-incident-marker) {
  background: transparent !important;
  border: none !important;
}

/* Asegurar que los marcadores estén por encima de otros elementos */
:global(.custom-incident-marker) {
  z-index: 1000 !important;
  background: transparent !important;
  border: none !important;
}

:global(.leaflet-popup-close-button) {
  color: #999 !important;
  font-size: 18px !important;
  width: 24px !important;
  height: 24px !important;
  padding: 0 !important;
}

:global(.leaflet-popup-close-button:hover) {
  color: #333 !important;
  background: rgba(0, 0, 0, 0.1) !important;
  border-radius: 50% !important;
}

@media (max-width: 768px) {
  .welcome-container {
    padding: 10px;
  }

  .header h1 {
    font-size: 2rem;
  }

  .map-section {
    flex-direction: column;
  }

  .map-controls {
    flex: none;
  }

  .map-container {
    height: 400px;
  }
}
</style>
display: flex; gap: 1rem; flex-wrap: wrap; } .btn { display: inline-flex; align-items: center; gap:
0.5rem; padding: 1rem 2rem; border-radius: 50px; text-decoration: none; font-weight: 600;
transition: all 0.3s ease; border: 2px solid transparent; } .btn-primary { background: rgba(255,
255, 255, 0.2); color: white; backdrop-filter: blur(10px); } .btn-primary:hover { background:
rgba(255, 255, 255, 0.3); transform: translateY(-2px); } .btn-secondary { background: transparent;
color: white; border-color: rgba(255, 255, 255, 0.3); } .btn-secondary:hover { background: rgba(255,
255, 255, 0.1); transform: translateY(-2px); } .icon { font-size: 1.2rem; } .hero-image { flex: 1;
display: flex; justify-content: center; align-items: center; } .floating-card { background:
rgba(255, 255, 255, 0.1); backdrop-filter: blur(10px); border-radius: 20px; padding: 2rem; border:
1px solid rgba(255, 255, 255, 0.2); animation: float 6s ease-in-out infinite; } @keyframes float {
0%, 100% { transform: translateY(0px); } 50% { transform: translateY(-20px); } } .card-content h3 {
margin-bottom: 1rem; font-size: 1.5rem; color: #fff; } .card-content ul { list-style: none; padding:
0; } .card-content li { padding: 0.5rem 0; opacity: 0.9; font-size: 0.95rem; } .features-section {
padding: 4rem 5%; background: rgba(255, 255, 255, 0.05); text-align: center; width: 100%;
box-sizing: border-box; } .features-section h2 { font-size: 2.5rem; margin-bottom: 3rem;
font-weight: 600; } .features-grid { display: grid; grid-template-columns: repeat(auto-fit,
minmax(300px, 1fr)); gap: 2rem; width: 100%; max-width: 1200px; margin: 0 auto; } .feature-card {
background: rgba(255, 255, 255, 0.1); backdrop-filter: blur(10px); border-radius: 15px; padding:
2rem; border: 1px solid rgba(255, 255, 255, 0.2); transition: all 0.3s ease; cursor: pointer; }
.feature-card:hover { transform: translateY(-5px); background: rgba(255, 255, 255, 0.15); }
.feature-icon { font-size: 3rem; margin-bottom: 1rem; } .feature-card h3 { font-size: 1.5rem;
margin-bottom: 1rem; font-weight: 600; color: #fff; } .feature-card p { opacity: 0.9; line-height:
1.6; font-size: 0.95rem; } @media (max-width: 768px) { .welcome-container { width: 100vw; margin: 0;
padding: 0; } .hero-section { flex-direction: column; text-align: center; gap: 2rem; padding: 2rem
5%; width: 100%; } .hero-title { font-size: 2.5rem; } .hero-buttons { justify-content: center;
width: 100%; } .features-section { padding: 2rem 5%; width: 100%; } .features-grid {
grid-template-columns: 1fr; width: 100%; } }
