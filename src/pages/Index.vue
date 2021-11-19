<template>
  <q-page padding>
    <div class="row justify-center">
      <div class="col-12 col-md-8">
        <q-card>
          <q-card-section class="bg-primary text-white">
              <div class="text-h6 text-center">Calculadora</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5 text-grey-5 text-right">
              {{acumulador + actual}}
            </div>
            <div class="text-h3 text-right">
              {{resultado}}
            </div>
          </q-card-section>
          <q-card-section class="bg-grey-4">
            <div class="row q-col-gutter-sm">
              <div class="col-3"
                v-for="(btn, index) in botones" :key="index"
              >
                <q-btn
                  class="full-width text-h6"
                  :color="!esNumero(btn) ? 'indigo' : 'grey-2'"
                  :text-color="!esNumero(btn) ? 'white' : 'grey-8'"
                  @click="btnAccion(btn)"
                >
                {{btn}}
                </q-btn>
              </div>
              <div class="col-6">
                <q-btn class="full-width text-h6" color="indigo" @click="btnReset">Reset</q-btn>
              </div>
              <div class="col-6">
                <q-btn class="full-width text-h6" color="orange" @click="btnResultado">=</q-btn>
              </div>
            </div>
          </q-card-section>
        </q-card>
        <pre>{{arrayResultado}}</pre>
      </div>
    </div>
  </q-page>
</template>

<script>
import {ref} from 'vue'
import {evaluate, round} from 'mathjs'

export default {
  setup() {
    const botones = [7, 8, 9, "%", 4, 5, 6, "+", 1, 2, 3, "-", ".", 0, "/", "*"];
    const esNumero = (btn) => !isNaN(btn);
    const actual = ref('');
    const acumulador = ref('');
    const resultado = ref('');
    const operadorClick = ref(true);
    const arrayResultado = ref([]);

    const btnAccion = valor => {
      if(esNumero(valor)){
        if(operadorClick.value){
          actual.value = ''
          operadorClick.value = false
        }

        actual.value = `${actual.value}${valor}`
      }else{
        ejecutarOperacion(valor)
      }
    }

    const ejecutarOperacion = valor =>{
      if(valor==='.'){
        if(actual.value.indexOf('.')===-1){
          actual.value = `${actual.value}${valor}`
        }
        return
      }

      if(valor==='%'){
        if(actual.value!==''){
          actual.value = `${parseFloat(actual.value)/100}`
        }
        return
      }

      agregarOperador(valor)
    }

    const agregarOperador = valor =>{
      if(!operadorClick.value){  
      acumulador.value += `${actual.value} ${valor}`
      actual.value= ''
      operadorClick.value = true
      }
    }

    const btnReset = () => {
      actual.value = ''
      acumulador.value = ''
      resultado.value = ''
      operadorClick.value = true
    }

    const btnResultado = () => {
      if(!operadorClick.value){
        resultado.value = evaluate(acumulador.value + actual.value)
        arrayResultado.value.push(`${acumulador.value} ${actual.value} = ${resultado.value}`)
      }else{
        resultado.value = 'Error'
      }
      
    }

    return{
      botones,
      btnAccion,
      esNumero,
      actual,
      acumulador,
      btnReset,
      btnResultado,
      resultado,
      arrayResultado
    }
  },
}
</script>

<style scoped>
  .text-h5{
    height: 25px;
  }
  .text-h3{
    height: 50px;
  }
</style>