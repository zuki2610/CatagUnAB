<template>
  <Transition name="cart-slide">
    <div v-if="mostrar" class="carrito-lateral-overlay" @click="cerrarCarrito">
      <div class="carrito-lateral" @click.stop>
        <div class="carrito-header">
          <h2 class="carrito-titulo">
            <span class="carrito-icon">🛒</span>
            <span class="carrito-text">MI CARRITO</span>
          </h2>
          <button class="btn-cerrar" @click="cerrarCarrito">
            <span class="cerrar-icon">×</span>
          </button>
        </div>

        <div class="carrito-content">
          <div v-if="carrito.length === 0" class="carrito-vacio">
            <div class="vacio-icon">🛒</div>
            <h3 class="vacio-titulo">Tu carrito está vacío</h3>
            <p class="vacio-descripcion">Agrega algunos productos increíbles de Stark Industries</p>
          </div>

          <div v-else class="carrito-items">
            <div 
              v-for="(producto, index) in carrito" 
              :key="`${producto.id}-${index}`"
              class="carrito-item"
            >
              <div class="item-imagen">
                <img :src="producto.imagen" :alt="producto.nombre" />
              </div>
              
              <div class="item-info">
                <h4 class="item-nombre">{{ producto.nombre }}</h4>
                <p class="item-precio">${{ producto.precio.toLocaleString() }}</p>
              </div>

              <div class="item-controls">
                <div class="cantidad-control">
                  <button class="btn-cantidad" @click="decrementarCantidad(index)" :disabled="cantidades[index] <= 1">
                    <span class="cantidad-icon">−</span>
                  </button>
                  <span class="cantidad-valor">{{ cantidades[index] }}</span>
                  <button class="btn-cantidad" @click="incrementarCantidad(index)">
                    <span class="cantidad-icon">+</span>
                  </button>
                </div>
                
                <button class="btn-eliminar" @click="eliminarProducto(index)">
                  <span class="eliminar-icon">🗑️</span>
                </button>
              </div>
            </div>
          </div>
        </div>

        <div v-if="carrito.length > 0" class="carrito-footer">
          <div class="carrito-total">
            <div class="total-line">
              <span class="total-label">Subtotal:</span>
              <span class="total-valor">${{ subtotalFormateado }}</span>
            </div>
            <div class="total-line">
              <span class="total-label">Impuestos (10%):</span>
              <span class="total-valor">${{ impuestosFormateados }}</span>
            </div>
            <div class="total-line total-final">
              <span class="total-label">Total:</span>
              <span class="total-valor">${{ totalFormateado }}</span>
            </div>
          </div>

          <div class="carrito-actions">
            <button class="btn-limpiar" @click="limpiarCarrito">
              <span class="btn-text">LIMPIAR CARRITO</span>
            </button>
            
            <button v-if="usuario" class="btn-comprar" @click="procesarCompra">
              <span class="btn-text">PROCESAR COMPRA</span>
              <div class="btn-glow"></div>
            </button>
            
            <div v-else class="auth-required">
              <div class="auth-message">
                <h4 class="auth-titulo">🚀 ¡Casi Listo!</h4>
                <p class="auth-descripcion">
                  Tu compra está lista. Regístrate para beneficios adicionales o continúa como invitado.
                </p>
                <div class="auth-buttons">
                  <button class="btn-login-carrito" @click="$emit('mostrar-login')">
                    <span class="btn-text">INICIAR SESIÓN</span>
                  </button>
                  <button class="btn-registro-carrito" @click="$emit('mostrar-registro')">
                    <span class="btn-text">CREAR CUENTA</span>
                  </button>
                </div>
                <button class="btn-invitado" @click="procesarCompraInvitado">
                  <span class="btn-text">CONTINUAR COMO INVITADO</span>
                  <div class="btn-glow"></div>
                </button>
              </div>
            </div>
          </div>
        </div>

        <div class="carrito-border"></div>
      </div>
    </div>
  </Transition>
</template>

