<template>
  <div>
    <div
      v-for="(shape, index) in shapes"
      :key="index"
      :style="{
        position: 'absolute',
        left: shape.x + 'px',
        top: shape.y + 'px',
        width: '70px',
        height: '70px',
        display: 'flex',
        justifyContent: 'center',
        alignItems: 'center',
        transition: 'filter 0.3s ease',
        filter: `drop-shadow(0 0 3px ${shape.color}) drop-shadow(0 0 6px ${shape.color})`,
        cursor: 'pointer',
      }"
      @mousedown="handleMouseDown(shape.id)"
      @mouseenter="e => e.target.style.filter = `drop-shadow(0 0 15px ${shape.color}) drop-shadow(0 0 30px ${shape.color})`"
      @mouseleave="e => e.target.style.filter = `drop-shadow(0 0 3px ${shape.color}) drop-shadow(0 0 6px ${shape.color})`"
    >
      <div
        :style="{
          width: '100%',
          height: '100%',
          backgroundColor: shape.color,
          borderRadius: shape.shape === 'circulo' ? '50%' : '0%',
          clipPath: getClipPath(shape.shape)
        }"
      ></div>
    </div>
    <div id="Contenedor">
      <h2>Cascada Geométrica</h2>
      <h2>Bryan Fausto Coaguila Torres</h2>
      <img src="../assets/vue.svg" alt="Vue logo" />


      <button id="agregarBtn" @click="addShape">Agregar Figura</button>
      <button id="clearBtn" @click="shapes = []">Limpiar</button>
    
    </div>
  </div>
</template>



<script setup>
import { ref, onMounted } from 'vue';

const paleta = ['#FF0033', '#FF6600', '#0099FF', '#FFFFFF'];


const shapes = ref([]);


const isMouseDown = ref(null);
const mouseDownTime = ref(0);
let timeRef = null;
let animationRef = null;


const getRandomColor = () => paleta[Math.floor(Math.random() * paleta.length)];

const getRandomShape = () => {
  const shapes = ['cuadrado', 'circulo', 'triangulo', 'pentagono'];
  return shapes[Math.floor(Math.random() * shapes.length)];
};


const addShape = () => {
  shapes.value.push({
    id: shapes.value.length,
    x: Math.random() * (window.innerWidth - 50),
    y: 0,
    speed: 0,
    shape: getRandomShape(),
    color: getRandomColor(),
  });
};

onMounted(() => {
  const updatePositions = () => {
    shapes.value = shapes.value.map((shape) => {
      const windowH = window.innerHeight;
      const shapeHeight = 70;
      const floor = windowH - shapeHeight;

      if (shape.id === isMouseDown.value) {
        return {
          ...shape,
          y: Math.max(shape.y + shape.speed / 60, 0),
          speed: shape.speed - 0.5,
        };
      } else if (shape.y <= 0) {
        return {
          ...shape,
          y: shape.y + shape.speed / 60,
          speed: 30,
        };
      } else if (shape.y >= floor) {
        return {
          ...shape,
          y: floor,
          speed: 0,
        };
      } else {
        if (shape.speed <= 0) {
          return {
            ...shape,
            y: shape.y + shape.speed / 60,
            speed: shape.speed + 1,
          };
        } else {
          return {
            ...shape,
            y: shape.y + shape.speed / 60,
            speed: shape.y < floor ? shape.speed + 1 : 0,
          };
        }
      }
    });
    animationRef = requestAnimationFrame(updatePositions);
  };

  animationRef = requestAnimationFrame(updatePositions);

  return () => cancelAnimationFrame(animationRef);
});


const handleMouseDown = (id) => {
  isMouseDown.value = id;
  mouseDownTime.value = 0;
  timeRef = setInterval(() => {
    mouseDownTime.value += 100;
  }, 100);
};


const getClipPath = (shape) => {
  if (shape === 'triangulo') return 'polygon(50% 0%, 0% 100%, 100% 100%)';
  if (shape === 'pentagono') return 'polygon(50% 0%, 100% 38%, 82% 100%, 18% 100%, 0% 38%)';
  return 'none';
};

</script>
<style>
@import './Home.css';
</style>