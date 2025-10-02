<template>
  <Transition name="perfil-fade">
    <div v-if="mostrar" class="perfil-overlay" @click="cerrarPerfil">
    <div class="perfil-modal" @click.stop>
      <div class="perfil-header">
        <div class="header-content">
          <h1 class="perfil-titulo">
            <span class="stark-logo">STARK</span>
            <span class="industries-text">PERFIL</span>
          </h1>
          <p class="subtitle">Gestión de Cuenta Corporativa</p>
        </div>
        <button class="btn-cerrar" @click="cerrarPerfil">
          <span class="cerrar-icon">×</span>
        </button>
      </div>

      <div class="perfil-content">
        <div class="perfil-sidebar">
          <div class="user-info">
            <div class="user-avatar">
              <img :src="usuario?.foto || 'https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?w=150&h=150&fit=crop&crop=face'" :alt="usuario?.nombre || 'Usuario'" />
              <div class="avatar-overlay">
                <button class="btn-cambiar-foto" @click="cambiarFoto">
                  <span class="camera-icon">📷</span>
                </button>
              </div>
            </div>
            <h3 class="user-nombre">{{ usuario?.nombre || 'Usuario' }}</h3>
            <p class="user-empresa">{{ usuario?.empresa || 'Empresa' }}</p>
            <p class="user-email">{{ usuario?.email || 'email@ejemplo.com' }}</p>
            <div class="user-status">
              <span class="status-dot"></span>
              <span class="status-text">Activo</span>
            </div>
          </div>

          <nav class="perfil-nav">
            <button 
              v-for="seccion in secciones" 
              :key="seccion.id"
              class="nav-item"
              :class="{ 'nav-activo': seccionActiva === seccion.id }"
              @click="cambiarSeccion(seccion.id)"
            >
              <span class="nav-icon">{{ seccion.icono }}</span>
              <span class="nav-text">{{ seccion.nombre }}</span>
            </button>
          </nav>
        </div>

        <div class="perfil-main">
          <!-- Sección: Información Personal -->
          <div v-if="seccionActiva === 'personal'" class="seccion-content">
            <h2 class="seccion-titulo">Información Personal</h2>
            <div class="info-grid">
              <div class="info-item">
                <label class="info-label">Nombre Completo</label>
                <input v-model="usuario.nombre" class="info-input" />
              </div>
              <div class="info-item">
                <label class="info-label">Empresa</label>
                <input v-model="usuario.empresa" class="info-input" />
              </div>
              <div class="info-item">
                <label class="info-label">Email</label>
                <input v-model="usuario.email" class="info-input" type="email" />
              </div>
              <div class="info-item">
                <label class="info-label">Teléfono</label>
                <input v-model="usuario.telefono" class="info-input" type="tel" />
              </div>
              <div class="info-item">
                <label class="info-label">Área de Interés</label>
                <select v-model="usuario.areaInteres" class="info-select">
                  <option value="tecnologia">Tecnología Avanzada</option>
                  <option value="defensa">Sistemas de Defensa</option>
                  <option value="energia">Energía Limpia</option>
                  <option value="investigacion">Investigación y Desarrollo</option>
                  <option value="consultoria">Consultoría Tecnológica</option>
                </select>
              </div>
              <div class="info-item">
                <label class="info-label">Fecha de Registro</label>
                <input :value="formatearFecha(usuario?.fechaRegistro)" class="info-input" readonly />
              </div>
            </div>
            <button class="btn-guardar" @click="guardarInformacion">
              <span class="btn-text">GUARDAR CAMBIOS</span>
              <div class="btn-glow"></div>
            </button>
          </div>

          <!-- Sección: Seguridad -->
          <div v-if="seccionActiva === 'seguridad'" class="seccion-content">
            <h2 class="seccion-titulo">Seguridad y Contraseña</h2>
            <div class="security-form">
              <div class="form-group">
                <label class="form-label">Contraseña Actual</label>
                <div class="password-input">
                  <input 
                    v-model="seguridad.passwordActual" 
                    :type="mostrarPasswordActual ? 'text' : 'password'"
                    class="form-input"
                    placeholder="••••••••"
                  />
                  <button class="btn-toggle-password" @click="mostrarPasswordActual = !mostrarPasswordActual">
                    <span class="password-icon">{{ mostrarPasswordActual ? '👁️' : '👁️‍🗨️' }}</span>
                  </button>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">Nueva Contraseña</label>
                <div class="password-input">
                  <input 
                    v-model="seguridad.nuevaPassword" 
                    :type="mostrarNuevaPassword ? 'text' : 'password'"
                    class="form-input"
                    placeholder="••••••••"
                  />
                  <button class="btn-toggle-password" @click="mostrarNuevaPassword = !mostrarNuevaPassword">
                    <span class="password-icon">{{ mostrarNuevaPassword ? '👁️' : '👁️‍🗨️' }}</span>
                  </button>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">Confirmar Nueva Contraseña</label>
                <div class="password-input">
                  <input 
                    v-model="seguridad.confirmarPassword" 
                    :type="mostrarConfirmarPassword ? 'text' : 'password'"
                    class="form-input"
                    placeholder="••••••••"
                  />
                  <button class="btn-toggle-password" @click="mostrarConfirmarPassword = !mostrarConfirmarPassword">
                    <span class="password-icon">{{ mostrarConfirmarPassword ? '👁️' : '👁️‍🗨️' }}</span>
                  </button>
                </div>
              </div>
              <button class="btn-cambiar-password" @click="cambiarPassword">
                <span class="btn-text">CAMBIAR CONTRASEÑA</span>
                <div class="btn-glow"></div>
              </button>
            </div>
          </div>

          <!-- Sección: Mis Compras -->
          <div v-if="seccionActiva === 'compras'" class="seccion-content">
            <h2 class="seccion-titulo">Historial de Compras</h2>
            <div v-if="!usuario?.compras || usuario.compras.length === 0" class="compras-vacio">
              <div class="vacio-icon">🛒</div>
              <h3 class="vacio-titulo">No hay compras registradas</h3>
              <p class="vacio-descripcion">Tus compras aparecerán aquí una vez que realices tu primera compra.</p>
            </div>
            <div v-else class="compras-list">
              <div 
                v-for="compra in usuario.compras" 
                :key="compra.id"
                class="compra-item"
              >
                <div class="compra-header">
                  <h4 class="compra-id">Compra #{{ compra.id }}</h4>
                  <span class="compra-fecha">{{ formatearFecha(compra.fecha) }}</span>
                </div>
                <div class="compra-productos">
                  <div 
                    v-for="producto in compra.productos" 
                    :key="producto.id"
                    class="producto-compra"
                  >
                    <img :src="producto.imagen" :alt="producto.nombre" class="producto-imagen" />
                    <div class="producto-info">
                      <h5 class="producto-nombre">{{ producto.nombre }}</h5>
                      <p class="producto-cantidad">Cantidad: {{ producto.cantidad }}</p>
                      <p class="producto-precio">${{ producto.precio.toLocaleString() }}</p>
                    </div>
                  </div>
                </div>
                <div class="compra-total">
                  <span class="total-label">Total:</span>
                  <span class="total-valor">${{ compra.total.toLocaleString() }}</span>
                </div>
                <div class="compra-estado">
                  <span class="estado-badge" :class="compra.estado">{{ compra.estado.toUpperCase() }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Sección: Mi Asesor -->
          <div v-if="seccionActiva === 'asesor'" class="seccion-content">
            <h2 class="seccion-titulo">Mi Asesor Personal</h2>
            <div class="asesor-card">
              <div class="asesor-avatar">
                <img :src="asesor.foto" :alt="asesor.nombre" />
                <div class="asesor-status">
                  <span class="status-dot online"></span>
                </div>
              </div>
              <div class="asesor-info">
                <h3 class="asesor-nombre">{{ asesor.nombre }}</h3>
                <p class="asesor-especialidad">{{ asesor.especialidad }}</p>
                <p class="asesor-email">{{ asesor.email }}</p>
                <div class="asesor-stats">
                  <div class="stat-item">
                    <span class="stat-label">Clientes Atendidos:</span>
                    <span class="stat-value">{{ asesor.clientesAtendidos }}</span>
                  </div>
                  <div class="stat-item">
                    <span class="stat-label">Experiencia:</span>
                    <span class="stat-value">{{ asesor.experiencia }}</span>
                  </div>
                </div>
              </div>
              <div class="asesor-actions">
                <button class="btn-contactar-asesor" @click="contactarAsesor">
                  <span class="btn-text">CONTACTAR</span>
                  <div class="btn-glow"></div>
                </button>
                <button class="btn-videollamada" @click="iniciarVideollamada">
                  <span class="btn-text">VIDEOLLAMADA</span>
                  <div class="btn-glow"></div>
                </button>
              </div>
            </div>
            <div class="asesor-mensajes">
              <h4 class="mensajes-titulo">Últimos Mensajes</h4>
              <div class="mensajes-list">
                <div 
                  v-for="mensaje in asesor.mensajes" 
                  :key="mensaje.id"
                  class="mensaje-item"
                >
                  <div class="mensaje-header">
                    <span class="mensaje-fecha">{{ formatearFecha(mensaje.fecha) }}</span>
                  </div>
                  <p class="mensaje-contenido">{{ mensaje.contenido }}</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Sección: Configuración -->
          <div v-if="seccionActiva === 'configuracion'" class="seccion-content">
            <h2 class="seccion-titulo">Configuración de Cuenta</h2>
            <div class="config-grid">
              <div class="config-item">
                <h4 class="config-titulo">Notificaciones</h4>
                <div class="config-options">
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="configuracion.notificacionesEmail" class="checkbox-input">
                    <span class="checkbox-text">Recibir notificaciones por email</span>
                  </label>
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="configuracion.notificacionesProductos" class="checkbox-input">
                    <span class="checkbox-text">Notificaciones de nuevos productos</span>
                  </label>
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="configuracion.notificacionesOfertas" class="checkbox-input">
                    <span class="checkbox-text">Ofertas especiales</span>
                  </label>
                </div>
              </div>
              <div class="config-item">
                <h4 class="config-titulo">Privacidad</h4>
                <div class="config-options">
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="configuracion.perfilPublico" class="checkbox-input">
                    <span class="checkbox-text">Perfil público</span>
                  </label>
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="configuracion.compartirDatos" class="checkbox-input">
                    <span class="checkbox-text">Compartir datos con partners</span>
                  </label>
                </div>
              </div>
            </div>
            <button class="btn-guardar-config" @click="guardarConfiguracion">
              <span class="btn-text">GUARDAR CONFIGURACIÓN</span>
              <div class="btn-glow"></div>
            </button>
          </div>
        </div>
      </div>

      <div class="perfil-border"></div>
    </div>
  </div>
  </Transition>
</template>

<script>
export default {
  name: 'PerfilUsuario',
  props: {
    usuario: {
      type: Object,
      required: true
    },
    mostrar: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      seccionActiva: 'personal',
      secciones: [
        { id: 'personal', nombre: 'Información', icono: '👤' },
        { id: 'seguridad', nombre: 'Seguridad', icono: '🔒' },
        { id: 'compras', nombre: 'Mis Compras', icono: '🛒' },
        { id: 'asesor', nombre: 'Mi Asesor', icono: '👨‍💼' },
        { id: 'configuracion', nombre: 'Configuración', icono: '⚙️' }
      ],
      seguridad: {
        passwordActual: '',
        nuevaPassword: '',
        confirmarPassword: ''
      },
      mostrarPasswordActual: false,
      mostrarNuevaPassword: false,
      mostrarConfirmarPassword: false,
      configuracion: {
        notificacionesEmail: true,
        notificacionesProductos: true,
        notificacionesOfertas: false,
        perfilPublico: false,
        compartirDatos: false
      },
      asesor: {
        nombre: 'Dr. Bruce Banner',
        especialidad: 'Tecnología Avanzada',
        email: 'bruce.banner@starkindustries.com',
        foto: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150&h=150&fit=crop&crop=face',
        clientesAtendidos: 247,
        experiencia: '15 años',
        mensajes: [
          {
            id: 1,
            fecha: new Date().toISOString(),
            contenido: 'Hola! He revisado tu perfil y veo que tienes interés en tecnología avanzada. Te recomiendo el nuevo Arc Reactor Mark V.'
          },
          {
            id: 2,
            fecha: new Date(Date.now() - 86400000).toISOString(),
            contenido: '¿Te gustaría programar una videollamada para discutir las mejores opciones para tu empresa?'
          }
        ]
      }
    }
  },
  methods: {
    cerrarPerfil() {
      console.log('🔴 Cerrando perfil de usuario')
      this.$emit('cerrar')
    },
    cambiarSeccion(seccionId) {
      this.seccionActiva = seccionId
    },
    cambiarFoto() {
      if (!this.usuario) return
      
      const input = document.createElement('input')
      input.type = 'file'
      input.accept = 'image/*'
      input.onchange = (e) => {
        const file = e.target.files[0]
        if (file) {
          const reader = new FileReader()
          reader.onload = (e) => {
            this.usuario.foto = e.target.result
            this.guardarUsuario()
          }
          reader.readAsDataURL(file)
        }
      }
      input.click()
    },
    guardarInformacion() {
      if (!this.usuario) return
      this.guardarUsuario()
      alert('Información guardada exitosamente')
    },
    cambiarPassword() {
      if (!this.usuario) return
      
      if (!this.seguridad.passwordActual) {
        alert('Ingresa tu contraseña actual')
        return
      }
      if (!this.seguridad.nuevaPassword) {
        alert('Ingresa una nueva contraseña')
        return
      }
      if (this.seguridad.nuevaPassword !== this.seguridad.confirmarPassword) {
        alert('Las contraseñas no coinciden')
        return
      }
      if (this.seguridad.nuevaPassword.length < 6) {
        alert('La nueva contraseña debe tener al menos 6 caracteres')
        return
      }

      // Actualizar contraseña
      this.usuario.password = this.seguridad.nuevaPassword
      this.guardarUsuario()
      
      // Limpiar formulario
      this.seguridad = {
        passwordActual: '',
        nuevaPassword: '',
        confirmarPassword: ''
      }
      
      alert('Contraseña cambiada exitosamente')
    },
    contactarAsesor() {
      alert(`Contactando a ${this.asesor.nombre}...`)
    },
    iniciarVideollamada() {
      alert(`Iniciando videollamada con ${this.asesor.nombre}...`)
    },
    guardarConfiguracion() {
      if (!this.usuario) return
      this.guardarUsuario()
      alert('Configuración guardada exitosamente')
    },
    guardarUsuario() {
      if (!this.usuario) return
      
      const usuarios = JSON.parse(localStorage.getItem('starkUsuarios') || '[]')
      const index = usuarios.findIndex(u => u.id === this.usuario.id)
      if (index !== -1) {
        usuarios[index] = { ...this.usuario, configuracion: this.configuracion }
        localStorage.setItem('starkUsuarios', JSON.stringify(usuarios))
        
        // Actualizar sesión
        const sesion = JSON.parse(localStorage.getItem('starkSesion') || '{}')
        sesion.nombre = this.usuario.nombre
        sesion.foto = this.usuario.foto
        localStorage.setItem('starkSesion', JSON.stringify(sesion))
        
        this.$emit('usuario-actualizado', usuarios[index])
      }
    },
    formatearFecha(fecha) {
      if (!fecha) return 'No disponible'
      return new Date(fecha).toLocaleDateString('es-ES', {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      })
    }
  },
  mounted() {
    // Cargar configuración guardada
    if (this.usuario?.configuracion) {
      this.configuracion = { ...this.configuracion, ...this.usuario.configuracion }
    }
  }
}
</script>

