<template>
  <v-card>
    <v-card-title class="justify-md-center">{{title}}</v-card-title>
    <v-divider/>
    <v-row>
      <v-col cols="12">
        <v-card
          v-for="(process, index) in processes"
          :key="index"
          :color="process.color"

        >
          <v-card-title class="justify-md-center">{{ process.name }}</v-card-title>
          <v-card-title class="justify-md-center">{{'Marcos: '+process.framesCount+'\nTiempo de vida: '+process.timelife}}</v-card-title>
        </v-card>
      </v-col>
    </v-row>
  </v-card>
</template>
<script>
export default {
  name: 'AuxMemory',
  props: {
    title: null,
    processes: null
  }
}
</script>