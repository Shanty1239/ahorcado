<template>
  <div class="Inicio" v-if="pantallaInicio">
    <div class="Titulo">
      <h1>AHO</h1>
    </div>

    <div class="res">
      <input v-model="nombre" type="text" id="nombre" placeholder="Nombre del Jugador" />
      <select v-model="nivel" id="Nivel">
        <option value="" disabled selected>Selecciona una opción...</option>
        <option value="libre">Fácil</option>
        <option value="ocupado">Medio</option>
        <option value="reservado">Difícil</option>
      </select>
      <button @click="iniciarjuego">Jugar</button>
    </div>
  </div>

  <div class="juego" v-else>
    <div class="segundaPantalla">
      <h2 class="saludo">¡Buena suerte, {{ nombre }}!</h2>

      <!-- Palabra oculta -->
      <div class="palabra">
        <span v-for="(letra, index) in palabra" :key="index">
          {{ letrasCorrectas.includes(letra) ? letra : '_' }}
        </span>
      </div>

      <!-- Entrada de letra -->
      <input
        v-model="letraIngresada"
        maxlength="1"
        @keyup.enter="verificarLetra"
        placeholder="Ingresa una letra"
        class="input-letra"
      />

      <!-- Letras incorrectas -->
      <p class="fallidas">Letras fallidas: {{ letrasIncorrectas.join(', ') }}</p>
      <p class="fallidas">Intentos restantes: {{ intentosRestantes }}</p>

      <!-- Resultado -->
      <p v-if="ganaste" class="ganaste">¡Ganaste! 🎉</p>
      <p v-if="perdiste" class="perdiste">Perdiste. La palabra era: {{ palabra }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const pantallaInicio = ref(true)
const nombre = ref('')
const nivel = ref('')

// lógica del juego
const palabras = ['sol', 'luna', 'estrella', 'planeta', 'cometa']
const palabra = ref(palabras[Math.floor(Math.random() * palabras.length)])

const letrasCorrectas = ref([])
const letrasIncorrectas = ref([])
const letraIngresada = ref('')
const intentosMaximos = 6

const intentosRestantes = computed(() => intentosMaximos - letrasIncorrectas.value.length)

const ganaste = computed(() =>
  [...new Set(palabra.value)].every((letra) => letrasCorrectas.value.includes(letra))
)

const perdiste = computed(() => intentosRestantes.value <= 0 && !ganaste.value)

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
  if (nombre.value && nivel.value) {
    pantallaInicio.value = false
  } else {
    alert('Por favor ingresa tu nombre y selecciona una dificultad.')
  }
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

select, button, input {
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
  background-image: url(./img/fondo.jpeg);
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