<style scoped>
.perfil-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(10px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 3000;
  animation: fadeIn 0.3s ease-out;
}

.perfil-modal {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid #00ffff;
  border-radius: 25px;
  max-width: 1200px;
  width: 95%;
  max-height: 90vh;
  overflow: hidden;
  position: relative;
  animation: slideIn 0.4s ease-out;
  box-shadow: 0 0 50px rgba(0, 255, 255, 0.3);
}

.perfil-header {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
  padding: 2rem;
  border-bottom: 2px solid #00ffff;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
  overflow: hidden;
}

.perfil-header::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  animation: scan 3s infinite;
}

.header-content {
  position: relative;
  z-index: 2;
}

.perfil-titulo {
  font-size: 2.5rem;
  font-weight: 900;
  margin-bottom: 0.5rem;
  text-shadow: 0 0 20px #00ffff;
}

.stark-logo {
  color: #00ffff;
  text-shadow: 0 0 30px #00ffff;
}

.industries-text {
  color: #ff00ff;
  text-shadow: 0 0 30px #ff00ff;
}

.subtitle {
  font-size: 1.1rem;
  color: #888;
  font-weight: 400;
}

.btn-cerrar {
  background: linear-gradient(45deg, #ff0000, #cc0000);
  border: none;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 0 20px rgba(255, 0, 0, 0.3);
  position: relative;
  z-index: 2;
}

.btn-cerrar:hover {
  transform: scale(1.1);
  box-shadow: 0 0 30px rgba(255, 0, 0, 0.5);
}

.cerrar-icon {
  color: #fff;
  font-size: 1.5rem;
  font-weight: bold;
}

.perfil-content {
  display: grid;
  grid-template-columns: 300px 1fr;
  height: calc(90vh - 120px);
}

.perfil-sidebar {
  background: rgba(0, 255, 255, 0.05);
  border-right: 1px solid rgba(0, 255, 255, 0.3);
  padding: 2rem 1rem;
  overflow-y: auto;
}

.user-info {
  text-align: center;
  margin-bottom: 2rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid rgba(0, 255, 255, 0.3);
}

.user-avatar {
  position: relative;
  width: 100px;
  height: 100px;
  margin: 0 auto 1rem;
  border-radius: 50%;
  overflow: hidden;
  border: 3px solid #00ffff;
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
}

.user-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.user-avatar:hover .avatar-overlay {
  opacity: 1;
}

.btn-cambiar-foto {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #000;
  font-size: 1.2rem;
}

.user-nombre {
  font-size: 1.3rem;
  font-weight: 700;
  color: #00ffff;
  margin-bottom: 0.5rem;
  text-shadow: 0 0 10px #00ffff;
}

.user-empresa {
  color: #888;
  font-size: 0.9rem;
  margin-bottom: 0.3rem;
}

.user-email {
  color: #666;
  font-size: 0.8rem;
  margin-bottom: 1rem;
}

.user-status {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.status-dot {
  width: 8px;
  height: 8px;
  background: #00ff00;
  border-radius: 50%;
  box-shadow: 0 0 10px #00ff00;
}

.status-text {
  color: #00ff00;
  font-size: 0.8rem;
  font-weight: 500;
}

.perfil-nav {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.nav-item {
  background: transparent;
  border: none;
  border-radius: 10px;
  padding: 1rem;
  color: #888;
  font-family: 'Orbitron', monospace;
  font-weight: 500;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 1rem;
  transition: all 0.3s ease;
  text-align: left;
}

.nav-item:hover {
  background: rgba(0, 255, 255, 0.1);
  color: #00ffff;
}

.nav-item.nav-activo {
  background: linear-gradient(45deg, rgba(0, 255, 255, 0.2), rgba(0, 255, 255, 0.1));
  color: #00ffff;
  border: 1px solid rgba(0, 255, 255, 0.3);
}

.nav-icon {
  font-size: 1.2rem;
}

.nav-text {
  font-size: 0.9rem;
}

.perfil-main {
  padding: 2rem;
  overflow-y: auto;
}

.seccion-content {
  max-width: 800px;
}

.seccion-titulo {
  font-size: 2rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 15px #00ffff;
  margin-bottom: 2rem;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.info-label {
  color: #00ffff;
  font-weight: 600;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.info-input,
.info-select {
  background: rgba(0, 255, 255, 0.1);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1rem;
  color: #fff;
  font-family: 'Orbitron', monospace;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.info-input:focus,
.info-select:focus {
  outline: none;
  border-color: #00ffff;
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
}

.info-input::placeholder {
  color: #666;
}

.info-select option {
  background: #1a1a2e;
  color: #fff;
}

.btn-guardar,
.btn-cambiar-password,
.btn-guardar-config {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 15px;
  padding: 1rem 2rem;
  color: #000;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
  font-size: 1rem;
}

.btn-guardar:hover,
.btn-cambiar-password:hover,
.btn-guardar-config:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(0, 255, 255, 0.4);
}

.btn-glow {
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  transition: left 0.5s ease;
}

.btn-guardar:hover .btn-glow,
.btn-cambiar-password:hover .btn-glow,
.btn-guardar-config:hover .btn-glow {
  left: 100%;
}

.security-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  max-width: 500px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-label {
  color: #00ffff;
  font-weight: 600;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.form-input {
  background: rgba(0, 255, 255, 0.1);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 10px;
  padding: 1rem;
  color: #fff;
  font-family: 'Orbitron', monospace;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-input:focus {
  outline: none;
  border-color: #00ffff;
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
}

.password-input {
  position: relative;
}

.btn-toggle-password {
  position: absolute;
  right: 1rem;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  color: #00ffff;
  cursor: pointer;
  font-size: 1.2rem;
}

.compras-vacio {
  text-align: center;
  padding: 4rem 2rem;
  background: rgba(0, 255, 255, 0.05);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
}

.vacio-icon {
  font-size: 4rem;
  margin-bottom: 1rem;
  animation: pulse 2s infinite;
}

.vacio-titulo {
  font-size: 1.8rem;
  color: #00ffff;
  margin-bottom: 1rem;
  text-shadow: 0 0 15px #00ffff;
}

.vacio-descripcion {
  color: #888;
  font-size: 1rem;
}

.compras-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.compra-item {
  background: rgba(0, 255, 255, 0.05);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 1.5rem;
  transition: all 0.3s ease;
}

.compra-item:hover {
  border-color: #00ffff;
  box-shadow: 0 10px 30px rgba(0, 255, 255, 0.2);
}

.compra-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.compra-id {
  color: #00ffff;
  font-size: 1.2rem;
  font-weight: 700;
}

.compra-fecha {
  color: #888;
  font-size: 0.9rem;
}

.compra-productos {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 1rem;
}

.producto-compra {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  background: rgba(0, 255, 255, 0.1);
  border-radius: 10px;
}

.producto-imagen {
  width: 60px;
  height: 60px;
  border-radius: 10px;
  object-fit: cover;
}

.producto-info {
  flex: 1;
}

.producto-nombre {
  color: #fff;
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: 0.3rem;
}

.producto-cantidad,
.producto-precio {
  color: #888;
  font-size: 0.8rem;
}

.compra-total {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
  border-top: 1px solid rgba(0, 255, 255, 0.3);
}

.total-label {
  color: #888;
  font-weight: 500;
}

.total-valor {
  color: #00ffff;
  font-size: 1.3rem;
  font-weight: bold;
  text-shadow: 0 0 10px #00ffff;
}

.compra-estado {
  text-align: right;
}

.estado-badge {
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.estado-badge.completado {
  background: linear-gradient(45deg, #00ff00, #00aa00);
  color: #000;
}

.estado-badge.procesando {
  background: linear-gradient(45deg, #ffff00, #ffaa00);
  color: #000;
}

.estado-badge.pendiente {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  color: #000;
}

.asesor-card {
  background: rgba(0, 255, 255, 0.05);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 2rem;
  display: grid;
  grid-template-columns: auto 1fr auto;
  gap: 2rem;
  align-items: center;
  margin-bottom: 2rem;
}

.asesor-avatar {
  position: relative;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  overflow: hidden;
  border: 3px solid #00ffff;
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.5);
}

.asesor-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.asesor-status {
  position: absolute;
  bottom: 5px;
  right: 5px;
}

.status-dot.online {
  background: #00ff00;
  box-shadow: 0 0 10px #00ff00;
}

.asesor-info {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.asesor-nombre {
  font-size: 1.5rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 10px #00ffff;
}

.asesor-especialidad {
  color: #888;
  font-size: 1rem;
}

.asesor-email {
  color: #666;
  font-size: 0.9rem;
}

.asesor-stats {
  display: flex;
  gap: 2rem;
  margin-top: 1rem;
}

.stat-item {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}

.stat-label {
  color: #888;
  font-size: 0.8rem;
}

.stat-value {
  color: #00ffff;
  font-weight: bold;
  font-size: 1rem;
}

.asesor-actions {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.btn-contactar-asesor,
.btn-videollamada {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 10px;
  padding: 0.8rem 1.5rem;
  color: #000;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
  font-size: 0.9rem;
}

.btn-videollamada {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-contactar-asesor:hover,
.btn-videollamada:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 255, 255, 0.3);
}

.asesor-mensajes {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 15px;
  padding: 1.5rem;
}

.mensajes-titulo {
  color: #00ffff;
  font-size: 1.2rem;
  font-weight: 600;
  margin-bottom: 1rem;
}

.mensajes-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.mensaje-item {
  background: rgba(0, 255, 255, 0.1);
  border-radius: 10px;
  padding: 1rem;
}

.mensaje-header {
  margin-bottom: 0.5rem;
}

.mensaje-fecha {
  color: #888;
  font-size: 0.8rem;
}

.mensaje-contenido {
  color: #ccc;
  line-height: 1.4;
}

.config-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  margin-bottom: 2rem;
}

.config-item {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 15px;
  padding: 1.5rem;
}

.config-titulo {
  color: #00ffff;
  font-size: 1.2rem;
  font-weight: 600;
  margin-bottom: 1rem;
}

.config-options {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  cursor: pointer;
}

.checkbox-input {
  width: 18px;
  height: 18px;
  accent-color: #00ffff;
}

.checkbox-text {
  color: #ccc;
  font-size: 0.9rem;
}

.perfil-border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: 25px;
  background: linear-gradient(45deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.perfil-modal:hover .perfil-border {
  opacity: 1;
}

/* Transiciones */
.perfil-fade-enter-active,
.perfil-fade-leave-active {
  transition: opacity 0.3s ease;
}

.perfil-fade-enter-from,
.perfil-fade-leave-to {
  opacity: 0;
}

@media (max-width: 1024px) {
  .perfil-content {
    grid-template-columns: 1fr;
  }
  
  .perfil-sidebar {
    border-right: none;
    border-bottom: 1px solid rgba(0, 255, 255, 0.3);
  }
  
  .perfil-nav {
    flex-direction: row;
    overflow-x: auto;
  }
  
  .nav-item {
    white-space: nowrap;
  }
}

@media (max-width: 768px) {
  .perfil-modal {
    width: 95%;
    margin: 1rem;
  }
  
  .perfil-header {
    padding: 1rem;
  }
  
  .perfil-titulo {
    font-size: 2rem;
  }
  
  .perfil-main {
    padding: 1rem;
  }
  
  .info-grid {
    grid-template-columns: 1fr;
  }
  
  .asesor-card {
    grid-template-columns: 1fr;
    text-align: center;
  }
  
  .asesor-actions {
    flex-direction: row;
  }
}
</style>