<script>
export default {
  name: 'CarritoLateral',
  props: {
    mostrar: {
      type: Boolean,
      default: false
    },
    carrito: {
      type: Array,
      required: true
    },
    usuario: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      cantidades: []
    }
  },
  computed: {
    subtotal() {
      return this.carrito.reduce((total, producto, index) => {
        return total + (producto.precio * this.cantidades[index])
      }, 0)
    },
    impuestos() {
      return this.subtotal * 0.10
    },
    total() {
      return this.subtotal + this.impuestos
    },
    subtotalFormateado() {
      return this.subtotal.toLocaleString()
    },
    impuestosFormateados() {
      return this.impuestos.toLocaleString()
    },
    totalFormateado() {
      return this.total.toLocaleString()
    }
  },
  methods: {
    cerrarCarrito() {
      this.$emit('cerrar')
    },
    incrementarCantidad(index) {
      this.cantidades[index]++
      this.$emit('actualizar-cantidad', index, this.cantidades[index])
    },
    decrementarCantidad(index) {
      if (this.cantidades[index] > 1) {
        this.cantidades[index]--
        this.$emit('actualizar-cantidad', index, this.cantidades[index])
      }
    },
    eliminarProducto(index) {
      this.$emit('eliminar-producto', index)
    },
    limpiarCarrito() {
      if (confirm('¿Estás seguro de que quieres limpiar todo el carrito?')) {
        this.$emit('limpiar-carrito')
      }
    },
    procesarCompra() {
      if (this.carrito.length === 0) {
        alert('El carrito está vacío')
        return
      }
      
      const mensaje = `
        🚀 PROCESANDO COMPRA STARK INDUSTRIES 🚀
        
        Productos: ${this.carrito.length}
        Total: $${this.totalFormateado}
        
        ¡Gracias por elegir Stark Industries!
        Su pedido será procesado por J.A.R.V.I.S.
      `
      
      alert(mensaje)
      this.$emit('procesar-compra', {
        productos: this.carrito,
        cantidades: this.cantidades,
        total: this.total
      })
    },
    procesarCompraInvitado() {
      if (this.carrito.length === 0) {
        alert('El carrito está vacío')
        return
      }
      
      const mensaje = `
        🚀 COMPRA COMO INVITADO - STARK INDUSTRIES 🚀
        
        Productos: ${this.carrito.length}
        Total: $${this.totalFormateado}
        
        ⚠️ NOTA: Como invitado, esta compra no se guardará en tu historial.
        Te recomendamos crear una cuenta para:
        • Guardar tu historial de compras
        • Acceder a tu asesor personal
        • Recibir ofertas exclusivas
        
        ¡Gracias por elegir Stark Industries!
        Su pedido será procesado por J.A.R.V.I.S.
      `
      
      alert(mensaje)
      this.$emit('procesar-compra-invitado', {
        productos: this.carrito,
        cantidades: this.cantidades,
        total: this.total
      })
    }
  },
  watch: {
    carrito: {
      handler(newCarrito) {
        this.cantidades = newCarrito.map(() => 1)
      },
      immediate: true
    }
  }
}
</script>

<style scoped>
.carrito-lateral-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(5px);
  z-index: 2000;
  display: flex;
  justify-content: flex-end;
}

.carrito-lateral {
  width: 450px;
  height: 100vh;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border-left: 2px solid #00ffff;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
  box-shadow: -10px 0 30px rgba(0, 255, 255, 0.3);
}

.carrito-header {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
  padding: 1.5rem;
  border-bottom: 2px solid #00ffff;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
  overflow: hidden;
}

.carrito-header::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  animation: scan 3s infinite;
}

.carrito-titulo {
  font-size: 1.5rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 15px #00ffff;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  position: relative;
  z-index: 2;
}

.carrito-icon {
  font-size: 1.2rem;
}

.btn-cerrar {
  background: linear-gradient(45deg, #ff0000, #cc0000);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 0 15px rgba(255, 0, 0, 0.3);
  position: relative;
  z-index: 2;
}

.btn-cerrar:hover {
  transform: scale(1.1);
  box-shadow: 0 0 20px rgba(255, 0, 0, 0.5);
}

.cerrar-icon {
  color: #fff;
  font-size: 1.2rem;
  font-weight: bold;
}

.carrito-content {
  flex: 1;
  overflow-y: auto;
  padding: 1rem;
}

.carrito-vacio {
  text-align: center;
  padding: 3rem 1rem;
  color: #888;
}

.vacio-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
  animation: pulse 2s infinite;
}

.vacio-titulo {
  font-size: 1.3rem;
  color: #00ffff;
  margin-bottom: 0.5rem;
  text-shadow: 0 0 10px #00ffff;
}

.vacio-descripcion {
  font-size: 0.9rem;
  line-height: 1.4;
}

.carrito-items {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.carrito-item {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.2);
  border-radius: 15px;
  padding: 1rem;
  display: flex;
  gap: 1rem;
  align-items: center;
  transition: all 0.3s ease;
}

.carrito-item:hover {
  border-color: #00ffff;
  box-shadow: 0 5px 15px rgba(0, 255, 255, 0.2);
}

