# 🚀 Stark Industries - Catálogo de Productos

Un catálogo interactivo de productos inspirado en Stark Industries, desarrollado con Vue 3 y un diseño futurista del año 3000.

## ✨ Características

- **Componentización jerárquica**: Estructura de componentes padre-hijo bien definida
- **Props dinámicas**: Comunicación eficiente entre componentes
- **Emisión de eventos**: Sistema de eventos para agregar productos al carrito
- **Slots reutilizables**: Contenido personalizable para cada producto
- **Componentes dinámicos**: Modal de detalles que se renderiza dinámicamente
- **Hooks del ciclo de vida**: Monitoreo completo del ciclo de vida de los componentes
- **Estilos dinámicos**: Indicadores visuales de stock en tiempo real
- **Vista completa del carrito**: Interfaz dedicada con gestión completa de productos
- **Gestión de cantidades**: Control de cantidad de productos en el carrito
- **Cálculos automáticos**: Subtotal, impuestos y total calculados dinámicamente
- **Sistema de autenticación**: Login y registro con localStorage
- **Perfil de usuario completo**: Gestión de datos personales y configuración
- **Sistema de asesores**: Asignación automática de asesores especializados
- **Historial de compras**: Registro completo de todas las compras realizadas
- **Diseño futurista**: Inspirado en la estética de Stark Industries

## 🛠️ Tecnologías Utilizadas

- **Vue 3** - Framework de JavaScript reactivo
- **Vite** - Herramienta de construcción rápida
- **CSS3** - Estilos avanzados con gradientes y animaciones
- **Google Fonts** - Tipografía Orbitron para el look futurista

## 🚀 Instalación y Ejecución

1. **Instalar dependencias**:
   ```bash
   npm install
   ```

2. **Ejecutar en modo desarrollo**:
   ```bash
   npm run dev
   ```

3. **Abrir en el navegador**:
   ```
   http://localhost:3000
   ```

## 📁 Estructura del Proyecto

```
src/
├── components/
│   ├── CatalogoProductos.vue    # Componente padre principal
│   ├── TarjetaProducto.vue      # Componente hijo reutilizable
│   ├── DetalleProducto.vue      # Modal dinámico de detalles
│   ├── VistaCarrito.vue         # Vista completa del carrito
│   ├── LoginModal.vue           # Modal de inicio de sesión
│   ├── RegistroModal.vue        # Modal de registro de usuario
│   └── PerfilUsuario.vue        # Perfil completo del usuario
├── App.vue                       # Componente raíz con autenticación
├── main.js                       # Punto de entrada
└── style.css                     # Estilos globales
```

## 🎯 Funcionalidades Implementadas

### Componentización y Jerarquía
- ✅ `CatalogoProductos.vue` - Componente padre principal
- ✅ `TarjetaProducto.vue` - Componente hijo reutilizable con props

### Props Dinámicas
- ✅ `nombre`, `precio`, `imagen`, `stock`, `descripcion`
- ✅ Validación de tipos y valores requeridos

### Emisión de Eventos
- ✅ Botón "Agregar al carrito" que emite evento con ID del producto
- ✅ Botón "Ver detalles" para mostrar información completa

### Slots Reutilizables
- ✅ Slot `contenido-personalizado` para etiquetas de oferta y recomendación
- ✅ Contenido dinámico basado en propiedades del producto

### Componentes Dinámicos
- ✅ `DetalleProducto.vue` se renderiza dinámicamente
- ✅ Modal con información completa del producto

### Hooks del Ciclo de Vida
- ✅ `created()` - Componente creado
- ✅ `mounted()` - Componente montado en el DOM
- ✅ `beforeUnmount()` - Componente a punto de ser destruido
- ✅ `unmounted()` - Componente destruido

### Estilos Dinámicos
- ✅ Indicadores visuales de stock (disponible, bajo stock, agotado)
- ✅ Clases CSS dinámicas basadas en el estado del producto

### Vista del Carrito
- ✅ `VistaCarrito.vue` - Interfaz completa de gestión del carrito
- ✅ Control de cantidades con botones incrementar/decrementar
- ✅ Eliminación individual de productos del carrito
- ✅ Cálculo automático de subtotal, impuestos (10%) y total
- ✅ Botón para limpiar todo el carrito
- ✅ Procesamiento de compra con confirmación
- ✅ Restauración automática del stock al eliminar productos

### Sistema de Autenticación
- ✅ `LoginModal.vue` - Modal de inicio de sesión con validaciones
- ✅ `RegistroModal.vue` - Modal de registro con formulario completo
- ✅ Persistencia de sesión con localStorage
- ✅ Validación de credenciales y manejo de errores
- ✅ Navegación fluida entre login y registro

