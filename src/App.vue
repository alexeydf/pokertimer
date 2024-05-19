<template>
  <div id="app">
    <input type="number" v-model="tempoEscolhido">
    <button @click="adicionar" class="btn">+</button><button class="btn">-</button>
    <p>{{ minutos }}:{{ segundos }}</p>
    <p>{{ blind }}</p>
    <button v-show="start" @click="startTimer" class="btn">start</button>
    <button @click="pauseTimer" class="btn">pause</button>
    <button @click="finalizar" class="btn">finalizar</button>
    <button @click="proximo" class="btn">proximo</button>
  </div>
</template>

<script>
import './styles/global.css';
import { blinds } from './blinds/blinds';

export default {
  name: 'App',
  components: {
   
  },
  data(){
    return{
      tempoEscolhido: 0,
      tempoRegressivo: 0,
      fim: null,
      intervalo: null,
      minutos: 0,
      segundos: 0,
      pause: false,
      start: true,
      blind: null,
      tempoFaltante: null,
      index: 0,
      blinds 
    }
   },
   beforeDestroy(){
    this.resetValores();
   },
  methods:{
    adicionar(){
      this.tempoEscolhido = this.tempoEscolhido + 5;
    },
    startTimer(){
      if(this.tempoEscolhido <= 0){
        return false;
      }

      if(this.pause){
        this.fim = new Date().getTime() + this.tempoFaltante;
        this.pause = false;
        this.start = false;
      }else{
        this.start = false;
        this.tempoRegressivo = this.tempoEscolhido * 60 * 1000;
        this.fim = new Date().getTime() + this.tempoRegressivo;
      }

      this.updateTimer();
      this.intervalo = setInterval(this.updateTimer, 1000);

    },
    updateTimer(){
      const now = new Date().getTime();
      const distance = this.fim - now

      if(distance < 0){
      
        if(this.index+1 >= this.blinds.length){
          this.resetValores();
        }else{
          this.index++;
          this.startTimer();
        }
        
      }else{
        this.tempoFaltante = distance;
        this.minutos = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
        this.segundos = Math.floor((distance % (1000 * 60)) / 1000);
        this.blind = this.blinds[this.index];

        if(this.minutos < 10){
          this.minutos = `0${this.minutos}`;
        }
        if(this.segundos < 10){
          this.segundos = `0${this.segundos}`;
        }
      }

      
    },
    pauseTimer(){
      clearInterval(this.intervalo);
      this.pause = true;
      this.start = true;
    },
    finalizar(){
      this.resetValores();
    },
    resetValores(){
      clearInterval(this.intervalo);
      this.tempoFaltante = null;
      this.tempoRegressivo = 0;
      this.minutos = 0;
      this.segundos = 0;
      this.start = true;
      this.pause = false;
      this.index = 0;
      this.tempoEscolhido = 0;
      this.fim = null;
      this.intervalo = null;
      this.blind = null;
    },
    proximo(){
      clearInterval(this.intervalo);
      if(this.index + 1 < this.blinds.length){
        this.index++;
        this.pause = false;
        this.startTimer();
      }else{
        this.resetValores();
      }
      
    }
  }
}
</script>

<style>

</style>
