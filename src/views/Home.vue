<template>
  <div>
    <v-layout :wrap = " true ">
      <v-flex xs12>
        <v-card>
          <v-date-picker 
          v-model="fecha" 
          full-width 
          locale="es-co"
          :min="minimo"
          :max="maximo"
          @change="getDollar(fecha)"
          ></v-date-picker>
        </v-card>
        <v-card color="error" dark>
          <v-card-text class="display-1 text-center">
            $ Precio Dolar Chile : {{valor}}
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
  import axios from "axios";
  import {mapMutations} from "vuex";
  export default {
    data() {
      return {
        fecha: new Date().toISOString().substr(0, 10),
        minimo: '1984',
        maximo: new Date().toISOString().substr(0, 10),
        valor: null
      }
    },
    methods: {
      ...mapMutations(['mostrarLoading','ocultarLoading']),

      async getDollar(dia){
        let ddmmyy = dia.split('-').reverse().join('-');

        try{
          this.mostrarLoading({titulo:'Accediendo a informacion', color:'secondary'})
          let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)

          if (datos.data.serie.length > 0){
             this.valor = await datos.data.serie[0].valor;
          }else {
             this.valor = 'Sin resultado'
          }
        }catch (e){
          console.log(e)
        }finally{
          this.ocultarLoading()
        }

      }
    },
    created(){
      this.getDollar(this.fecha)
    }
  }
</script>
