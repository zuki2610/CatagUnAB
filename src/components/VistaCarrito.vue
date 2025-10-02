<template>
  <div class="vista-carrito">
    <div class="carrito-header">
      <button class="btn-volver" @click="volverCatalogo">
        <span class="volver-icon">←</span>
        <span class="volver-text">VOLVER AL CATÁLOGO</span>
      </button>
      
      <div class="carrito-titulo">
        <h1 class="titulo-principal">
          <span class="stark-logo">STARK</span>
          <span class="industries-text">CARRITO</span>
        </h1>
        <p class="subtitle">Tecnología Seleccionada</p>
      </div>

      <div class="carrito-stats">
        <div class="stat-item">
          <span class="stat-label">Productos:</span>
          <span class="stat-value">{{ carrito.length }}</span>
        </div>
        <div class="stat-item">
          <span class="stat-label">Total:</span>
          <span class="stat-value">${{ totalFormateado }}</span>
        </div>
      </div>
    </div>

    <div class="carrito-content">
      <div v-if="carrito.length === 0" class="carrito-vacio">
        <div class="vacio-icon">🛒</div>
        <h2 class="vacio-titulo">Carrito Vacío</h2>
        <p class="vacio-descripcion">No hay productos seleccionados</p>
        <button class="btn-explorar" @click="volverCatalogo">
          <span class="btn-text">EXPLORAR PRODUCTOS</span>
          <div class="btn-glow"></div>
        </button>
      </div>

      <div v-else class="productos-carrito">
        <div class="carrito-list">
          <div 
            v-for="(producto, index) in carrito" 
            :key="`${producto.id}-${index}`"
            class="item-carrito"
          >
            <div class="item-imagen">
              <img :src="producto.imagen" :alt="producto.nombre" />
              <div class="imagen-overlay">
                <div class="scan-line"></div>
              </div>
            </div>

            <div class="item-info">
              <h3 class="item-nombre">{{ producto.nombre }}</h3>
              <p class="item-descripcion">{{ producto.descripcion }}</p>
              <div class="item-precio">
                <span class="precio-simbolo">$</span>
                <span class="precio-valor">{{ producto.precio.toLocaleString() }}</span>
                <span class="precio-moneda">USD</span>
              </div>
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

              <div class="item-subtotal">
                <span class="subtotal-label">Subtotal:</span>
                <span class="subtotal-valor">${{ calcularSubtotal(index) }}</span>
              </div>

              <button class="btn-eliminar" @click="eliminarProducto(index)">
                <span class="eliminar-icon">🗑️</span>
                <span class="eliminar-text">ELIMINAR</span>
              </button>
            </div>
          </div>
        </div>

        <div class="carrito-summary">
          <div class="summary-header">
            <h3 class="summary-titulo">RESUMEN DE COMPRA</h3>
            <div class="summary-glow"></div>
          </div>

          <div class="summary-details">
            <div class="summary-item">
              <span class="summary-label">Subtotal:</span>
              <span class="summary-value">${{ subtotalFormateado }}</span>
            </div>
            <div class="summary-item">
              <span class="summary-label">Impuestos (10%):</span>
              <span class="summary-value">${{ impuestosFormateados }}</span>
            </div>
            <div class="summary-item total">
              <span class="summary-label">Total:</span>
              <span class="summary-value">${{ totalFormateado }}</span>
            </div>
          </div>

          <div class="summary-actions">
            <button class="btn-limpiar" @click="limpiarCarrito">
              <span class="btn-text">LIMPIAR CARRITO</span>
              <div class="btn-glow"></div>
            </button>
            
            <button v-if="usuario" class="btn-comprar" @click="procesarCompra">
              <span class="btn-text">PROCESAR COMPRA</span>
              <div class="btn-glow"></div>
            </button>
            
            <div v-else class="auth-required">
              <div class="auth-message">
                <h4 class="auth-titulo">🚀 ¡Casi Listo!</h4>
                <p class="auth-descripcion">
                  Tu compra está lista para procesarse. Puedes continuar como invitado o crear una cuenta para beneficios adicionales.
                </p>
                <div class="auth-buttons">
                  <button class="btn-login-carrito" @click="$emit('mostrar-login')">
                    <span class="btn-text">INICIAR SESIÓN</span>
                    <div class="btn-glow"></div>
                  </button>
                  <button class="btn-registro-carrito" @click="$emit('mostrar-registro')">
                    <span class="btn-text">CREAR CUENTA</span>
                    <div class="btn-glow"></div>
                  </button>
                </div>
                <div class="benefits-list">
                  <h5 class="benefits-title">Beneficios de registrarse:</h5>
                  <ul class="benefits-items">
                    <li>📋 Historial de compras guardado</li>
                    <li>👨‍💼 Asesor personal asignado</li>
                    <li>🎁 Ofertas exclusivas</li>
                    <li>⚡ Proceso de compra más rápido</li>
                  </ul>
                </div>
                <p class="auth-note">
                  O continúa como invitado - ¡tu compra se procesará igual!
                </p>
                <button class="btn-invitado" @click="procesarCompraInvitado">
                  <span class="btn-text">CONTINUAR COMO INVITADO</span>
                  <div class="btn-glow"></div>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="carrito-border"></div>
  </div>
