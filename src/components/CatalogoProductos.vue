<template>
  <div class="catalogo-container">
    <div class="catalogo-header">
      <div class="header-main">
        <h2 class="section-title">Productos Disponibles</h2>
        <div v-if="!usuario" class="welcome-banner">
          <div class="banner-content">
            <h3 class="banner-title">🚀 Tecnología del Futuro</h3>
            <p class="banner-text">Explora nuestra colección completa de tecnología avanzada. Navega libremente y descubre productos innovadores.</p>
            <div class="banner-benefits">
              <span class="benefit">✨ Sin registro requerido</span>
              <span class="benefit">🛒 Compra inmediata</span>
              <span class="benefit">🔒 Registro opcional para beneficios</span>
            </div>
          </div>
        </div>
      </div>
      <div class="header-actions">
        <div class="product-counter">
          <span class="counter-text">{{ productos.length }} productos</span>
          <div class="counter-glow"></div>
        </div>
        <button class="btn-ver-carrito" @click="verCarrito" :class="{ 'carrito-activo': carrito.length > 0 }">
          <span class="carrito-icon">🛒</span>
          <span class="carrito-text">VER CARRITO</span>
          <span class="carrito-count">{{ carrito.length }}</span>
        </button>
      </div>
    </div>

    <div class="productos-grid">
      <TarjetaProducto
        v-for="producto in productos"
        :key="producto.id"
        :id="producto.id"
        :nombre="producto.nombre"
        :precio="producto.precio"
        :imagen="producto.imagen"
        :stock="producto.stock"
        :descripcion="producto.descripcion"
        @agregar-al-carrito="manejarAgregarCarrito"
        @ver-detalle="mostrarDetalle"
      >
        <!-- Slot para contenido personalizado -->
        <template #contenido-personalizado>
          <div v-if="producto.oferta" class="oferta-badge">
            <span class="oferta-text">OFERTA</span>
            <div class="oferta-glow"></div>
          </div>
          <div v-if="producto.recomendado" class="recomendado-badge">
            <span class="recomendado-text">RECOMENDADO</span>
            <div class="recomendado-glow"></div>
          </div>
        </template>
      </TarjetaProducto>
    </div>

    <!-- Componente dinámico para detalles -->
    <DetalleProducto
      v-if="productoSeleccionado"
      :producto="productoSeleccionado"
      @cerrar="cerrarDetalle"
      @agregar-al-carrito="manejarAgregarCarrito"
    />

    <!-- Carrito Lateral -->
    <CarritoLateral
      :mostrar="mostrarCarrito"
      :carrito="carrito"
      :usuario="usuario"
      @cerrar="volverCatalogo"
      @actualizar-cantidad="actualizarCantidad"
      @eliminar-producto="eliminarProducto"
      @limpiar-carrito="limpiarCarrito"
      @procesar-compra="procesarCompra"
      @procesar-compra-invitado="procesarCompraInvitado"
      @mostrar-login="$emit('mostrar-login')"
      @mostrar-registro="$emit('mostrar-registro')"
    />

    <!-- Carrito flotante -->
    <div class="carrito-flotante" :class="{ 'carrito-activo': carrito.length > 0 }" @click="verCarrito">
      <div class="carrito-icon">
        <span class="carrito-count">{{ carrito.length }}</span>
      </div>
      <div class="carrito-total">
        Total: ${{ calcularTotal() }}
      </div>
    </div>
  </div>
</template>

<script>
import TarjetaProducto from './TarjetaProducto.vue'
import DetalleProducto from './DetalleProducto.vue'
import CarritoLateral from './CarritoLateral.vue'

