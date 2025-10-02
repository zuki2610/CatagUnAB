<template>
  <div id="app">
    <header class="stark-header">
      <div class="header-content">
        <h1 class="stark-title">
          <span class="stark-logo">STARK</span>
          <span class="industries-text">INDUSTRIES</span>
        </h1>
        <p class="subtitle">Catálogo de Tecnología Avanzada</p>
        <div class="neural-network">
          <div class="neural-line"></div>
          <div class="neural-dot"></div>
          <div class="neural-line"></div>
        </div>
      </div>
      
      <!-- Navbar de Autenticación -->
      <nav class="auth-navbar">
        <div v-if="!usuarioLogueado" class="auth-buttons">
          <div class="guest-message">
            <span class="guest-text">¡Bienvenido a Stark Industries!</span>
            <span class="guest-note">Explora nuestra tecnología avanzada • Regístrate para beneficios exclusivos</span>
          </div>
          <div class="auth-options">
            <button class="btn-login" @click="mostrarLogin = true">
              <span class="btn-text">INICIAR SESIÓN</span>
              <div class="btn-glow"></div>
            </button>
            <button class="btn-registro" @click="mostrarRegistro = true">
              <span class="btn-text">REGISTRARSE</span>
              <div class="btn-glow"></div>
            </button>
          </div>
        </div>
        
        <div v-else class="user-menu">
          <div class="user-info">
            <img :src="sesion.foto" :alt="sesion.nombre" class="user-avatar" />
            <div class="user-details">
              <span class="user-nombre">{{ sesion.nombre }}</span>
              <span class="user-empresa">{{ sesion.empresa }}</span>
            </div>
          </div>
          <div class="user-actions">
            <button class="btn-perfil" @click="abrirPerfil">
              <span class="btn-text">MI PERFIL</span>
              <div class="btn-glow"></div>
            </button>
            <button class="btn-logout" @click="cerrarSesion">
              <span class="btn-text">CERRAR SESIÓN</span>
              <div class="btn-glow"></div>
            </button>
          </div>
        </div>
      </nav>
    </header>
    
    <main class="main-content">
      <CatalogoProductos 
        :usuario="sesion" 
        @mostrar-login="mostrarLogin = true"
        @mostrar-registro="mostrarRegistro = true"
      />
    </main>
    
    <footer class="stark-footer">
      <p>&copy; 2024 Stark Industries. Todos los derechos reservados.</p>
      <div class="footer-glow"></div>
    </footer>

    <!-- Modales de Autenticación -->
    <LoginModal 
      :mostrar="mostrarLogin" 
      @cerrar="cerrarLogin"
      @cambiar-a-registro="cambiarARegistro"
      @login-exitoso="manejarLoginExitoso"
    />
    
    <RegistroModal 
      :mostrar="mostrarRegistro" 
      @cerrar="cerrarRegistro"
      @cambiar-a-login="cambiarALogin"
      @registro-exitoso="manejarRegistroExitoso"
    />

    <!-- Modal de Perfil -->
    <PerfilUsuario 
      v-if="usuarioCompleto"
      :mostrar="mostrarPerfil"
      :usuario="usuarioCompleto"
      @cerrar="cerrarPerfil"
      @usuario-actualizado="manejarUsuarioActualizado"
    />
  </div>
</template>

<script>
import CatalogoProductos from './components/CatalogoProductos.vue'
import LoginModal from './components/LoginModal.vue'
import RegistroModal from './components/RegistroModal.vue'
import PerfilUsuario from './components/PerfilUsuario.vue'

