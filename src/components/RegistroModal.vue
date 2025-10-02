<template>
  <div v-if="mostrar" class="registro-overlay" @click="cerrarModal">
    <div class="registro-modal" @click.stop>
      <div class="modal-header">
        <h2 class="modal-titulo">
          <span class="stark-logo">STARK</span>
          <span class="industries-text">REGISTRO</span>
        </h2>
        <button class="btn-cerrar" @click="cerrarModal">
          <span class="cerrar-icon">×</span>
        </button>
      </div>

      <div class="modal-content">
        <div class="registro-form">
          <div class="form-row">
            <div class="form-group">
              <label class="form-label">Nombre Completo</label>
              <input 
                v-model="formulario.nombre" 
                type="text" 
                class="form-input"
                placeholder="Tony Stark"
                :class="{ 'input-error': errores.nombre }"
              />
              <span v-if="errores.nombre" class="error-message">{{ errores.nombre }}</span>
            </div>

            <div class="form-group">
              <label class="form-label">Empresa</label>
              <input 
                v-model="formulario.empresa" 
                type="text" 
                class="form-input"
                placeholder="Stark Industries"
                :class="{ 'input-error': errores.empresa }"
              />
              <span v-if="errores.empresa" class="error-message">{{ errores.empresa }}</span>
            </div>
          </div>

          <div class="form-group">
            <label class="form-label">Email Corporativo</label>
            <input 
              v-model="formulario.email" 
              type="email" 
              class="form-input"
              placeholder="tony.stark@starkindustries.com"
              :class="{ 'input-error': errores.email }"
            />
            <span v-if="errores.email" class="error-message">{{ errores.email }}</span>
          </div>

          <div class="form-row">
            <div class="form-group">
              <label class="form-label">Contraseña</label>
              <div class="password-input">
                <input 
                  v-model="formulario.password" 
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

            <div class="form-group">
              <label class="form-label">Confirmar Contraseña</label>
              <div class="password-input">
                <input 
                  v-model="formulario.confirmarPassword" 
                  :type="mostrarConfirmarPassword ? 'text' : 'password'"
                  class="form-input"
                  placeholder="••••••••"
                  :class="{ 'input-error': errores.confirmarPassword }"
                />
                <button class="btn-toggle-password" @click="mostrarConfirmarPassword = !mostrarConfirmarPassword">
                  <span class="password-icon">{{ mostrarConfirmarPassword ? '👁️' : '👁️‍🗨️' }}</span>
                </button>
              </div>
              <span v-if="errores.confirmarPassword" class="error-message">{{ errores.confirmarPassword }}</span>
            </div>
          </div>

          <div class="form-group">
            <label class="form-label">Teléfono</label>
            <input 
              v-model="formulario.telefono" 
              type="tel" 
              class="form-input"
              placeholder="+1 (555) 123-4567"
              :class="{ 'input-error': errores.telefono }"
            />
            <span v-if="errores.telefono" class="error-message">{{ errores.telefono }}</span>
          </div>

          <div class="form-group">
            <label class="form-label">Área de Interés</label>
            <select v-model="formulario.areaInteres" class="form-select">
              <option value="">Seleccionar área</option>
              <option value="tecnologia">Tecnología Avanzada</option>
              <option value="defensa">Sistemas de Defensa</option>
              <option value="energia">Energía Limpia</option>
              <option value="investigacion">Investigación y Desarrollo</option>
              <option value="consultoria">Consultoría Tecnológica</option>
            </select>
          </div>

          <div class="form-options">
            <label class="checkbox-label">
              <input type="checkbox" v-model="formulario.aceptarTerminos" class="checkbox-input">
              <span class="checkbox-text">Acepto los términos y condiciones</span>
            </label>
          </div>

          <div class="form-options">
            <label class="checkbox-label">
              <input type="checkbox" v-model="formulario.recibirNoticias" class="checkbox-input">
              <span class="checkbox-text">Recibir noticias sobre nuevos productos</span>
            </label>
          </div>

          <button class="btn-registro" @click="registrarUsuario" :disabled="cargando">
            <span class="btn-text">{{ cargando ? 'REGISTRANDO...' : 'CREAR CUENTA' }}</span>
            <div class="btn-glow"></div>
          </button>

          <div class="login-divider">
            <span class="divider-text">O</span>
            <div class="divider-line"></div>
          </div>

          <button class="btn-login" @click="cambiarAModalLogin">
            <span class="btn-text">YA TENGO CUENTA</span>
            <div class="btn-glow"></div>
          </button>
        </div>

        <div class="registro-info">
          <h3 class="info-titulo">Únete a Stark Industries</h3>
          <p class="info-descripcion">
            Crea tu cuenta corporativa para acceder a tecnología de vanguardia, 
            asesoría personalizada y productos exclusivos.
          </p>
          <div class="info-benefits">
            <div class="benefit-item">
              <span class="benefit-icon">🎯</span>
              <div class="benefit-content">
                <h4>Asesor Personal</h4>
                <p>Asignación automática de un asesor especializado</p>
              </div>
            </div>
            <div class="benefit-item">
              <span class="benefit-icon">💎</span>
              <div class="benefit-content">
                <h4>Productos Exclusivos</h4>
                <p>Acceso anticipado a nuevas tecnologías</p>
              </div>
            </div>
            <div class="benefit-item">
              <span class="benefit-icon">🛡️</span>
              <div class="benefit-content">
                <h4>Soporte Premium</h4>
                <p>Soporte técnico 24/7 con J.A.R.V.I.S.</p>
              </div>
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
  name: 'RegistroModal',
  props: {
    mostrar: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      formulario: {
        nombre: '',
        empresa: '',
        email: '',
        password: '',
        confirmarPassword: '',
        telefono: '',
        areaInteres: '',
        aceptarTerminos: false,
        recibirNoticias: false
      },
      mostrarPassword: false,
      mostrarConfirmarPassword: false,
      cargando: false,
      errores: {}
    }
  },
  methods: {
    cerrarModal() {
      console.log('🔴 Cerrando modal de registro')
      this.$emit('cerrar')
    },
    cambiarAModalLogin() {
      this.$emit('cambiar-a-login')
    },
    async registrarUsuario() {
      this.errores = {}
      this.cargando = true

      // Validaciones
      if (!this.formulario.nombre.trim()) {
        this.errores.nombre = 'El nombre es requerido'
      }

      if (!this.formulario.empresa.trim()) {
        this.errores.empresa = 'La empresa es requerida'
      }

      if (!this.formulario.email) {
        this.errores.email = 'El email es requerido'
      } else if (!this.validarEmail(this.formulario.email)) {
        this.errores.email = 'Formato de email inválido'
      }

      if (!this.formulario.password) {
        this.errores.password = 'La contraseña es requerida'
      } else if (this.formulario.password.length < 6) {
        this.errores.password = 'La contraseña debe tener al menos 6 caracteres'
      }

      if (!this.formulario.confirmarPassword) {
        this.errores.confirmarPassword = 'Confirma tu contraseña'
      } else if (this.formulario.password !== this.formulario.confirmarPassword) {
        this.errores.confirmarPassword = 'Las contraseñas no coinciden'
      }

      if (!this.formulario.telefono.trim()) {
        this.errores.telefono = 'El teléfono es requerido'
      }

      if (!this.formulario.aceptarTerminos) {
        this.errores.terminos = 'Debes aceptar los términos y condiciones'
      }

      if (Object.keys(this.errores).length > 0) {
        this.cargando = false
        return
      }

      try {
        // Simular registro
        await this.simularRegistro()
        
        // Obtener usuarios existentes
        const usuarios = JSON.parse(localStorage.getItem('starkUsuarios') || '[]')
        
        // Verificar si el email ya existe
        const usuarioExistente = usuarios.find(u => u.email === this.formulario.email)
        if (usuarioExistente) {
          this.errores.email = 'Este email ya está registrado'
          this.cargando = false
          return
        }

        // Crear nuevo usuario
        const nuevoUsuario = {
          id: Date.now().toString(),
          nombre: this.formulario.nombre,
          empresa: this.formulario.empresa,
          email: this.formulario.email,
          password: this.formulario.password,
          telefono: this.formulario.telefono,
          areaInteres: this.formulario.areaInteres,
          foto: 'https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?w=150&h=150&fit=crop&crop=face',
          fechaRegistro: new Date().toISOString(),
          recibirNoticias: this.formulario.recibirNoticias,
          compras: [],
          asesor: this.asignarAsesor(this.formulario.areaInteres)
        }

        // Guardar usuario
        usuarios.push(nuevoUsuario)
        localStorage.setItem('starkUsuarios', JSON.stringify(usuarios))

        // Crear sesión automática
        const sesion = {
          id: nuevoUsuario.id,
          email: nuevoUsuario.email,
          nombre: nuevoUsuario.nombre,
          foto: nuevoUsuario.foto,
          fechaLogin: new Date().toISOString(),
          recordarSesion: true
        }
        
        localStorage.setItem('starkSesion', JSON.stringify(sesion))
        
        this.$emit('registro-exitoso', sesion)
        this.cerrarModal()
        
        console.log('✅ Registro exitoso:', nuevoUsuario.nombre)
      } catch (error) {
        console.error('Error en registro:', error)
        this.errores.general = 'Error al crear la cuenta'
      } finally {
        this.cargando = false
      }
    },
    validarEmail(email) {
      const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return regex.test(email)
    },
    simularRegistro() {
      return new Promise(resolve => {
        setTimeout(resolve, 2000)
      })
    },
    asignarAsesor(areaInteres) {
      const asesores = {
        tecnologia: { nombre: 'Dr. Bruce Banner', especialidad: 'Tecnología Avanzada', email: 'bruce.banner@starkindustries.com' },
        defensa: { nombre: 'Col. James Rhodes', especialidad: 'Sistemas de Defensa', email: 'james.rhodes@starkindustries.com' },
        energia: { nombre: 'Dr. Helen Cho', especialidad: 'Energía Limpia', email: 'helen.cho@starkindustries.com' },
        investigacion: { nombre: 'Dr. Erik Selvig', especialidad: 'Investigación y Desarrollo', email: 'erik.selvig@starkindustries.com' },
        consultoria: { nombre: 'Pepper Potts', especialidad: 'Consultoría Tecnológica', email: 'pepper.potts@starkindustries.com' }
      }
      
      return asesores[areaInteres] || asesores.tecnologia
    }
  }
}
</script>