### Perfil de Usuario
- ✅ `PerfilUsuario.vue` - Interfaz completa de gestión de perfil
- ✅ **Información Personal**: Edición de datos básicos
- ✅ **Seguridad**: Cambio de contraseña con validaciones
- ✅ **Mis Compras**: Historial completo de compras realizadas
- ✅ **Mi Asesor**: Información del asesor asignado con contacto
- ✅ **Configuración**: Preferencias de notificaciones y privacidad
- ✅ Cambio de foto de perfil con upload de archivos
- ✅ Guardado automático en localStorage

## 🎨 Diseño Stark Industries

El diseño está inspirado en la estética futurista de Stark Industries:

- **Colores**: Azul cian (#00ffff) y magenta (#ff00ff) como colores principales
- **Tipografía**: Orbitron para un look tecnológico
- **Efectos**: Gradientes, brillos, animaciones de escaneo
- **Interfaz**: Elementos flotantes, bordes luminosos, efectos de partículas

## 🔧 Scripts Disponibles

- `npm run dev` - Servidor de desarrollo
- `npm run build` - Construcción para producción
- `npm run preview` - Vista previa de la construcción

## 📱 Responsive Design

El catálogo es completamente responsive y se adapta a diferentes tamaños de pantalla:
- Desktop: Grid de 3-4 columnas
- Tablet: Grid de 2 columnas
- Mobile: Grid de 1 columna

## 🎮 Interactividad

- **Hover effects**: Animaciones suaves al pasar el mouse
- **Click events**: Navegación entre productos y detalles
- **Carrito flotante**: Indicador visual del carrito de compras
- **Modal dinámico**: Detalles del producto en ventana emergente
- **Vista del carrito**: Interfaz completa para gestionar productos seleccionados
- **Navegación fluida**: Transición suave entre catálogo y carrito
- **Gestión de stock**: Actualización automática del inventario

## 🛒 Funcionalidades del Carrito

### Gestión de Productos
- **Agregar productos**: Desde tarjetas o modal de detalles
- **Control de cantidades**: Botones para incrementar/decrementar
- **Eliminar productos**: Botón individual para cada producto
- **Limpiar carrito**: Eliminar todos los productos de una vez

### Cálculos Automáticos
- **Subtotal**: Suma de precios × cantidades
- **Impuestos**: 10% sobre el subtotal
- **Total**: Subtotal + impuestos
- **Actualización en tiempo real**: Los cálculos se actualizan automáticamente

### Experiencia de Usuario
- **Estado vacío**: Mensaje cuando no hay productos
- **Confirmaciones**: Alertas para acciones importantes
- **Restauración de stock**: Al eliminar productos del carrito
- **Procesamiento de compra**: Simulación de checkout con J.A.R.V.I.S.

## 🔐 Sistema de Autenticación

### Login y Registro
- **Formularios completos**: Con validaciones en tiempo real
- **Persistencia de sesión**: Con localStorage para mantener login
- **Validación de credenciales**: Verificación de email y contraseña
- **Manejo de errores**: Mensajes claros para el usuario
- **Navegación fluida**: Cambio fácil entre login y registro

### Gestión de Usuarios
- **Registro completo**: Nombre, empresa, email, teléfono, área de interés
- **Asignación de asesor**: Automática según área de interés
- **Foto de perfil**: Upload y cambio de imagen personal
- **Configuración de cuenta**: Preferencias y notificaciones

## 👤 Perfil de Usuario

### Secciones del Perfil
1. **Información Personal**
   - Edición de datos básicos (nombre, empresa, email, teléfono)
   - Cambio de área de interés
   - Fecha de registro

2. **Seguridad**
   - Cambio de contraseña con validaciones
   - Verificación de contraseña actual
   - Confirmación de nueva contraseña

3. **Mis Compras**
   - Historial completo de compras realizadas
   - Detalles de cada compra (productos, cantidades, precios)
   - Estados de compra (completado, procesando, pendiente)
   - Fechas y totales

4. **Mi Asesor**
   - Información del asesor asignado
   - Especialidad y experiencia
   - Contacto directo y videollamada
   - Mensajes recientes del asesor

5. **Configuración**
   - Preferencias de notificaciones
   - Configuración de privacidad
   - Opciones de perfil público/privado

### Sistema de Asesores
- **Asignación automática**: Según área de interés del usuario
- **Especialistas disponibles**:
  - Dr. Bruce Banner (Tecnología Avanzada)
  - Col. James Rhodes (Sistemas de Defensa)
  - Dr. Helen Cho (Energía Limpia)
  - Dr. Erik Selvig (Investigación y Desarrollo)
  - Pepper Potts (Consultoría Tecnológica)
- **Contacto directo**: Email y videollamada
- **Mensajes**: Sistema de comunicación con el asesor

¡Disfruta explorando el catálogo de tecnología avanzada de Stark Industries! 🚀