</template>

<script>
export default {
  name: 'VistaCarrito',
  props: {
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
    volverCatalogo() {
      this.$emit('volver-catalogo')
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
    calcularSubtotal(index) {
      return (this.carrito[index].precio * this.cantidades[index]).toLocaleString()
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
        // Inicializar cantidades cuando se agregan productos
        this.cantidades = newCarrito.map(() => 1)
      },
      immediate: true
    }
  },
  mounted() {
    console.log('🛒 VistaCarrito - MOUNTED: Vista del carrito montada')
  }
}
</script>

<style scoped>
.vista-carrito {
  min-height: 100vh;
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 50%, #16213e 100%);
  position: relative;
  overflow-x: hidden;
}

.carrito-header {
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a2e 100%);
  padding: 2rem;
  border-bottom: 2px solid #00ffff;
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

.btn-volver {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  border: none;
  border-radius: 10px;
  padding: 0.8rem 1.5rem;
  color: #000;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  transition: all 0.3s ease;
  margin-bottom: 2rem;
  position: relative;
  overflow: hidden;
}

.btn-volver:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(255, 107, 0, 0.3);
}

.volver-icon {
  font-size: 1.2rem;
}

.carrito-titulo {
  text-align: center;
  margin-bottom: 2rem;
  position: relative;
  z-index: 2;
}

.titulo-principal {
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
  font-weight: 400;
}

