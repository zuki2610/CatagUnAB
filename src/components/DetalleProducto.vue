<template>
  <div class="detalle-overlay" @click="cerrarDetalle">
    <div class="detalle-modal" @click.stop>
      <div class="detalle-header">
        <h2 class="detalle-titulo">{{ producto.nombre }}</h2>
        <button class="btn-cerrar" @click="cerrarDetalle">
          <span class="cerrar-icon">×</span>
        </button>
      </div>

      <div class="detalle-content">
        <div class="detalle-imagen">
          <img :src="producto.imagen" :alt="producto.nombre" />
          <div class="imagen-scan">
            <div class="scan-line"></div>
            <div class="scan-line reverse"></div>
          </div>
        </div>

        <div class="detalle-info">
          <div class="info-section">
            <h3 class="section-title">Descripción</h3>
            <p class="descripcion-completa">{{ producto.descripcion }}</p>
          </div>

          <div class="info-section">
            <h3 class="section-title">Especificaciones</h3>
            <div class="especificaciones">
              <div class="spec-item">
                <span class="spec-label">Precio:</span>
                <span class="spec-value precio-destacado">${{ producto.precio.toLocaleString() }} USD</span>
              </div>
              <div class="spec-item">
                <span class="spec-label">Stock Disponible:</span>
                <span class="spec-value" :class="stockClass">{{ stockText }}</span>
              </div>
              <div class="spec-item">
                <span class="spec-label">ID del Producto:</span>
                <span class="spec-value">{{ producto.id }}</span>
              </div>
            </div>
          </div>

          <div class="info-section">
            <h3 class="section-title">Características Avanzadas</h3>
            <div class="caracteristicas">
              <div class="caracteristica-item">
                <div class="caracteristica-icon">⚡</div>
                <span>Tecnología de Vanguardia</span>
              </div>
              <div class="caracteristica-item">
                <div class="caracteristica-icon">🔧</div>
                <span>Mantenimiento Automático</span>
              </div>
              <div class="caracteristica-item">
                <div class="caracteristica-icon">🛡️</div>
                <span>Garantía Extendida</span>
              </div>
              <div class="caracteristica-item">
                <div class="caracteristica-icon">🚀</div>
                <span>Rendimiento Optimizado</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="detalle-footer">
        <button 
          class="btn-agregar-detalle" 
          :disabled="producto.stock === 0"
          @click="agregarAlCarrito"
          :class="{ 'btn-disabled': producto.stock === 0 }"
        >
          <span class="btn-text">{{ producto.stock === 0 ? 'SIN STOCK' : 'AGREGAR AL CARRITO' }}</span>
          <div class="btn-glow"></div>
        </button>
        
        <button class="btn-contactar" @click="contactarAsesor">
          <span class="btn-text">CONTACTAR ASESOR</span>
          <div class="btn-glow"></div>
        </button>
      </div>

      <div class="modal-border"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetalleProducto',
  props: {
    producto: {
      type: Object,
      required: true
    }
  },
  computed: {
    stockClass() {
      if (this.producto.stock === 0) return 'stock-agotado'
      if (this.producto.stock <= 3) return 'stock-bajo'
      return 'stock-disponible'
    },
    stockText() {
      if (this.producto.stock === 0) return 'AGOTADO'
      if (this.producto.stock <= 3) return `SOLO ${this.producto.stock} UNIDADES`
      return `${this.producto.stock} UNIDADES DISPONIBLES`
    }
  },
  methods: {
    cerrarDetalle() {
      this.$emit('cerrar')
    },
    agregarAlCarrito() {
      if (this.producto.stock > 0) {
        this.$emit('agregar-al-carrito', this.producto.id)
        console.log(`Producto ${this.producto.nombre} agregado al carrito desde detalle`)
      }
    },
    contactarAsesor() {
      alert(`Contactando asesor para ${this.producto.nombre}...`)
    }
  },
  mounted() {
    // Prevenir scroll del body cuando el modal está abierto
    document.body.style.overflow = 'hidden'
  },
  beforeUnmount() {
    // Restaurar scroll del body
    document.body.style.overflow = 'auto'
  }
}
</script>

