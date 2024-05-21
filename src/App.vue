<template>
  <div id="app">
    <header v-show="startBtn">
      <label for="tempo-blind">Tempo do Blind:</label>
      <div class="form">
        <input type="number" v-model="tempoEscolhido" id="tempo-blind">
        <button @click="adicionar" class="btn btn-form">+</button><button @click="diminuir" class="btn btn-form">-</button>
      </div>
    </header>

    <main>
      <section class="timer">
        <div>{{ minutos }}:{{ segundos }}</div>
      </section>

      <section class="btns">
        <button @click="anterior" class="btn btn-acoes"><span class="material-symbols-outlined">fast_rewind</span></button>
        <button v-show="startBtn" @click="startTimer" class="btn btn-acoes"><span class="material-symbols-outlined">play_arrow</span></button>
        <button v-show="pauseBtn" @click="pauseTimer" class="btn btn-acoes"><span class="material-symbols-outlined">pause</span></button>
        <button @click="finalizar" class="btn btn-acoes"><span class="material-symbols-outlined">stop</span></button>
        <button @click="proximo" class="btn btn-acoes"><span class="material-symbols-outlined">fast_forward</span></button>
      </section>

      <section class="info-blind">
        <p>Nível: {{ level }}</p>

        <div class="blind">
          {{ smallBlind }} / {{ bigBlind }}
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import './styles/global.css';
import { blinds } from './blinds/blinds';
import alert from './assets/alert.mp3';

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
      startBtn: true,
      pauseBtn:false,
      bigBlind: null,
      smallBlind: null,
      level: null,
      tempoFaltante: null,
      index: 0,
      sound: new Audio(alert),
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
    diminuir(){
      if(this.tempoEscolhido > 0){
        this.tempoEscolhido = this.tempoEscolhido - 5;
      }else{
        return false
      }
    },
    startTimer(){
      if(this.tempoEscolhido <= 0 || !this.startBtn){
        return false;
      }

      if(this.pause){
        this.fim = new Date().getTime() + this.tempoFaltante;
        this.pause = false;
      }else{
        this.tempoRegressivo = this.tempoEscolhido * 60 * 1000;
        this.fim = new Date().getTime() + this.tempoRegressivo;
      }

      this.startBtn = false;
      this.pauseBtn = true;

      this.updateTimer();
      this.intervalo = setInterval(this.updateTimer, 1000);

    },
    updateTimer(){
      const now = new Date().getTime();
      const distance = this.fim - now

      if(distance < 0){
        clearInterval(this.intervalo);
        if(this.index+1 >= this.blinds.length){
          this.startBtn = true;
          this.pauseBtn = false
          this.finalizar();
          return false;
        }else{
          this.sound.play();
          this.index++;
          this.startBtn = true;
          this.startTimer();
        }
        
      }else{
        this.tempoFaltante = distance;
        this.minutos = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
        this.segundos = Math.floor((distance % (1000 * 60)) / 1000);
        this.bigBlind = this.blinds[this.index].bigBlind;
        this.smallBlind = this.blinds[this.index].smallBlind;
        this.level = this.blinds[this.index].level;

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
      this.startBtn = true;
      this.pauseBtn = false;
    },
    finalizar(){
      this.startBtn = true;
      this.pauseBtn = false;
      this.resetValores();
    },
    proximo(){
      
      if(this.index + 1 < this.blinds.length){
        clearInterval(this.intervalo);
        this.index++;
        this.pause = false;
        this.startBtn = true;
        this.startTimer();
      }else{
        this.resetValores();
      }
      
    },
    anterior(){
      
      if(this.index + 1 > 1){
        clearInterval(this.intervalo);
        this.index--;
        this.startBtn = true;
        this.pause = false;
        this.startTimer();
      }else{
        return false;
      }
    },
    resetValores(){
      clearInterval(this.intervalo);
      this.tempoFaltante = 0;
      this.tempoRegressivo = 0;
      this.minutos = 0;
      this.segundos = 0;
      this.pause = false;
      this.index = 0;
      this.tempoEscolhido = 0;
      this.fim = 0;
      this.intervalo = 0;
      this.bigBlind = null;
      this.smallBlind = null;
      this.level = null;
      this.startBtn = true;
      this.pauseBtn = false;
      
    }
  }
}
</script>

<style>

</style>
