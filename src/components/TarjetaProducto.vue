<template>
  <div 
    class="tarjeta-producto" 
    :class="{
      'sin-stock': stock === 0,
      'con-stock': stock > 0,
      'stock-bajo': stock > 0 && stock <= 3
    }"
    @click="verDetalle"
  >
    <div class="tarjeta-header">
      <div class="producto-imagen">
        <img :src="imagen" :alt="nombre" />
        <div class="imagen-overlay">
          <div class="scan-line"></div>
        </div>
      </div>
      <div class="stock-indicator" :class="stockClass">
        <span class="stock-text">{{ stockText }}</span>
      </div>
    </div>

    <div class="tarjeta-content">
      <h3 class="producto-nombre">{{ nombre }}</h3>
      <p class="producto-descripcion">{{ descripcion }}</p>
      
      <div class="precio-container">
        <span class="precio-simbolo">$</span>
        <span class="precio-valor">{{ precioFormateado }}</span>
        <span class="precio-moneda">USD</span>
      </div>

      <div class="producto-acciones">
        <button 
          class="btn-agregar" 
          :disabled="stock === 0"
          @click.stop="agregarAlCarrito"
          :class="{ 'btn-disabled': stock === 0 }"
        >
          <span class="btn-text">{{ stock === 0 ? 'SIN STOCK' : 'AGREGAR' }}</span>
          <div class="btn-glow"></div>
        </button>
        
        <button class="btn-detalle" @click.stop="verDetalle">
          <span class="btn-text">DETALLES</span>
          <div class="btn-glow"></div>
        </button>
      </div>

      <!-- Slot para contenido personalizado -->
      <div class="contenido-personalizado">
        <slot name="contenido-personalizado"></slot>
      </div>
    </div>

    <div class="tarjeta-border"></div>
  </div>
</template>

<script>
export default {
  name: 'TarjetaProducto',
  props: {
    id: {
      type: Number,
      required: true
    },
    nombre: {
      type: String,
      required: true
    },
    precio: {
      type: Number,
      required: true
    },
    imagen: {
      type: String,
      required: true
    },
    stock: {
      type: Number,
      required: true
    },
    descripcion: {
      type: String,
      required: true
    }
  },
  computed: {
    precioFormateado() {
      return this.precio.toLocaleString()
    },
    stockClass() {
      if (this.stock === 0) return 'stock-agotado'
      if (this.stock <= 3) return 'stock-bajo'
      return 'stock-disponible'
    },
    stockText() {
      if (this.stock === 0) return 'AGOTADO'
      if (this.stock <= 3) return `SOLO ${this.stock}`
      return 'DISPONIBLE'
    }
  },
  methods: {
    agregarAlCarrito() {
      if (this.stock > 0) {
        this.$emit('agregar-al-carrito', this.id)
        console.log(`Producto ${this.nombre} agregado al carrito`)
      }
    },
    verDetalle() {
      this.$emit('ver-detalle', this.id)
    }
  },
  // Hooks del ciclo de vida
  created() {
    console.log(`🔄 TarjetaProducto ${this.nombre} - CREATED: Componente creado`)
  },
  mounted() {
    console.log(`✅ TarjetaProducto ${this.nombre} - MOUNTED: Componente montado en el DOM`)
    // Animación de entrada
    this.$el.style.animation = 'slideIn 0.6s ease-out'
  },
  beforeUnmount() {
    console.log(`🗑️ TarjetaProducto ${this.nombre} - BEFORE_UNMOUNT: Componente a punto de ser destruido`)
  },
  unmounted() {
    console.log(`❌ TarjetaProducto ${this.nombre} - UNMOUNTED: Componente destruido`)
  }
}
</script>

<style scoped>
.tarjeta-producto {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 1.5rem;
  position: relative;
  cursor: pointer;
  transition: all 0.3s ease;
  overflow: hidden;
  animation: slideIn 0.6s ease-out;
}

.tarjeta-producto:hover {
  transform: translateY(-10px);
  border-color: #00ffff;
  box-shadow: 0 20px 40px rgba(0, 255, 255, 0.2);
}

.tarjeta-producto.con-stock {
  border-color: rgba(0, 255, 0, 0.5);
}

.tarjeta-producto.sin-stock {
  border-color: rgba(255, 0, 0, 0.5);
  opacity: 0.7;
}