export default {
  name: 'App',
  components: {
    CatalogoProductos,
    LoginModal,
    RegistroModal,
    PerfilUsuario
  },
  data() {
    return {
      mostrarLogin: false,
      mostrarRegistro: false,
      mostrarPerfil: false,
      sesion: null,
      usuarioCompleto: null
    }
  },
  computed: {
    usuarioLogueado() {
      return this.sesion !== null
    }
  },
  methods: {
    cambiarARegistro() {
      this.mostrarLogin = false
      this.mostrarRegistro = true
    },
    cambiarALogin() {
      this.mostrarRegistro = false
      this.mostrarLogin = true
    },
    cerrarLogin() {
      console.log('🔴 Cerrando login desde App.vue')
      this.mostrarLogin = false
    },
    cerrarRegistro() {
      console.log('🔴 Cerrando registro desde App.vue')
      this.mostrarRegistro = false
    },
    manejarLoginExitoso(sesionData) {
      this.sesion = sesionData
      this.cargarUsuarioCompleto()
      console.log('✅ Usuario logueado:', sesionData.nombre)
    },
    manejarRegistroExitoso(sesionData) {
      this.sesion = sesionData
      this.cargarUsuarioCompleto()
      console.log('✅ Usuario registrado:', sesionData.nombre)
    },
    manejarUsuarioActualizado(usuario) {
      this.usuarioCompleto = usuario
      this.sesion.nombre = usuario.nombre
      this.sesion.foto = usuario.foto
      console.log('✅ Usuario actualizado:', usuario.nombre)
    },
    abrirPerfil() {
      console.log('🟢 Abriendo perfil de usuario')
      this.mostrarPerfil = true
    },
    cerrarPerfil() {
      console.log('🔴 Cerrando perfil desde App.vue')
      this.mostrarPerfil = false
    },
    cerrarSesion() {
      localStorage.removeItem('starkSesion')
      this.sesion = null
      this.usuarioCompleto = null
      console.log('👋 Sesión cerrada')
    },
    cargarUsuarioCompleto() {
      if (this.sesion) {
        const usuarios = JSON.parse(localStorage.getItem('starkUsuarios') || '[]')
        this.usuarioCompleto = usuarios.find(u => u.id === this.sesion.id)
      }
    },
    verificarSesion() {
      const sesionGuardada = localStorage.getItem('starkSesion')
      if (sesionGuardada) {
        const sesionData = JSON.parse(sesionGuardada)
        // Solo restaurar si el usuario marcó "recordar sesión"
        if (sesionData.recordarSesion) {
          this.sesion = sesionData
          this.cargarUsuarioCompleto()
          console.log('🔄 Sesión restaurada:', this.sesion.nombre)
        } else {
          // Limpiar sesión si no se marcó recordar
          localStorage.removeItem('starkSesion')
          console.log('🧹 Sesión no recordada, limpiada')
        }
      }
    },
    limpiarSesionActual() {
      localStorage.removeItem('starkSesion')
      this.sesion = null
      this.usuarioCompleto = null
      console.log('🧹 Sesión actual limpiada')
    }
  },
  mounted() {
    this.verificarSesion()
  }
}
</script>

<style scoped>
#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.stark-header {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
  padding: 2rem 0 1rem;
  text-align: center;
  border-bottom: 2px solid #00ffff;
  position: relative;
  overflow: hidden;
}

.stark-header::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  animation: scan 3s infinite;
}

@keyframes scan {
  0% { left: -100%; }
  100% { left: 100%; }
}

.header-content {
  position: relative;
  z-index: 2;
}

.stark-title {
  font-size: 3.5rem;
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
  font-size: 1.2rem;
  color: #888;
  margin-bottom: 1rem;
  font-weight: 400;
}

.neural-network {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}

