<template>
  <v-card>
    <v-card-title class="justify-md-center">Memoria RAM</v-card-title>
    <v-row

      v-for="(frame, index) in framesView"
      :key="index"
    >
      <v-col
        class="justify-center"
        cols="1"
      >
        <v-card-title class="mb-0 mt-0 pb-0 pt-0">{{index}}</v-card-title>
      </v-col>
      <v-col
        cols="11"
        class="mb-0 mt-0 pb-0 pt-0"
      >
        <div
          :style="frame"
        >
          {{frame.name}}
        </div>
      </v-col>
    </v-row>
  </v-card>
</template>
<script>
  export default{
    name: 'Ram',
    props: {
      framesView: null
    }
  }
</script>
