<template>
  <q-layout view="lHh Lpr lFf" class="bg-grey-1">
    <q-page-container>
      <q-page class="q-pa-lg flex flex-center">
        <div class="container q-gutter-y-xl">
          <div class="text-center q-mb-xl">
            <h1 class="text-h3 text-primary text-bold q-mb-xs">🛒Carrito de Compras🛒</h1>
          </div>

          <div class="row q-col-gutter-xl justify-center">
            <div class="col-12 col-md-7">
              <q-card class="shadow-5 rounded-borders q-pa-lg">
                <div class="text-h5 text-primary q-mb-lg flex items-center">
                  <q-icon name="inventory_2" class="q-mr-sm" size="28px" />
                  <span class="border-bottom-primary">Productos Disponibles</span>
                </div>

                <div class="row q-col-gutter-lg">
                  <div
                    v-for="producto in productos"
                    :key="producto.id"
                    class="col-6 col-sm-4"
                  >
                    <q-card class="column full-height rounded-borders shadow-2 product-card hoverable">
                      <q-img
                        :src="producto.imagen"
                        :alt="producto.nombre"
                        style="height: 150px;"
                        class="rounded-borders"
                      />
                      <q-card-section class="text-center q-pt-none">
                        <div class="text-subtitle2 text-weight-bold text-grey-9 q-mb-xs">
                          {{ producto.nombre }}
                        </div>
                        <div class="q-mb-sm">
                          <q-badge color="primary" class="text-subtitle2 q-px-sm q-py-xs">
                            ${{ formatCOP(producto.precio) }}
                          </q-badge>
                        </div>
                        <q-btn
                          color="primary"
                          icon="add_shopping_cart"
                          label="Agregar"
                          size="sm"
                          class="full-width rounded-borders"
                          @click="agregarAlCarrito(producto)"
                        />
                      </q-card-section>
                    </q-card>
                  </div>
                </div>
              </q-card>
            </div>

            <div class="col-12 col-md-5">
              <q-card class="shadow-5 rounded-borders q-pa-lg sticky-card">
                <div class="text-h5 text-secondary q-mb-lg flex items-center">
                  <q-icon name="receipt_long" class="q-mr-sm" size="28px" />
                  <span class="border-bottom-secondary">Resumen del Pedido</span>
                </div>

                <div v-if="carrito.length > 0">
                  <div class="q-mb-lg">
                    <q-list bordered class="rounded-borders">
                      <q-item 
                        v-for="item in carrito" 
                        :key="item.id"
                        class="rounded-borders q-mb-sm shadow-1"
                      >
                        <q-item-section avatar>
                          <q-avatar size="50px" rounded class="shadow-2">
                            <img :src="item.imagen" :alt="item.nombre" />
                            <q-badge 
                              v-if="item.cantidad > 1" 
                              color="negative" 
                              floating
                              class="text-caption"
                            >
                              {{ item.cantidad }}
                            </q-badge>
                          </q-avatar>
                        </q-item-section>
                        <q-item-section>
                          <q-item-label class="text-weight-medium text-grey-9">{{ item.nombre }}</q-item-label>
                          <q-item-label caption class="text-grey-7">
                            ${{ formatCOP(item.precio) }} × {{ item.cantidad }}
                          </q-item-label>
                        </q-item-section>
                        <q-item-section side class="q-gutter-xs">
                          <q-btn
                            dense
                            flat
                            round
                            color="positive"
                            icon="add_circle"
                            size="18px"
                            @click="aumentarCantidad(item.id)"
                          />
                          <q-btn
                            dense
                            flat
                            round
                            color="negative"
                            icon="remove_circle"
                            size="18px"
                            @click="disminuirCantidad(item.id)"
                          />
                        </q-item-section>
                      </q-item>
                    </q-list>
                  </div>

                  <q-card class="bg-blue-1 rounded-borders q-pa-md total-card">
                    <div class="text-body1 text-grey-8">
                      <div class="row items-center q-mb-sm">
                        <div class="col">Total de artículos:</div>
                        <div class="col-auto text-h6 text-weight-bold text-primary">
                          {{ totalItems }}
                        </div>
                      </div>
                      <div class="row q-mb-xs">
                        <div class="col">Subtotal:</div>
                        <div class="col-auto text-grey-9">${{ formatCOP(subtotal) }}</div>
                      </div>
                      <div class="row q-mb-xs">
                        <div class="col">Impuesto (16%):</div>
                        <div class="col-auto text-grey-9">${{ formatCOP(impuesto) }}</div>
                      </div>
                      <q-separator spaced class="q-my-sm" />
                      <div class="row items-center">
                        <div class="col text-h6 text-primary text-weight-medium">Total Final:</div>
                        <div class="col-auto text-h4 text-weight-bold text-primary">
                          ${{ formatCOP(totalFinal) }}
                        </div>
                      </div>
                    </div>
                  </q-card>

                  <div class="q-mt-lg q-gutter-sm">
                    <q-btn
                      color="primary"
                      icon="shopping_cart_checkout"
                      label="Proceder al Pago"
                      class="full-width rounded-borders q-py-sm"
                      size="lg"
                    />
                    <q-btn
                      color="grey-7"
                      icon="arrow_back"
                      label="Seguir Comprando"
                      outline
                      class="full-width rounded-borders q-py-sm"
                      size="lg"
                    />
                  </div>
                </div>

                <div v-else class="text-center q-pa-xl">
                  <q-icon name="shopping_bag" size="330px" color="grey-4" class="q-mb-md" />
                  <div class="text-h6 text-grey-7 q-mb-xs">Tu carrito está vacío</div>
                  <div class="text-caption text-grey-5 q-mb-lg">Agrega algunos productos</div>
                </div>
              </q-card>
            </div>
          </div>
        </div>
      </q-page>
    </q-page-container>

    <transition name="slide-down">
      <div v-if="mostrarNotificacion" class="notificacion-custom" :class="notificacion.tipo">
        <q-icon :name="notificacion.icono" size="28px" class="q-mr-sm" />
        <div class="notificacion-texto">{{ notificacion.mensaje }}</div>
      </div>
    </transition>
  </q-layout>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const mostrarNotificacion = ref(false)
