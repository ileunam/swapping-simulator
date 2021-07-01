<template>
  <v-app>
    <v-main>
      <v-row>
        <v-col cols="4">
          <v-subheader>Cantidad de procesos</v-subheader>
          <v-combobox
            clearable
            persistent-hint
            v-model="processCount"
            :items="processCounts"
            @change="generateProcesses"
          ></v-combobox>
        </v-col>
        <v-col cols="8">
          <v-subheader>Velocidad de ciclo CPU</v-subheader>
          <v-slider
            v-model="speed"
            class="align-center"
            :max="10000"
            :min="0"
            hide-details
            @change="generateProcesses"
          >

          </v-slider>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" md="3">
          <AuxMemory :processes="processes" title="Procesos en espera"/>
        </v-col>
        <v-col cols="12" md="3">
          <AuxMemory :processes="swappedProcesses" title="Almacén de respaldo"/>
        </v-col>
        <v-col cols="12" md="3">
          <AuxMemory :processes="cpuProcesses" title="CPU - Procesos en ejecución"/>
        </v-col>
        <v-col cols="12" md="3">
          <Ram :framesView="framesView"/>
        </v-col>

      </v-row>
    </v-main>
  </v-app>
</template>

<script>

import AuxMemory from './components/AuxMemory';
import Ram from './components/Ram';
export default {
  name: 'App',
  components: {
    AuxMemory, Ram
  },
  methods: {
    generateProcesses: function (){
      let n = this.processCount;
      this.processes = [];
      this.cpuProcesses = [];
      this.swappedProcesses = [];
      this.frames =  [0,0,0,0,0,0,0,0,0,0];
      clearInterval(this.intervalId);
      for (let item of this.framesView) {
        item.background = 'white';
      }
      let init = 'A';
      for (let i = 0; i < n; i++) {
        let framesCount = Math.floor(Math.random()*5) + 1;
        this.processes.push({
          name: 'Proceso '+init,
          framesCount: framesCount,
          timelife: Math.floor(Math.random()*29) + 10,
          color: '#'+(0x1000000+Math.random()*0xffffff).toString(16).substr(1,6),
          framesIndexes: []
        });
        init = String.fromCharCode(init.charCodeAt(0) + 1);
      }
      if(this.speed !== 0)
        this.intervalId = setInterval(this.nextStep, 7000 - this.speed);
    },
    writeFrames: function (process){
      let i = 0
      let framesPainted = 0;
      while (i < this.frames.length) {
        if(this.frames[i] === 0){
          process.framesIndexes.push(i);
          this.frames[i] = 1;
          this.framesView[i].background = process.color;
          framesPainted++;
        }
        i++;
        if(framesPainted === process.framesCount) break;
      }
    },
    deleteFrames: function (process){
      let indexes = process.framesIndexes;
      for (let i = 0; i < indexes.length; i++) {
        this.frames[indexes[i]] = 0;
        this.framesView[indexes[i]].background = 'white';
      }
    },
    isWritable: function (process){
      let processSize = process.framesCount;
      let avaliableFrames = 0;
      for (let i = 0; i < this.frames.length; i++) {
        if(this.frames[i] === 0) avaliableFrames++;
      }
      return processSize <= avaliableFrames;
    },
    updateRam: function (){
      for (let i = 0; i < this.frames.length; i++) {
        if(this.frames[i] === 0) this.framesView[i].background  = 'blue';
        else{
          this.framesView[0].background = 'white';
        }
      }
    },
    decrementTimeLife: function () {
      let cpuProcesses = this.cpuProcesses;
      for (let process of cpuProcesses) {
        process.timelife--;
        if(process.timelife === 0) {
          this.deleteFrames(process);
          cpuProcesses.splice(cpuProcesses.indexOf(process), 1);

        }
      }
    },
    getLighterProcess: function(processes){
      let index = 0;
      let min = processes[0];
      for (let i = 0; i < processes.length; i++) {
        let process = processes[i];
        if(process.framesCount < min.framesCount){
          //alert(process.framesCount+' < '+min.framesCount);
          index = i;
          min = process;
        }
      }
      min = processes[index];
      //alert('menor: '+JSON.stringify(min));
      return min;
    },
    nextStep: function () {
      let processes = this.processes;
      if(processes.length === 0){
        if(!(this.swappedProcesses.length === 0))
          processes = this.swappedProcesses;
      }

      if(processes.length !== 0) {
        let cpuProcesses = this.cpuProcesses;
        let process = this.getLighterProcess(processes);
        while (!this.isWritable(process)) {
          let swappedProcess = this.getLighterProcess(cpuProcesses);
          cpuProcesses.splice(cpuProcesses.indexOf(swappedProcess), 1);
          this.deleteFrames(swappedProcess);
          this.swappedProcesses.push(swappedProcess);
          swappedProcess.framesIndexes = [];
        }
        processes.splice(processes.indexOf(process), 1);
        this.writeFrames(process);
        cpuProcesses.push(process);
      }
      this.decrementTimeLife();
    }
  },
  mounted() {
    //this.generateProcesses(10);

  },
  data: () => ({
    processes: [],
    processCount: 0,
    processCounts: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20],
    speed: 0,
    cpuProcesses: [],
    swappedProcesses: [],
    frames: [0,0,0,0,0,0,0,0,0,0],
    intervalId: 0,
    framesView: [
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      },
      {

          background: 'white',
          height: '70px',
          border: '5px solid black',

      }
    ]
  }),
};
</script>