.carrito-stats {
  display: flex;
  justify-content: center;
  gap: 3rem;
  position: relative;
  z-index: 2;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.stat-label {
  color: #888;
  font-size: 0.9rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.stat-value {
  color: #00ffff;
  font-size: 1.5rem;
  font-weight: bold;
  text-shadow: 0 0 15px #00ffff;
}

.carrito-content {
  padding: 2rem;
  max-width: 1400px;
  margin: 0 auto;
}

.carrito-vacio {
  text-align: center;
  padding: 4rem 2rem;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 25px;
  position: relative;
  overflow: hidden;
}

.vacio-icon {
  font-size: 4rem;
  margin-bottom: 1rem;
  animation: pulse 2s infinite;
}

.vacio-titulo {
  font-size: 2.5rem;
  color: #00ffff;
  margin-bottom: 1rem;
  text-shadow: 0 0 20px #00ffff;
}

.vacio-descripcion {
  color: #888;
  font-size: 1.2rem;
  margin-bottom: 2rem;
}

.btn-explorar {
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
  font-size: 1.1rem;
}

.btn-explorar:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(0, 255, 255, 0.4);
}

.productos-carrito {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 2rem;
}

.carrito-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.item-carrito {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 1.5rem;
  display: grid;
  grid-template-columns: 150px 1fr auto;
  gap: 1.5rem;
  align-items: center;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.item-carrito:hover {
  border-color: #00ffff;
  box-shadow: 0 10px 30px rgba(0, 255, 255, 0.2);
}

.item-imagen {
  position: relative;
  width: 150px;
  height: 120px;
  border-radius: 15px;
  overflow: hidden;
  background: linear-gradient(45deg, #0a0a0a, #1a1a2e);
}

.item-imagen img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.imagen-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.item-carrito:hover .imagen-overlay {
  opacity: 1;
}

.scan-line {
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, #00ffff, transparent);
  animation: scan 2s infinite;
}

.item-info {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
}

.item-nombre {
  font-size: 1.3rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 10px #00ffff;
}

.item-descripcion {
  color: #888;
  font-size: 0.9rem;
  line-height: 1.4;
}

.item-precio {
  display: flex;
  align-items: baseline;
  gap: 0.3rem;
}

.precio-simbolo {
  font-size: 1rem;
  color: #00ffff;
  font-weight: bold;
}

.precio-valor {
  font-size: 1.5rem;
  color: #fff;
  font-weight: 900;
  text-shadow: 0 0 15px #00ffff;
}

.precio-moneda {
  font-size: 0.8rem;
  color: #888;
}

.item-controls {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
}

.cantidad-control {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  background: rgba(0, 255, 255, 0.1);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 10px;
  padding: 0.5rem;
}

.btn-cantidad {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  color: #000;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-cantidad:hover:not(:disabled) {
  transform: scale(1.1);
  box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
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
  font-size: 1.1rem;
  min-width: 30px;
  text-align: center;
}

.item-subtotal {
  text-align: center;
}

.subtotal-label {
  color: #888;
  font-size: 0.8rem;
  display: block;
  margin-bottom: 0.3rem;
}

.subtotal-valor {
  color: #00ffff;
  font-weight: bold;
  font-size: 1.1rem;
  text-shadow: 0 0 10px #00ffff;
}

.btn-eliminar {
  background: linear-gradient(45deg, #ff0000, #cc0000);
  border: none;
  border-radius: 10px;
  padding: 0.5rem 1rem;
  color: #fff;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  transition: all 0.3s ease;
  font-size: 0.8rem;
}

.btn-eliminar:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 0, 0, 0.3);
}

.carrito-summary {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 25px;
  padding: 2rem;
  position: sticky;
  top: 2rem;
  height: fit-content;
}

.summary-header {
  text-align: center;
  margin-bottom: 2rem;
  position: relative;
}

.summary-titulo {
  font-size: 1.5rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 15px #00ffff;
  margin-bottom: 1rem;
}

.summary-glow {
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, #00ffff, transparent);
  animation: pulse 2s infinite;
}

.summary-details {
  margin-bottom: 2rem;
}

.summary-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(0, 255, 255, 0.1);
}

.summary-item.total {
  border-bottom: none;
  border-top: 2px solid #00ffff;
  margin-top: 1rem;
  padding-top: 1.5rem;
}

.summary-label {
  color: #888;
  font-weight: 500;
}

.summary-value {
  color: #fff;
  font-weight: bold;
  font-size: 1.1rem;
}

.summary-item.total .summary-value {
  color: #00ffff;
  font-size: 1.5rem;
  text-shadow: 0 0 15px #00ffff;
}

.summary-actions {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.btn-limpiar,
.btn-comprar {
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

.btn-limpiar {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  color: #000;
}

.btn-comprar {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-limpiar:hover,
.btn-comprar:hover {
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

.btn-limpiar:hover .btn-glow,
.btn-comprar:hover .btn-glow {
  left: 100%;
}

.auth-required {
  margin-top: 1rem;
}

.auth-message {
  background: rgba(255, 0, 255, 0.05);
  border: 2px solid rgba(255, 0, 255, 0.3);
  border-radius: 15px;
  padding: 1.5rem;
  text-align: center;
}

.auth-titulo {
  color: #ff00ff;
  font-size: 1.2rem;
  font-weight: 700;
  margin-bottom: 1rem;
  text-shadow: 0 0 10px #ff00ff;
}

.auth-descripcion {
  color: #ccc;
  font-size: 0.9rem;
  line-height: 1.4;
  margin-bottom: 1.5rem;
}

.auth-buttons {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
  justify-content: center;
}

.btn-login-carrito,
.btn-registro-carrito {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  border: none;
  border-radius: 10px;
  padding: 0.8rem 1.2rem;
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

.btn-registro-carrito {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-login-carrito:hover,
.btn-registro-carrito:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 255, 255, 0.3);
}

.benefits-list {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.2);
  border-radius: 10px;
  padding: 1rem;
  margin: 1rem 0;
}

.benefits-title {
  color: #00ffff;
  font-size: 0.9rem;
  font-weight: 600;
  margin-bottom: 0.8rem;
  text-shadow: 0 0 10px #00ffff;
}

.benefits-items {
  list-style: none;
  padding: 0;
  margin: 0;
}

.benefits-items li {
  color: #ccc;
  font-size: 0.8rem;
  margin-bottom: 0.5rem;
  padding-left: 0.5rem;
}

.auth-note {
  color: #888;
  font-size: 0.8rem;
  margin-bottom: 1rem;
  font-style: italic;
}

.btn-invitado {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
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

.btn-invitado:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(255, 107, 0, 0.3);
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

@media (max-width: 1024px) {
  .productos-carrito {
    grid-template-columns: 1fr;
  }
  
  .carrito-summary {
    position: static;
  }
}

@media (max-width: 768px) {
  .carrito-header {
    padding: 1rem;
  }
  
  .titulo-principal {
    font-size: 2.5rem;
  }
  
  .carrito-stats {
    gap: 2rem;
  }
  
  .carrito-content {
    padding: 1rem;
  }
  
  .item-carrito {
    grid-template-columns: 1fr;
    text-align: center;
    gap: 1rem;
  }
  
  .item-imagen {
    width: 100%;
    height: 200px;
  }
  
  .item-controls {
    flex-direction: row;
    justify-content: space-between;
  }
}
</style>