const notificacion = ref({ mensaje: '', tipo: '', icono: '' })

function mostrarToast(mensaje, tipo = 'positivo', icono = 'check_circle') {
  notificacion.value = { mensaje, tipo, icono }
  mostrarNotificacion.value = true
  setTimeout(() => (mostrarNotificacion.value = false), 2000)
}

const formatCOP = (precio) => Math.round(precio).toLocaleString('es-CO')

const productos = ref([
  { id: 1, nombre: 'AirPods Pro', precio: 1300000, imagen: 'https://co.tiendasishop.com/cdn/shop/files/IMG-14912675.jpg?v=1726874179&width=1000' },
  { id: 2, nombre: 'Iphone 17 PRO MAX', precio: 7000000, imagen: 'https://tigocolombia.vteximg.com.br/arquivos/ids/167435-1000-1000/iPhone_17_Pro_Max_Silver_PDP_Image_Position_1__COES.jpg?v=638932906935430000' },
  { id: 3, nombre: 'Bloqueador Afelius Oil Free', precio: 112000, imagen: 'https://www.lineaestetica.co/wp-content/uploads/2023/06/98-1-SIEGFRIED-Afelius-Oil-Free-60Gr-linea-estetica.jpg' },
  { id: 4, nombre: 'Monitor 40"', precio: 850000, imagen: 'https://challengerco.vteximg.com.br/arquivos/ids/166877-1000-1000/7705191045375_001.jpg.jpg?v=638775569451930000' },
  { id: 5, nombre: 'Johnson Champú', precio: 25000, imagen: 'https://images.ctfassets.net/3vnc73o2e0fb/4eDLhWuQEodDjIogMG1eBj/458437ec5b64b439bd876ea92e89bdb8/JNB_NA_US_381371177301_111773016_449509_Baby_Gold_Shampoo_13p6oz_00000_copy.webp?fm=webp&w=3840' },
  { id: 6, nombre: 'Galletas Navidad', precio: 25000, imagen: 'https://encrypted-tbn3.gstatic.com/shopping?q=tbn:ANd9GcS4IGNpZQJgIBBdHXG6_v3fkaCq7ZCLCakV91MvUJC26m2yzPMqdWRvydgHoFdShUKpeKjkVspd0t6iLzZgvXj1XVZDFin-uOa_AN8idmT661nZIHdllww1RQ' }
])

