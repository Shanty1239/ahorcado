<template>
  <!-- Pantalla inicio -->
  <div class="Inicio" v-if="pantallaInicio">
    <div class="Titulo">
      <h1>AHORCADO</h1>
    </div>

    <div class="res">
      <input v-model="nombreJugador" type="text" placeholder="Nombre del Jugador" />

      <!-- Select de nivel -->
      <select v-model="nivel">
        <option value="" disabled selected>Selecciona dificultad...</option>
        <option value="facil">Fácil</option>
        <option value="medio">Medio</option>
        <option value="dificil">Difícil</option>
      </select>

      <!-- Select de categoría -->
      <select v-model="categoria" :disabled="!nivel">
        <option value="" disabled selected>Selecciona categoría...</option>
        <option v-for="(cat, i) in categoriasDisponibles" :key="i" :value="cat">
          {{ cat }}
        </option>
      </select>

      <button @click="iniciarjuego" :disabled="!nivel || !categoria">Jugar</button>
    </div>
  </div>

  <!-- Pantalla juego -->
  <div class="juego" v-else>
    <div class="segundaPantalla">
      <p class="saludo">Bienvenido, {{ nombreJugador || 'Jugador' }} 👾</p>

      <!-- Astronauta -->
      <div class="astronauta">
        <img v-for="(parte, index) in imagenActual" :key="index" :src="parte" :class="['parte', `parte-${index}`]" />
      </div>

      <!-- Palabra -->
      <div class="palabra">
        <span v-for="(letra, i) in palabra" :key="i">
          {{ letrasCorrectas.includes(letra) ? letra : '_' }}
        </span>
      </div>

      <!-- Entrada -->
      <input v-model="letraIngresada" maxlength="1" @keyup.enter="verificarLetra"
        placeholder="Ingresa una letra" class="input-letra" />

      <p class="fallidas">Letras fallidas: {{ letrasIncorrectas.join(', ') }}</p>
      <p>Intentos restantes: {{ intentosRestantes }}</p>

      <p v-if="ganaste" class="ganaste">🎉 ¡Ganaste!</p>
      <p v-if="perdiste" class="perdiste">💀 Perdiste. La palabra era: {{ palabra }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// estado pantallas
const pantallaInicio = ref(true)
const nombreJugador = ref('')
const nivel = ref('')
const categoria = ref('')

// Palabras organizadas por nivel y categoría
const palabrasPorNivel = {
  facil: {
    animales: ['gato', 'perro', 'lobo', 'pez'],
    frutas: ['uva', 'kiwi', 'mango', 'pera'],
    espacio: ['sol', 'luna', 'marte', 'roca']
  },
  medio: {
    animales: ['jirafa', 'delfin', 'caballo', 'conejo'],
    frutas: ['sandia', 'papaya', 'cereza', 'banano'],
    espacio: ['galaxia', 'cometa', 'asteroide', 'orbita']
  },
  dificil: {
    animales: ['hipopotamo', 'ornitorrinco', 'canguro', 'armadillo'],
    frutas: ['granadilla', 'frambuesa', 'maracuya', 'arándano'],
    espacio: ['constelacion', 'supernova', 'nebulosa', 'exoplaneta']
  }
}

const palabra = ref('')
const letrasCorrectas = ref([])
const letrasIncorrectas = ref([])
const letraIngresada = ref('')

// ahora son 5 intentos porque hay 5 partes
const intentosMaximos = 5

// imágenes del astronauta en orden de aparición
const partesAstronauta = [
  new URL('./assets/astronauta/casco.png', import.meta.url).href,
  new URL('./assets/astronauta/tronco.png', import.meta.url).href,
  new URL('./assets/astronauta/brazo_izquierdo.png', import.meta.url).href,
  new URL('./assets/astronauta/brazo_derecho.png', import.meta.url).href,
  new URL('./assets/astronauta/piernas.png', import.meta.url).href
]

const intentosRestantes = computed(() => intentosMaximos - letrasIncorrectas.value.length)

const ganaste = computed(() =>
  [...new Set(palabra.value)].every((letra) => letrasCorrectas.value.includes(letra))
)

const perdiste = computed(() => intentosRestantes.value <= 0 && !ganaste.value)

// Imagen acumulada según errores
const imagenActual = computed(() => partesAstronauta.slice(0, letrasIncorrectas.value.length))

// categorías disponibles según nivel
const categoriasDisponibles = computed(() => (nivel.value ? Object.keys(palabrasPorNivel[nivel.value]) : []))

function verificarLetra() {
  const letra = letraIngresada.value.toLowerCase()

  if (!letra.match(/[a-zñ]/) || letra.length !== 1) {
    letraIngresada.value = ''
    return
  }

  if (letrasCorrectas.value.includes(letra) || letrasIncorrectas.value.includes(letra)) {
    letraIngresada.value = ''
    return
  }

  if (palabra.value.includes(letra)) {
    letrasCorrectas.value.push(letra)
  } else {
    letrasIncorrectas.value.push(letra)
  }

  letraIngresada.value = ''
}

function iniciarjuego() {
  if (!nivel.value || !categoria.value) return

  const listaPalabras = palabrasPorNivel[nivel.value][categoria.value]
  palabra.value = listaPalabras[Math.floor(Math.random() * listaPalabras.length)]

  letrasCorrectas.value = []
  letrasIncorrectas.value = []
  letraIngresada.value = ''
  pantallaInicio.value = false
}
</script>


<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

body {
  margin: 0;
  padding: 0;
  background: linear-gradient(to right, #0E000F, #480079, #900131, #23578A);
  font-family: 'Press Start 2P', cursive;
}

.Inicio {
  width: 90vw;
  height: 90vh;
  margin: 5vh auto;
  background-image: url(https://i.pinimg.com/originals/47/0c/a6/470ca6289e3500ca59375cf5ef12bca3.gif);
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
}

.Titulo {
  width: 500px;
  text-align: center;
  background: linear-gradient(to right, #020031, #480079, #900131);
  border-radius: 10px;
  margin-bottom: 20px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.7);
  padding: 15px;
  font-size: 1.2rem;
}

.Titulo h1 {
  background: linear-gradient(to right, #C7FEFE, #84DDFF, #61B6EA, #225588);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  font-weight: bold;
}

.res {
  display: flex;
  flex-direction: column;
  background: linear-gradient(to right, #020031, #900131);
  padding: 30px;
  border-radius: 15px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.7);
}

/* Contenedor selects */
.controles {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 10px 0;
}

.controles label {
  color: #84DDFF;
  font-size: 0.9rem;
  margin-top: 10px;
  text-shadow: 1px 1px 3px #000;
}

select,
button,
input {
  margin: 10px;
  padding: 10px 20px;
  font-size: 1rem;
  border-radius: 5px;
  border: none;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.7);
  color: #333;
  background-color: #84DDFF;
  transition: background-color 0.3s, color 0.3s;
  font-family: 'Press Start 2P', cursive;
}

button:hover,
input:hover,
select:hover {
  background-color: #225588;
  color: white;
}

/* SEGUNDA PANTALLA */
.juego {
  width: 90vw;
  height: 90vh;
  margin: 5vh auto;
  border-radius: 20px;
  background-image: url(./assets/fondo.jpeg);
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  display: flex;
  justify-content: center;
  align-items: center;
}

.segundaPantalla {
  background-color: rgba(0, 0, 0, 0.7);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  width: 600px;
  color: #fff;
}

.saludo {
  font-size: 1rem;
  margin-bottom: 30px;
  color: #84DDFF;
  text-shadow: 1px 1px 3px #000;
}

.astronauta {
  position: relative;
  width: 200px;
  height: 320px;
  margin: 20px auto;
}

.parte {
  position: absolute;
  image-rendering: pixelated;
}

/* casco */
.parte-0 {
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
}

/* tronco */
.parte-1 {
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
  width: 140px;
}

/* brazo izquierdo */
.parte-2 {
  top: 110px;
  left: 0;
  transform: none;
  width: 70px;
}

/* brazo derecho */
.parte-3 {
  top: 110px;
  right: 0;
  transform: none;
  width: 70px;
}

/* piernas */
.parte-4 {
  top: 200px;
  left: 50%;
  transform: translateX(-50%);
  width: 140px;
}

.palabra {
  font-size: 2rem;
  letter-spacing: 1rem;
  margin-bottom: 20px;
  color: #FDFAC2;
  text-shadow: 1px 1px 2px #000;
}

.input-letra {
  font-size: 1.2rem;
  text-align: center;
}

.fallidas {
  color: #ffdcdc;
  font-weight: bold;
  font-size: 0.8rem;
}

.ganaste {
  color: #B6F2B6;
  font-size: 1rem;
  margin-top: 20px;
}

.perdiste {
  color: #FF8A8A;
  font-size: 1rem;
  margin-top: 20px;
}
</style>