<style scoped>
.registro-overlay {
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

.registro-modal {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid #ff00ff;
  border-radius: 25px;
  max-width: 900px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  animation: slideIn 0.4s ease-out;
  box-shadow: 0 0 50px rgba(255, 0, 255, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem 2rem 1rem;
  border-bottom: 1px solid rgba(255, 0, 255, 0.3);
}

.modal-titulo {
  font-size: 2.2rem;
  font-weight: 700;
  margin: 0;
}

.stark-logo {
  color: #ff00ff;
  text-shadow: 0 0 20px #ff00ff;
}

.industries-text {
  color: #00ffff;
  text-shadow: 0 0 20px #00ffff;
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

.registro-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-label {
  color: #ff00ff;
  font-weight: 600;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.form-input,
.form-select {
  background: rgba(255, 0, 255, 0.1);
  border: 2px solid rgba(255, 0, 255, 0.3);
  border-radius: 10px;
  padding: 1rem;
  color: #fff;
  font-family: 'Orbitron', monospace;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-input:focus,
.form-select:focus {
  outline: none;
  border-color: #ff00ff;
  box-shadow: 0 0 20px rgba(255, 0, 255, 0.3);
}

.form-input::placeholder {
  color: #666;
}

.form-select option {
  background: #1a1a2e;
  color: #fff;
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
  color: #ff00ff;
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
  align-items: center;
  gap: 0.5rem;
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
  accent-color: #ff00ff;
}

.checkbox-text {
  color: #888;
  font-size: 0.9rem;
}

.btn-registro,
.btn-login {
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

.btn-registro {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-login {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-registro:hover:not(:disabled),
.btn-login:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(255, 0, 255, 0.4);
}

.btn-registro:disabled {
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

.btn-registro:hover .btn-glow,
.btn-login:hover .btn-glow {
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
  background: linear-gradient(90deg, transparent, #ff00ff, transparent);
}

.registro-info {
  background: rgba(255, 0, 255, 0.05);
  border: 1px solid rgba(255, 0, 255, 0.2);
  border-radius: 15px;
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.info-titulo {
  font-size: 1.3rem;
  font-weight: 600;
  color: #ff00ff;
  text-shadow: 0 0 10px #ff00ff;
  margin-bottom: 0.5rem;
}

.info-descripcion {
  color: #ccc;
  line-height: 1.6;
  font-size: 0.9rem;
}

.info-benefits {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-top: 1rem;
}

.benefit-item {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  padding: 1rem;
  background: rgba(255, 0, 255, 0.1);
  border-radius: 10px;
  border: 1px solid rgba(255, 0, 255, 0.2);
}

.benefit-icon {
  font-size: 1.5rem;
  margin-top: 0.2rem;
}

.benefit-content h4 {
  color: #ff00ff;
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: 0.3rem;
}

.benefit-content p {
  color: #888;
  font-size: 0.8rem;
  line-height: 1.4;
}

.modal-border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: 25px;
  background: linear-gradient(45deg, transparent, rgba(255, 0, 255, 0.1), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.registro-modal:hover .modal-border {
  opacity: 1;
}

@media (max-width: 768px) {
  .registro-modal {
    width: 95%;
    margin: 1rem;
  }
  
  .modal-content {
    grid-template-columns: 1fr;
    gap: 1.5rem;
    padding: 1.5rem;
  }
  
  .form-row {
    grid-template-columns: 1fr;
  }
  
  .modal-header {
    padding: 1.5rem 1.5rem 1rem;
  }
  
  .modal-titulo {
    font-size: 1.8rem;
  }
}
</style>