const carrito = ref([])

onMounted(() => {
  const guardado = localStorage.getItem('carrito')
  if (guardado) carrito.value = JSON.parse(guardado)
})

const agregarAlCarrito = (producto) => {
  const existente = carrito.value.find(p => p.id === producto.id)
  if (existente) {
    existente.cantidad++
    mostrarToast(`+1 ${producto.nombre} agregado`, 'positivo', 'add_shopping_cart')
  } else {
    carrito.value.push({ ...producto, cantidad: 1 })
    mostrarToast(`${producto.nombre} agregado al carrito`, 'positivo', 'add_shopping_cart')
  }
}

const aumentarCantidad = (id) => {
  const item = carrito.value.find(p => p.id === id)
  if (item) item.cantidad++
}

const disminuirCantidad = (id) => {
  const item = carrito.value.find(p => p.id === id)
  if (item) {
    if (item.cantidad > 1) item.cantidad--
    else eliminarDelCarrito(id)
  }
}

const eliminarDelCarrito = (id) => {
  const item = carrito.value.find(p => p.id === id)
  carrito.value = carrito.value.filter(p => p.id !== id)
  if (item) mostrarToast(`${item.nombre} eliminado del carrito`, 'negativo', 'remove_circle')
}

const totalItems = computed(() => carrito.value.reduce((a, i) => a + i.cantidad, 0))
const subtotal = computed(() => carrito.value.reduce((a, i) => a + i.precio * i.cantidad, 0))
const impuesto = computed(() => subtotal.value * 0.16)
const totalFinal = computed(() => subtotal.value + impuesto.value)

watch(carrito, (nuevo) => {
  localStorage.setItem('carrito', JSON.stringify(nuevo))
}, { deep: true })

watch(totalFinal, (nuevo) => {
  if (nuevo > 5000000) {
    mostrarToast('⚠️ El total supera los $5.000.000', 'advertencia', 'warning')
  }
})
</script>

<style scoped>
.bg-grey-1 {
  background: linear-gradient(135deg, #f5f7fa 0%, #e4edf5 100%);
  min-height: 100vh;
}

.container {
  max-width: 1200px;
  width: 100%;
}

.border-bottom-primary {
  padding-bottom: 8px;
  border-bottom: 3px solid #1976d2;
}

.border-bottom-secondary {
  padding-bottom: 8px;
  border-bottom: 3px solid #26a69a;
}

.product-card {
  transition: all 0.3s ease;
}

.product-card:hover {
  transform: translateY(-5px);
}

.hoverable:hover {
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}

.sticky-card {
  position: sticky;
  top: 20px;
}

.total-card {
  border-left: 4px solid #1976d2;
}

.rounded-borders {
  border-radius: 12px;
}

.notificacion-custom {
  position: fixed;
  top: 40px; 
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  align-items: center;
  background-color: white;
  color: #333;
  border-radius: 16px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.25);
  padding: 20px 30px; 
  min-width: 400px; 
  z-index: 9999;
  font-weight: 600;
  font-size: 18px;
}

.notificacion-custom q-icon {
  font-size: 40px;
}


.notificacion-texto {
  flex: 1;
}

.notificacion-custom.positivo {
  border-left: 5px solid #21ba45;
}

.notificacion-custom.negativo {
  border-left: 5px solid #c10015;
}

.notificacion-custom.advertencia {
  border-left: 5px solid #f2c037;
}

.slide-down-enter-active,
.slide-down-leave-active {
  transition: all 0.4s ease;
}

.slide-down-enter-from {
  opacity: 0;
  transform: translate(-50%, -20px);
}

.slide-down-leave-to {
  opacity: 0;
  transform: translate(-50%, -20px);
}
</style>