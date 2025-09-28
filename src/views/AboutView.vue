<template>
  <div class="about-container">
    <div class="about-header">
      <h1>Acerca del Sistema</h1>
      <p>Sistema de Gestión de Incidentes para Parques Nacionales</p>
    </div>

    <div class="about-content">
      <div class="info-section">
        <div class="info-card">
          <div class="card-icon">🏞️</div>
          <h3>Nuestra Misión</h3>
          <p>
            Proporcionar una herramienta integral y eficiente para la gestión y monitoreo de
            incidentes en áreas protegidas, garantizando la seguridad de visitantes y la
            preservación del ecosistema natural.
          </p>
        </div>

        <div class="info-card">
          <div class="card-icon">🛡️</div>
          <h3>Seguridad Primero</h3>
          <p>
            Nuestro sistema prioriza la respuesta rápida y efectiva ante emergencias, facilitando la
            coordinación entre equipos de rescate, bomberos forestales y autoridades locales.
          </p>
        </div>

        <div class="info-card">
          <div class="card-icon">📊</div>
          <h3>Análisis de Datos</h3>
          <p>
            Utilizamos análisis estadístico avanzado para identificar patrones, prevenir incidentes
            futuros y optimizar la asignación de recursos en áreas de mayor riesgo.
          </p>
        </div>
      </div>

      <div class="features-list">
        <h2>Características Principales</h2>
        <div class="features-grid">
          <div class="feature-item">
            <span class="feature-icon">🚨</span>
            <div class="feature-text">
              <h4>Registro de Incidentes</h4>
              <p>Clasificación automática por tipo, severidad y ubicación</p>
            </div>
          </div>

          <div class="feature-item">
            <span class="feature-icon">📍</span>
            <div class="feature-text">
              <h4>Geolocalización GPS</h4>
              <p>Ubicación precisa de incidentes para respuesta rápida</p>
            </div>
          </div>

          <div class="feature-item">
            <span class="feature-icon">📈</span>
            <div class="feature-text">
              <h4>Dashboard Interactivo</h4>
              <p>Visualización de estadísticas y tendencias en tiempo real</p>
            </div>
          </div>

          <div class="feature-item">
            <span class="feature-icon">🔔</span>
            <div class="feature-text">
              <h4>Sistema de Alertas</h4>
              <p>Notificaciones automáticas para incidentes críticos</p>
            </div>
          </div>

          <div class="feature-item">
            <span class="feature-icon">👥</span>
            <div class="feature-text">
              <h4>Gestión de Equipos</h4>
              <p>Coordinación eficiente de personal y recursos</p>
            </div>
          </div>

          <div class="feature-item">
            <span class="feature-icon">📋</span>
            <div class="feature-text">
              <h4>Reportes Detallados</h4>
              <p>Informes completos para análisis y autoridades</p>
            </div>
          </div>
        </div>
      </div>

      <div class="stats-section">
        <h2>Estadísticas del Sistema</h2>
        <div class="stats-grid">
          <div class="stat-item">
            <div class="stat-number">24/7</div>
            <div class="stat-label">Monitoreo Continuo</div>
          </div>
          <div class="stat-item">
            <div class="stat-number">100%</div>
            <div class="stat-label">Disponibilidad</div>
          </div>
          <div class="stat-item">
            <div class="stat-number">&lt;5min</div>
            <div class="stat-label">Tiempo de Respuesta</div>
          </div>
          <div class="stat-item">
            <div class="stat-number">99.9%</div>
            <div class="stat-label">Precisión GPS</div>
          </div>
        </div>
      </div>

      <div class="contact-section">
        <h2>Información del Sistema</h2>
        <div class="contact-info">
          <div class="contact-item">
            <span class="contact-icon">🏢</span>
            <div>
              <h4>Administración</h4>
              <p>Servicio Nacional de Parques</p>
            </div>
          </div>
          <div class="contact-item">
            <span class="contact-icon">💻</span>
            <div>
              <h4>Soporte Técnico</h4>
              <p>Disponible 24/7 para emergencias</p>
            </div>
          </div>
          <div class="contact-item">
            <span class="contact-icon">📱</span>
            <div>
              <h4>Emergencias</h4>
              <p>Línea directa de emergencias</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.about-container {
  width: 100vw;
  max-width: 100vw;
  margin: 0;
  padding: 2rem 5%;
  min-height: 100vh;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  box-sizing: border-box;
  overflow-x: hidden;
}

.about-header {
  text-align: center;
  margin-bottom: 3rem;
}

.about-header h1 {
  font-size: 3rem;
  color: #2c3e50;
  margin-bottom: 1rem;
  font-weight: 700;
}

.about-header p {
  font-size: 1.25rem;
  color: #6c757d;
  font-weight: 500;
}

.about-content {
  max-width: 1200px;
  margin: 0 auto;
}

.info-section {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  margin-bottom: 4rem;
}

.info-card {
  background: white;
  border-radius: 15px;
  padding: 2rem;
  text-align: center;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
}

.info-card:hover {
  transform: translateY(-5px);
}

.card-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
}

.info-card h3 {
  font-size: 1.5rem;
  color: #2c3e50;
  margin-bottom: 1rem;
  font-weight: 600;
}

.info-card p {
  color: #6c757d;
  line-height: 1.6;
}

.features-list {
  margin-bottom: 4rem;
}

.features-list h2 {
  text-align: center;
  font-size: 2.5rem;
  color: #2c3e50;
  margin-bottom: 2rem;
  font-weight: 600;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 1.5rem;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  background: white;
  padding: 1.5rem;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.feature-item:hover {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  transform: translateY(-2px);
}

.feature-icon {
  font-size: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
  background: #e3f2fd;
  border-radius: 50%;
  flex-shrink: 0;
}

.feature-text h4 {
  color: #2c3e50;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

.feature-text p {
  color: #6c757d;
  font-size: 0.9rem;
  margin: 0;
}

.stats-section {
  margin-bottom: 4rem;
  text-align: center;
}

.stats-section h2 {
  font-size: 2.5rem;
  color: #2c3e50;
  margin-bottom: 2rem;
  font-weight: 600;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 2rem;
}

.stat-item {
  background: white;
  padding: 2rem;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.stat-number {
  font-size: 3rem;
  font-weight: 700;
  color: #28a745;
  margin-bottom: 0.5rem;
}

.stat-label {
  color: #6c757d;
  font-weight: 500;
}

.contact-section {
  text-align: center;
}

.contact-section h2 {
  font-size: 2.5rem;
  color: #2c3e50;
  margin-bottom: 2rem;
  font-weight: 600;
}

.contact-info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  background: white;
  padding: 2rem;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: left;
}

.contact-icon {
  font-size: 2.5rem;
  flex-shrink: 0;
}

.contact-item h4 {
  color: #2c3e50;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

.contact-item p {
  color: #6c757d;
  margin: 0;
}

@media (max-width: 768px) {
  .about-container {
    padding: 1rem 3%;
  }

  .about-header h1 {
    font-size: 2.5rem;
  }

  .info-section {
    grid-template-columns: 1fr;
  }

  .features-grid {
    grid-template-columns: 1fr;
  }

  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .contact-info {
    grid-template-columns: 1fr;
  }
}
</style>