.item-imagen {
  width: 60px;
  height: 60px;
  border-radius: 10px;
  overflow: hidden;
  background: linear-gradient(45deg, #0a0a0a, #1a1a2e);
}

.item-imagen img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.item-info {
  flex: 1;
  min-width: 0;
}

.item-nombre {
  font-size: 0.9rem;
  font-weight: 600;
  color: #00ffff;
  text-shadow: 0 0 8px #00ffff;
  margin-bottom: 0.3rem;
  line-height: 1.2;
}

.item-precio {
  font-size: 0.8rem;
  color: #fff;
  font-weight: bold;
}

.item-controls {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  align-items: center;
}

.cantidad-control {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(0, 255, 255, 0.1);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 8px;
  padding: 0.3rem;
}

.btn-cantidad {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  color: #000;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
}

.btn-cantidad:hover:not(:disabled) {
  transform: scale(1.1);
  box-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
}

.btn-cantidad:disabled {
  background: linear-gradient(45deg, #666, #444);
  color: #999;
  cursor: not-allowed;
  opacity: 0.6;
}

.cantidad-valor {
  color: #fff;
  font-weight: bold;
  font-size: 0.9rem;
  min-width: 20px;
  text-align: center;
}

.btn-eliminar {
  background: linear-gradient(45deg, #ff0000, #cc0000);
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  color: #fff;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
}

.btn-eliminar:hover {
  transform: scale(1.1);
  box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
}

.carrito-footer {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
  border-top: 2px solid #00ffff;
  padding: 1.5rem;
}

.carrito-total {
  margin-bottom: 1.5rem;
}

.total-line {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 0;
  border-bottom: 1px solid rgba(0, 255, 255, 0.1);
}

.total-line.total-final {
  border-bottom: none;
  border-top: 2px solid #00ffff;
  margin-top: 0.5rem;
  padding-top: 1rem;
}

.total-label {
  color: #888;
  font-weight: 500;
}

.total-valor {
  color: #fff;
  font-weight: bold;
}

.total-line.total-final .total-valor {
  color: #00ffff;
  font-size: 1.2rem;
  text-shadow: 0 0 10px #00ffff;
}

.carrito-actions {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.btn-limpiar,
.btn-comprar,
.btn-invitado {
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
  font-size: 0.9rem;
}

.btn-limpiar {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  color: #000;
}

.btn-comprar {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-invitado {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  color: #000;
}

.btn-limpiar:hover,
.btn-comprar:hover,
.btn-invitado:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 255, 255, 0.3);
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

.btn-comprar:hover .btn-glow,
.btn-invitado:hover .btn-glow {
  left: 100%;
}

.auth-required {
  margin-top: 1rem;
}

.auth-message {
  background: rgba(255, 0, 255, 0.05);
  border: 2px solid rgba(255, 0, 255, 0.3);
  border-radius: 15px;
  padding: 1rem;
  text-align: center;
}

.auth-titulo {
  color: #ff00ff;
  font-size: 1rem;
  font-weight: 700;
  margin-bottom: 0.8rem;
  text-shadow: 0 0 10px #ff00ff;
}

.auth-descripcion {
  color: #ccc;
  font-size: 0.8rem;
  line-height: 1.4;
  margin-bottom: 1rem;
}

.auth-buttons {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
  justify-content: center;
}

.btn-login-carrito,
.btn-registro-carrito {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 8px;
  padding: 0.6rem 1rem;
  color: #000;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.7rem;
}

.btn-registro-carrito {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-login-carrito:hover,
.btn-registro-carrito:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 255, 255, 0.3);
}

.carrito-border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, transparent, rgba(0, 255, 255, 0.05), transparent);
  pointer-events: none;
}

/* Transiciones */
.cart-slide-enter-active,
.cart-slide-leave-active {
  transition: transform 0.3s ease;
}

.cart-slide-enter-from {
  transform: translateX(100%);
}

.cart-slide-leave-to {
  transform: translateX(100%);
}

@media (max-width: 768px) {
  .carrito-lateral {
    width: 100%;
  }
  
  .carrito-header {
    padding: 1rem;
  }
  
  .carrito-titulo {
    font-size: 1.3rem;
  }
  
  .carrito-content {
    padding: 0.8rem;
  }
  
  .carrito-item {
    flex-direction: column;
    text-align: center;
    gap: 0.8rem;
    padding: 0.8rem;
  }
  
  .item-controls {
    flex-direction: row;
    justify-content: space-between;
    width: 100%;
  }
  
  .auth-buttons {
    flex-direction: column;
  }
  
  .carrito-footer {
    padding: 1rem;
  }
  
  .btn-limpiar,
  .btn-comprar,
  .btn-invitado {
    font-size: 0.8rem;
    padding: 0.8rem 1.2rem;
  }
}

@media (max-width: 480px) {
  .carrito-header {
    padding: 0.8rem;
  }
  
  .carrito-titulo {
    font-size: 1.1rem;
  }
  
  .carrito-content {
    padding: 0.6rem;
  }
  
  .carrito-item {
    padding: 0.6rem;
    gap: 0.6rem;
  }
  
  .item-imagen {
    width: 50px;
    height: 50px;
  }
  
  .item-nombre {
    font-size: 0.8rem;
  }
  
  .item-precio {
    font-size: 0.75rem;
  }
  
  .btn-cantidad {
    width: 20px;
    height: 20px;
    font-size: 0.7rem;
  }
  
  .cantidad-valor {
    font-size: 0.8rem;
  }
  
  .btn-eliminar {
    width: 25px;
    height: 25px;
    font-size: 0.7rem;
  }
  
  .carrito-footer {
    padding: 0.8rem;
  }
  
  .btn-limpiar,
  .btn-comprar,
  .btn-invitado {
    font-size: 0.75rem;
    padding: 0.7rem 1rem;
  }
  
  .auth-titulo {
    font-size: 0.9rem;
  }
  
  .auth-descripcion {
    font-size: 0.75rem;
  }
  
  .btn-login-carrito,
  .btn-registro-carrito {
    font-size: 0.65rem;
    padding: 0.5rem 0.8rem;
  }
}
</style>