.neural-line {
  width: 50px;
  height: 2px;
  background: linear-gradient(90deg, #00ffff, #ff00ff);
  animation: pulse 2s infinite;
}

.neural-dot {
  width: 8px;
  height: 8px;
  background: #00ffff;
  border-radius: 50%;
  box-shadow: 0 0 10px #00ffff;
  animation: glow 2s infinite;
}

.main-content {
  flex: 1;
  padding: 2rem;
}

.stark-footer {
  background: linear-gradient(135deg, #1a1a2e 0%, #0a0a0a 100%);
  padding: 1rem;
  text-align: center;
  border-top: 1px solid #00ffff;
  position: relative;
}

.footer-glow {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(90deg, transparent, #00ffff, transparent);
  animation: scan 2s infinite reverse;
}

.auth-navbar {
  position: relative;
  z-index: 2;
  padding: 1rem 2rem;
  border-top: 1px solid rgba(0, 255, 255, 0.3);
}

.auth-buttons {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.guest-message {
  text-align: center;
  margin-bottom: 0.5rem;
}

.guest-text {
  color: #00ffff;
  font-weight: bold;
  font-size: 1.1rem;
  text-shadow: 0 0 15px #00ffff;
  display: block;
  margin-bottom: 0.3rem;
}

.guest-note {
  color: #ccc;
  font-size: 0.9rem;
  display: block;
  line-height: 1.3;
}

.auth-options {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.btn-login,
.btn-registro {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 15px;
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

.btn-registro {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-login:hover,
.btn-registro:hover {
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

.btn-login:hover .btn-glow,
.btn-registro:hover .btn-glow {
  left: 100%;
}

.user-menu {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 2rem;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.user-avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: 2px solid #00ffff;
  box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
  object-fit: cover;
}

.user-details {
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.user-nombre {
  color: #00ffff;
  font-weight: bold;
  font-size: 1rem;
  text-shadow: 0 0 10px #00ffff;
}

.user-empresa {
  color: #888;
  font-size: 0.8rem;
}

.user-actions {
  display: flex;
  gap: 1rem;
}

.btn-perfil,
.btn-logout {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 10px;
  padding: 0.6rem 1.2rem;
  color: #000;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
  font-size: 0.8rem;
}

.btn-logout {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
}

.btn-perfil:hover,
.btn-logout:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 255, 255, 0.3);
}

@media (max-width: 768px) {
  .stark-title {
    font-size: 2.2rem;
    line-height: 1.1;
  }
  
  .stark-logo {
    display: block;
    margin-bottom: 0.2rem;
  }
  
  .industries-text {
    display: block;
  }
  
  .subtitle {
    font-size: 1rem;
    margin-bottom: 0.8rem;
  }
  
  .main-content {
    padding: 1rem;
  }
  
  .auth-navbar {
    padding: 1rem;
  }
  
  .auth-buttons {
    gap: 0.8rem;
  }
  
  .guest-message {
    margin-bottom: 0.3rem;
  }
  
  .guest-text {
    font-size: 1rem;
  }
  
  .guest-note {
    font-size: 0.8rem;
  }
  
  .auth-options {
    flex-direction: column;
    gap: 0.8rem;
    width: 100%;
  }
  
  .btn-login,
  .btn-registro {
    width: 100%;
    font-size: 0.8rem;
    padding: 0.7rem 1.2rem;
  }
  
  .user-menu {
    flex-direction: column;
    gap: 1rem;
  }
  
  .user-actions {
    flex-direction: column;
    width: 100%;
  }
  
  .btn-perfil,
  .btn-logout {
    width: 100%;
    font-size: 0.7rem;
    padding: 0.5rem 1rem;
  }
}

@media (max-width: 480px) {
  .stark-header {
    padding: 1rem 0 0.8rem;
  }
  
  .stark-title {
    font-size: 1.8rem;
    line-height: 1.2;
  }
  
  .stark-logo {
    display: block;
    margin-bottom: 0.1rem;
  }
  
  .industries-text {
    display: block;
  }
  
  .subtitle {
    font-size: 0.9rem;
    margin-bottom: 0.6rem;
  }
  
  .guest-text {
    font-size: 0.9rem;
  }
  
  .guest-note {
    font-size: 0.75rem;
    line-height: 1.2;
  }
  
  .btn-login,
  .btn-registro {
    font-size: 0.75rem;
    padding: 0.6rem 1rem;
  }
  
  .btn-perfil,
  .btn-logout {
    font-size: 0.65rem;
    padding: 0.4rem 0.8rem;
  }
  
  .user-nombre {
    font-size: 0.9rem;
  }
  
  .user-empresa {
    font-size: 0.7rem;
  }
  
  .main-content {
    padding: 0.8rem;
  }
  
  .auth-navbar {
    padding: 0.8rem;
  }
}

@media (max-width: 360px) {
  .stark-title {
    font-size: 1.6rem;
    line-height: 1.3;
  }
  
  .subtitle {
    font-size: 0.8rem;
  }
  
  .guest-text {
    font-size: 0.8rem;
  }
  
  .guest-note {
    font-size: 0.7rem;
  }
  
  .btn-login,
  .btn-registro {
    font-size: 0.7rem;
    padding: 0.5rem 0.8rem;
  }
  
  .main-content {
    padding: 0.6rem;
  }
  
  .auth-navbar {
    padding: 0.6rem;
  }
}
</style>