export default {
  name: 'CatalogoProductos',
  components: {
    TarjetaProducto,
    DetalleProducto,
    CarritoLateral
  },
  props: {
    usuario: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      productoSeleccionado: null,
      mostrarCarrito: false,
      carrito: [],
      productos: [
        {
          id: 1,
          nombre: 'Arc Reactor Mark V',
          precio: 2500000,
          imagen: '/img/reactorarc.jpg',
          stock: 5,
          descripcion: 'Reactor de fusión compacto de nueva generación',
          oferta: true,
          recomendado: false
        },
        {
          id: 2,
          nombre: 'Repulsor Gauntlet',
          precio: 1800000,
          imagen: '/img/guantelete.jpg',
          stock: 0,
          descripcion: 'Guantelete con tecnología repulsora avanzada',
          oferta: false,
          recomendado: true
        },
        {
          id: 3,
          nombre: 'J.A.R.V.I.S. Core',
          precio: 3200000,
          imagen: '/img/jarvis.jpg',
          stock: 3,
          descripcion: 'Sistema de inteligencia artificial avanzado',
          oferta: false,
          recomendado: true
        },
        {
          id: 4,
          nombre: 'Iron Man Helmet',
          precio: 950000,
          imagen: '/img/casco.jpg',
          stock: 8,
          descripcion: 'Casco con HUD integrado y sistemas de comunicación',
          oferta: true,
          recomendado: false
        },
        {
          id: 5,
          nombre: 'Quantum Stabilizer',
          precio: 4200000,
          imagen: '/img/estabilizador.png',
          stock: 2,
          descripcion: 'Estabilizador cuántico para viajes temporales',
          oferta: false,
          recomendado: false
        },
        {
          id: 6,
          nombre: 'Nano Tech Suit',
          precio: 5500000,
          imagen: '/img/suit.jpg',
          stock: 1,
          descripcion: 'Traje de nanotecnología autorreparable',
          oferta: true,
          recomendado: true
        }
      ]
    }
  },
  methods: {
    manejarAgregarCarrito(productoId) {
      const producto = this.productos.find(p => p.id === productoId)
      if (producto && producto.stock > 0) {
        this.carrito.push(producto)
        producto.stock--
        console.log(`Producto ${producto.nombre} agregado al carrito`)
      }
    },
    mostrarDetalle(productoId) {
      this.productoSeleccionado = this.productos.find(p => p.id === productoId)
    },
    cerrarDetalle() {
      this.productoSeleccionado = null
    },
    verCarrito() {
      this.mostrarCarrito = true
      this.productoSeleccionado = null
    },
    volverCatalogo() {
      this.mostrarCarrito = false
    },
    actualizarCantidad(index, nuevaCantidad) {
      // Esta función se puede usar para actualizar cantidades si se implementa
      console.log(`Cantidad actualizada para producto ${index}: ${nuevaCantidad}`)
    },
    eliminarProducto(index) {
      const productoEliminado = this.carrito[index]
      this.carrito.splice(index, 1)
      
      // Restaurar stock del producto
      const productoOriginal = this.productos.find(p => p.id === productoEliminado.id)
      if (productoOriginal) {
        productoOriginal.stock++
      }
      
      console.log(`Producto ${productoEliminado.nombre} eliminado del carrito`)
    },
    limpiarCarrito() {
      // Restaurar stock de todos los productos
      this.carrito.forEach(productoCarrito => {
        const productoOriginal = this.productos.find(p => p.id === productoCarrito.id)
        if (productoOriginal) {
          productoOriginal.stock++
        }
      })
      
      this.carrito = []
      console.log('Carrito limpiado completamente')
    },
    procesarCompra(datosCompra) {
      console.log('Compra procesada:', datosCompra)
      
      // Crear registro de compra
      const nuevaCompra = {
        id: Date.now().toString(),
        fecha: new Date().toISOString(),
        productos: datosCompra.productos.map(producto => ({
          id: producto.id,
          nombre: producto.nombre,
          precio: producto.precio,
          imagen: producto.imagen,
          cantidad: datosCompra.cantidades[datosCompra.productos.indexOf(producto)]
        })),
        total: datosCompra.total,
        estado: 'completado'
      }
      
      // Guardar compra en el usuario si está logueado
      if (this.usuario) {
        this.guardarCompraEnUsuario(nuevaCompra)
      }
      
      this.carrito = []
      this.mostrarCarrito = false
      
      // Mostrar mensaje de confirmación
      alert('¡Compra procesada exitosamente! J.A.R.V.I.S. ha confirmado su pedido.')
    },
    procesarCompraInvitado(datosCompra) {
      console.log('Compra como invitado procesada:', datosCompra)
      
      // No guardar en historial, solo procesar la compra
      this.carrito = []
      this.mostrarCarrito = false
      
      // Mostrar mensaje de confirmación
      alert('¡Compra como invitado procesada exitosamente! J.A.R.V.I.S. ha confirmado su pedido.\n\nNota: Esta compra no se guardará en tu historial. Te recomendamos crear una cuenta para futuras compras.')
    },
    guardarCompraEnUsuario(compra) {
      const usuarios = JSON.parse(localStorage.getItem('starkUsuarios') || '[]')
      const usuarioIndex = usuarios.findIndex(u => u.id === this.usuario.id)
      
      if (usuarioIndex !== -1) {
        if (!usuarios[usuarioIndex].compras) {
          usuarios[usuarioIndex].compras = []
        }
        usuarios[usuarioIndex].compras.unshift(compra)
        localStorage.setItem('starkUsuarios', JSON.stringify(usuarios))
        console.log('✅ Compra guardada en historial del usuario:', compra.id)
        console.log('📊 Total de compras del usuario:', usuarios[usuarioIndex].compras.length)
      } else {
        console.error('❌ Usuario no encontrado para guardar compra')
      }
    },
    calcularTotal() {
      const total = this.carrito.reduce((total, producto) => {
        const precio = producto.precio || 0
        return total + precio
      }, 0)
      return isNaN(total) ? '0' : total.toLocaleString()
    }
  }
}
</script>

