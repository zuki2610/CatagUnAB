<template>
  <div v-if="mostrar" class="login-overlay" @click="cerrarModal">
    <div class="login-modal" @click.stop>
      <div class="modal-header">
        <h2 class="modal-titulo">
          <span class="stark-logo">STARK</span>
          <span class="industries-text">LOGIN</span>
        </h2>
        <button class="btn-cerrar" @click="cerrarModal">
          <span class="cerrar-icon">×</span>
        </button>
      </div>

      <div class="modal-content">
        <div class="login-form">
          <div class="form-group">
            <label class="form-label">Email Corporativo</label>
            <input 
              v-model="email" 
              type="email" 
              class="form-input"
              placeholder="tony.stark@starkindustries.com"
              :class="{ 'input-error': errores.email }"
            />
            <span v-if="errores.email" class="error-message">{{ errores.email }}</span>
          </div>

          <div class="form-group">
            <label class="form-label">Contraseña</label>
            <div class="password-input">
              <input 
                v-model="password" 
                :type="mostrarPassword ? 'text' : 'password'"
                class="form-input"
                placeholder="••••••••"
                :class="{ 'input-error': errores.password }"
              />
              <button class="btn-toggle-password" @click="mostrarPassword = !mostrarPassword">
                <span class="password-icon">{{ mostrarPassword ? '👁️' : '👁️‍🗨️' }}</span>
              </button>
            </div>
            <span v-if="errores.password" class="error-message">{{ errores.password }}</span>
          </div>

          <div class="form-options">
            <label class="checkbox-label">
              <input type="checkbox" v-model="recordarSesion" class="checkbox-input">
              <span class="checkbox-text">Recordar sesión</span>
            </label>
            <a href="#" class="forgot-password">¿Olvidaste tu contraseña?</a>
          </div>

          <button class="btn-login" @click="iniciarSesion" :disabled="cargando">
            <span class="btn-text">{{ cargando ? 'INICIANDO...' : 'INICIAR SESIÓN' }}</span>
            <div class="btn-glow"></div>
          </button>

          <div class="login-divider">
            <span class="divider-text">O</span>
            <div class="divider-line"></div>
          </div>

          <button class="btn-registro" @click="cambiarAModalRegistro">
            <span class="btn-text">CREAR CUENTA NUEVA</span>
            <div class="btn-glow"></div>
          </button>
        </div>

        <div class="login-info">
          <h3 class="info-titulo">Acceso Corporativo</h3>
          <p class="info-descripcion">
            Accede a tu cuenta de Stark Industries para gestionar tus compras, 
            contactar con tu asesor personal y acceder a tecnología exclusiva.
          </p>
          <div class="info-features">
            <div class="feature-item">
              <span class="feature-icon">🛒</span>
              <span>Historial de compras</span>
            </div>
            <div class="feature-item">
              <span class="feature-icon">👨‍💼</span>
              <span>Asesor personal</span>
            </div>
            <div class="feature-item">
              <span class="feature-icon">⚡</span>
              <span>Tecnología exclusiva</span>
            </div>
          </div>
        </div>
      </div>

      <div class="modal-border"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LoginModal',
  props: {
    mostrar: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      email: '',
      password: '',
      recordarSesion: false,
      mostrarPassword: false,
      cargando: false,
      errores: {}
    }
  },
  methods: {
    cerrarModal() {
      console.log('🔴 Cerrando modal de login')
      this.$emit('cerrar')
    },
    cambiarAModalRegistro() {
      this.$emit('cambiar-a-registro')
    },
    async iniciarSesion() {
      this.errores = {}
      this.cargando = true

      // Validaciones
      if (!this.email) {
        this.errores.email = 'El email es requerido'
      } else if (!this.validarEmail(this.email)) {
        this.errores.email = 'Formato de email inválido'
      }

      if (!this.password) {
        this.errores.password = 'La contraseña es requerida'
      } else if (this.password.length < 6) {
        this.errores.password = 'La contraseña debe tener al menos 6 caracteres'
      }

      if (Object.keys(this.errores).length > 0) {
        this.cargando = false
        return
      }

      try {
        // Simular autenticación
        await this.simularLogin()
        
        // Obtener usuarios del localStorage
        const usuarios = JSON.parse(localStorage.getItem('starkUsuarios') || '[]')
        const usuario = usuarios.find(u => u.email === this.email && u.password === this.password)
        
        if (usuario) {
          // Crear sesión
          const sesion = {
            id: usuario.id,
            email: usuario.email,
            nombre: usuario.nombre,
            foto: usuario.foto,
            fechaLogin: new Date().toISOString(),
            recordarSesion: this.recordarSesion
          }
          
          localStorage.setItem('starkSesion', JSON.stringify(sesion))
          
          this.$emit('login-exitoso', sesion)
          this.cerrarModal()
          
          console.log('✅ Login exitoso:', usuario.nombre)
        } else {
          this.errores.email = 'Credenciales incorrectas'
          this.errores.password = 'Verifica tu email y contraseña'
        }
      } catch (error) {
        console.error('Error en login:', error)
        this.errores.general = 'Error al iniciar sesión'
      } finally {
        this.cargando = false
      }
    },
    validarEmail(email) {
      const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return regex.test(email)
    },
    simularLogin() {
      return new Promise(resolve => {
        setTimeout(resolve, 1500)
      })
    }
  }
}
</script>