<style scoped>
.detalle-overlay {
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
  z-index: 2000;
  animation: fadeIn 0.3s ease-out;
}

.detalle-modal {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid #00ffff;
  border-radius: 25px;
  max-width: 900px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  animation: slideIn 0.4s ease-out;
  box-shadow: 0 0 50px rgba(0, 255, 255, 0.3);
}

.detalle-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem 2rem 1rem;
  border-bottom: 1px solid rgba(0, 255, 255, 0.3);
}

.detalle-titulo {
  font-size: 2.2rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 20px #00ffff;
  margin: 0;
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

.detalle-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  padding: 2rem;
}

.detalle-imagen {
  position: relative;
  border-radius: 15px;
  overflow: hidden;
  background: linear-gradient(45deg, #0a0a0a, #1a1a2e);
}

.detalle-imagen img {
  width: 100%;
  height: 300px;
  object-fit: cover;
}

.imagen-scan {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

.scan-line {
  position: absolute;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, #00ffff, transparent);
  animation: scanMove 3s infinite;
}

.scan-line.reverse {
  animation: scanMoveReverse 3s infinite;
  animation-delay: 1.5s;
}

.detalle-info {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.info-section {
  background: rgba(0, 255, 255, 0.05);
  border: 1px solid rgba(0, 255, 255, 0.2);
  border-radius: 15px;
  padding: 1.5rem;
}

.section-title {
  font-size: 1.3rem;
  font-weight: 600;
  color: #00ffff;
  margin-bottom: 1rem;
  text-shadow: 0 0 10px #00ffff;
}

.descripcion-completa {
  color: #ccc;
  line-height: 1.6;
  font-size: 1rem;
}

.especificaciones {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.spec-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem 0;
  border-bottom: 1px solid rgba(0, 255, 255, 0.1);
}

.spec-label {
  color: #888;
  font-weight: 500;
}

.spec-value {
  color: #fff;
  font-weight: 600;
}

.precio-destacado {
  color: #00ffff;
  font-size: 1.2rem;
  text-shadow: 0 0 10px #00ffff;
}

.stock-disponible {
  color: #00ff00;
}

.stock-bajo {
  color: #ffff00;
}

.stock-agotado {
  color: #ff0000;
}

.caracteristicas {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.caracteristica-item {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 0.8rem;
  background: rgba(0, 255, 255, 0.1);
  border-radius: 10px;
  border: 1px solid rgba(0, 255, 255, 0.2);
}

.caracteristica-icon {
  font-size: 1.5rem;
}

.detalle-footer {
  display: flex;
  gap: 1rem;
  padding: 2rem;
  border-top: 1px solid rgba(0, 255, 255, 0.3);
}

.btn-agregar-detalle,
.btn-contactar {
  flex: 1;
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

.btn-agregar-detalle {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-contactar {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-agregar-detalle:hover:not(.btn-disabled),
.btn-contactar:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(0, 255, 255, 0.4);
}

.btn-disabled {
  background: linear-gradient(45deg, #666, #444);
  color: #999;
  cursor: not-allowed;
  opacity: 0.6;
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

.btn-agregar-detalle:hover .btn-glow,
.btn-contactar:hover .btn-glow {
  left: 100%;
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

.detalle-modal:hover .modal-border {
  opacity: 1;
}

@keyframes scanMove {
  0% { top: 0; opacity: 0; }
  50% { opacity: 1; }
  100% { top: 100%; opacity: 0; }
}

@keyframes scanMoveReverse {
  0% { bottom: 0; opacity: 0; }
  50% { opacity: 1; }
  100% { bottom: 100%; opacity: 0; }
}

@media (max-width: 768px) {
  .detalle-modal {
    width: 95%;
    margin: 1rem;
  }
  
  .detalle-content {
    grid-template-columns: 1fr;
    gap: 1.5rem;
    padding: 1.5rem;
  }
  
  .detalle-header {
    padding: 1.5rem 1.5rem 1rem;
  }
  
  .detalle-titulo {
    font-size: 1.8rem;
  }
  
  .caracteristicas {
    grid-template-columns: 1fr;
  }
  
  .detalle-footer {
    flex-direction: column;
    padding: 1.5rem;
  }
}
</style>