<style scoped>
.catalogo-container {
  max-width: 1400px;
  margin: 0 auto;
  position: relative;
}

.catalogo-header {
  margin-bottom: 3rem;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(0, 255, 255, 0.3);
}

.header-main {
  margin-bottom: 2rem;
}

.header-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 2rem;
}

.welcome-banner {
  background: linear-gradient(135deg, rgba(0, 255, 255, 0.1), rgba(255, 0, 255, 0.1));
  border: 2px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  padding: 2rem;
  margin-top: 1rem;
  position: relative;
  overflow: hidden;
}

.welcome-banner::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 255, 255, 0.1), transparent);
  animation: scan 4s infinite;
}

.banner-content {
  position: relative;
  z-index: 2;
}

.banner-title {
  font-size: 1.8rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 20px #00ffff;
  margin-bottom: 1rem;
}

.banner-text {
  color: #ccc;
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: 1.5rem;
}

.banner-benefits {
  display: flex;
  gap: 2rem;
  flex-wrap: wrap;
}

.benefit {
  color: #00ffff;
  font-size: 0.9rem;
  font-weight: 500;
  padding: 0.5rem 1rem;
  background: rgba(0, 255, 255, 0.1);
  border: 1px solid rgba(0, 255, 255, 0.3);
  border-radius: 20px;
  text-shadow: 0 0 10px #00ffff;
}

.section-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: #00ffff;
  text-shadow: 0 0 20px #00ffff;
  animation: slideIn 1s ease-out;
}

.product-counter {
  position: relative;
  display: flex;
  align-items: center;
  gap: 1rem;
}

.counter-text {
  font-size: 1.1rem;
  color: #888;
  font-weight: 400;
}

.counter-glow {
  width: 20px;
  height: 20px;
  background: radial-gradient(circle, #00ffff, transparent);
  border-radius: 50%;
  animation: pulse 2s infinite;
}

.btn-ver-carrito {
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
  display: flex;
  align-items: center;
  gap: 0.8rem;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
}

.btn-ver-carrito:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 30px rgba(0, 255, 255, 0.5);
}