<style scoped>
.login-overlay {
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

.login-modal {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid #00ffff;
  border-radius: 25px;
  max-width: 800px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  animation: slideIn 0.4s ease-out;
  box-shadow: 0 0 50px rgba(0, 255, 255, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem 2rem 1rem;
  border-bottom: 1px solid rgba(0, 255, 255, 0.3);
}

.modal-titulo {
  font-size: 2.2rem;
  font-weight: 700;
  margin: 0;
}

.stark-logo {
  color: #00ffff;
  text-shadow: 0 0 20px #00ffff;
}

.industries-text {
  color: #ff00ff;
  text-shadow: 0 0 20px #ff00ff;
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

.modal-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  padding: 2rem;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
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

.form-input::placeholder {
  color: #666;
}

.input-error {
  border-color: #ff0000 !important;
  box-shadow: 0 0 20px rgba(255, 0, 0, 0.3) !important;
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

.error-message {
  color: #ff0000;
  font-size: 0.8rem;
  font-weight: 500;
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}

.checkbox-input {
  width: 18px;
  height: 18px;
  accent-color: #00ffff;
}

.checkbox-text {
  color: #888;
  font-size: 0.9rem;
}

.forgot-password {
  color: #00ffff;
  text-decoration: none;
  font-size: 0.9rem;
  transition: color 0.3s ease;
}

.forgot-password:hover {
  color: #ff00ff;
}

.btn-login,
.btn-registro {
  padding: 1rem 1.5rem;
  border: none;
  border-radius: 15px;
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

.btn-login {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-registro {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-login:hover:not(:disabled),
.btn-registro:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(0, 255, 255, 0.4);
}

.btn-login:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  transform: none;
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

.login-divider {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin: 1rem 0;
}

.divider-text {
  color: #888;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.divider-line {
  flex: 1;
  height: 1px;
  background: linear-gradient(90deg, transparent, #00ffff, transparent);
}

.login-info {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.2);
  border-radius: 15px;
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.info-titulo {
  font-size: 1.3rem;
  font-weight: 600;
  color: #00ffff;
  text-shadow: 0 0 10px #00ffff;
  margin-bottom: 0.5rem;
}

.info-descripcion {
  color: #ccc;
  line-height: 1.6;
  font-size: 0.9rem;
}

.info-features {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  margin-top: 1rem;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 0.8rem;
  background: rgba(0, 255, 255, 0.1);
  border-radius: 10px;
  border: 1px solid rgba(0, 255, 255, 0.2);
}

.feature-icon {
  font-size: 1.2rem;
}

.modal-border {
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

.login-modal:hover .modal-border {
  opacity: 1;
}

@media (max-width: 768px) {
  .login-modal {
    width: 95%;
    margin: 1rem;
  }
  
  .modal-content {
    grid-template-columns: 1fr;
    gap: 1.5rem;
    padding: 1.5rem;
  }
  
  .modal-header {
    padding: 1.5rem 1.5rem 1rem;
  }
  
  .modal-titulo {
    font-size: 1.8rem;
  }
}
</style>