.tarjeta-producto.stock-bajo {
  border-color: rgba(255, 255, 0, 0.5);
}

.tarjeta-header {
  position: relative;
  margin-bottom: 1.5rem;
}

.producto-imagen {
  position: relative;
  width: 100%;
  height: 200px;
  border-radius: 15px;
  overflow: hidden;
  background: linear-gradient(45deg, #0a0a0a, #1a1a2e);
}

.producto-imagen img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.tarjeta-producto:hover .producto-imagen img {
  transform: scale(1.1);
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

.tarjeta-producto:hover .imagen-overlay {
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

.stock-indicator {
  position: absolute;
  top: 1rem;
  right: 1rem;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.stock-disponible {
  background: linear-gradient(45deg, #00ff00, #00aa00);
  color: #000;
  box-shadow: 0 0 15px rgba(0, 255, 0, 0.5);
}

.stock-bajo {
  background: linear-gradient(45deg, #ffff00, #ffaa00);
  color: #000;
  box-shadow: 0 0 15px rgba(255, 255, 0, 0.5);
}

.stock-agotado {
  background: linear-gradient(45deg, #ff0000, #aa0000);
  color: #fff;
  box-shadow: 0 0 15px rgba(255, 0, 0, 0.5);
}

.tarjeta-content {
  position: relative;
  z-index: 2;
}

.producto-nombre {
  font-size: 1.4rem;
  font-weight: 700;
  color: #00ffff;
  margin-bottom: 0.8rem;
  text-shadow: 0 0 10px #00ffff;
}

.producto-descripcion {
  color: #888;
  font-size: 0.9rem;
  line-height: 1.4;
  margin-bottom: 1.5rem;
  height: 2.8rem;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.precio-container {
  display: flex;
  align-items: baseline;
  margin-bottom: 1.5rem;
  gap: 0.3rem;
}

.precio-simbolo {
  font-size: 1.2rem;
  color: #00ffff;
  font-weight: bold;
}

.precio-valor {
  font-size: 2rem;
  color: #fff;
  font-weight: 900;
  text-shadow: 0 0 15px #00ffff;
}

.precio-moneda {
  font-size: 0.9rem;
  color: #888;
  font-weight: 400;
}

.producto-acciones {
  display: flex;
  gap: 1rem;
  margin-bottom: 1rem;
}

.btn-agregar,
.btn-detalle {
  flex: 1;
  padding: 0.8rem 1rem;
  border: none;
  border-radius: 10px;
  font-family: 'Orbitron', monospace;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.3s ease;
}

.btn-agregar {
  background: linear-gradient(45deg, #00ffff, #0088ff);
  color: #000;
}

.btn-detalle {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
}

.btn-agregar:hover:not(.btn-disabled),
.btn-detalle:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(0, 255, 255, 0.3);
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

.btn-agregar:hover .btn-glow,
.btn-detalle:hover .btn-glow {
  left: 100%;
}

.contenido-personalizado {
  margin-top: 1rem;
}

.oferta-badge,
.recomendado-badge {
  position: relative;
  padding: 0.5rem 1rem;
  border-radius: 15px;
  text-align: center;
  font-weight: bold;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-size: 0.8rem;
  margin-bottom: 0.5rem;
}

.oferta-badge {
  background: linear-gradient(45deg, #ff6b00, #ff8c00);
  color: #000;
}

.recomendado-badge {
  background: linear-gradient(45deg, #00ff88, #00cc66);
  color: #000;
}

.oferta-glow,
.recomendado-glow {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: 15px;
  animation: glow 2s infinite;
}

.oferta-glow {
  box-shadow: 0 0 20px rgba(255, 107, 0, 0.5);
}

.recomendado-glow {
  box-shadow: 0 0 20px rgba(0, 255, 136, 0.5);
}

.tarjeta-border {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: 20px;
  background: linear-gradient(45deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.tarjeta-producto:hover .tarjeta-border {
  opacity: 1;
}

@keyframes scan {
  0% { left: -100%; }
  100% { left: 100%; }
}

@media (max-width: 768px) {
  .tarjeta-producto {
    padding: 1rem;
  }
  
  .producto-imagen {
    height: 150px;
  }
  
  .producto-nombre {
    font-size: 1.2rem;
  }
  
  .precio-valor {
    font-size: 1.6rem;
  }
  
  .producto-acciones {
    flex-direction: column;
    gap: 0.8rem;
  }
}
</style>