.btn-ver-carrito.carrito-activo {
  background: linear-gradient(45deg, #ff00ff, #8800ff);
  color: #fff;
  box-shadow: 0 0 20px rgba(255, 0, 255, 0.3);
}

.btn-ver-carrito.carrito-activo:hover {
  box-shadow: 0 15px 30px rgba(255, 0, 255, 0.5);
}

.carrito-icon {
  font-size: 1.2rem;
}

.carrito-text {
  font-size: 0.9rem;
}

.carrito-count {
  background: rgba(0, 0, 0, 0.3);
  border-radius: 50%;
  width: 25px;
  height: 25px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 0.8rem;
}

.productos-grid {
  display: grid;
  grid-template-columns: repeat(3, 450px);
  gap: 2rem;
  margin-bottom: 3rem;
  justify-content: center;
}

.carrito-flotante {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  background: linear-gradient(135deg, #1a1a2e, #16213e);
  border: 2px solid #00ffff;
  border-radius: 15px;
  padding: 1rem 1.5rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  box-shadow: 0 0 30px rgba(0, 255, 255, 0.3);
  transform: translateY(100px);
  opacity: 0;
  transition: all 0.3s ease;
  z-index: 1000;
}

.carrito-flotante.carrito-activo {
  transform: translateY(0);
  opacity: 1;
}

.carrito-icon {
  position: relative;
  width: 40px;
  height: 40px;
  background: linear-gradient(45deg, #00ffff, #ff00ff);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.carrito-count {
  color: #000;
  font-weight: bold;
  font-size: 1.2rem;
}

.carrito-total {
  color: #00ffff;
  font-weight: 600;
  font-size: 1.1rem;
}

@media (max-width: 1024px) {
  .productos-grid {
    grid-template-columns: repeat(3, 400px);
    gap: 1rem;
    justify-content: center;
  }
}

@media (max-width: 900px) {
  .productos-grid {
    grid-template-columns: repeat(3, 350px);
    gap: 0.8rem;
    justify-content: center;
  }
}

@media (max-width: 768px) {
  .catalogo-header {
    text-align: center;
  }
  
  .header-actions {
    flex-direction: column;
    gap: 1rem;
  }
  
  .section-title {
    font-size: 2rem;
  }
  
  .welcome-banner {
    padding: 1.5rem;
    margin-top: 1rem;
  }
  
  .banner-title {
    font-size: 1.5rem;
  }
  
  .banner-text {
    font-size: 1rem;
  }
  
  .banner-benefits {
    flex-direction: column;
    gap: 1rem;
    align-items: center;
  }
  
  .benefit {
    font-size: 0.8rem;
    padding: 0.4rem 0.8rem;
  }
  
  .productos-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
  
  .carrito-flotante {
    bottom: 1rem;
    right: 1rem;
    padding: 0.8rem 1.2rem;
  }
}

@media (max-width: 480px) {
  .productos-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
    margin-bottom: 2rem;
  }
  
  .catalogo-header {
    padding: 1rem;
  }
  
  .section-title {
    font-size: 1.8rem;
  }
  
  .welcome-banner {
    padding: 1rem;
    margin-top: 0.8rem;
  }
  
  .banner-title {
    font-size: 1.3rem;
  }
  
  .banner-text {
    font-size: 0.9rem;
  }
  
  .benefit {
    font-size: 0.75rem;
    padding: 0.3rem 0.6rem;
  }
  
  .carrito-flotante {
    bottom: 0.8rem;
    right: 0.8rem;
    left: 0.8rem;
    padding: 0.6rem 1rem;
    flex-direction: column;
    gap: 0.5rem;
    text-align: center;
  }
  
  .carrito-icon {
    width: 35px;
    height: 35px;
  }
  
  .carrito-count {
    font-size: 1rem;
  }
  
  .carrito-total {
    font-size: 1rem;
  }
}
</style>
