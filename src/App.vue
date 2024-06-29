<script setup>
import { ref, reactive, onMounted, computed} from "vue"
import Alerta from './components/Alerta.vue'

const monedas = ref([
      { codigo: 'USD', texto: 'Dolar de Estados Unidos'},
      { codigo: 'MXN', texto: 'Peso Mexicano'},
      { codigo: 'EUR', texto: 'Euro'},
      { codigo: 'GBP', texto: 'Libra Esterlina'},
])

const cryptomonedas = ref([]);
const error = ref('');

const cotizar = reactive({
    moneda: '',
    criptomoneda: ''
})

const mostrarResultado = computed(() =>{
    return Object.values(cotizacion.value).length > 0;
})

const cotizacion = ref({});

console.log(cotizacion);

onMounted(()=>{
const url = "https://min-api.cryptocompare.com/data/top/totalvolfull?limit=10&tsym=USD";
fetch(url)
    .then(respuesta => respuesta.json())
    .then(({Data}) => cryptomonedas.value = Data)
})

const cotizarCripto = () => {
    // Validar que cotizar este lleno

    if(Object.values(cotizar).includes('')){
        error.value = 'Todos los campos son obligatorios'
        return


    }
    error.value = ''
    obtenerCotizacion();

}

const obtenerCotizacion = async () =>{
    
    const { moneda, criptomoneda } = cotizar
    const url = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${criptomoneda}&tsyms=${moneda}`
    
    const respuesta = await fetch(url)
    const data = await respuesta.json()

    cotizacion.value = data.DISPLAY[criptomoneda][moneda]

    console.log(data.DISPLAY[criptomoneda][moneda]);
}
</script>

<template>
    <div class="contenedor">
        <h1 class="titulo">Cotizador de <span>Criptomonedas</span></h1>
        
        <div class="contenido">
            <Alerta
                v-if="error"
            >
            {{ error }}
            </Alerta>
            <form 
                class="formulario"
                @submit.prevent="cotizarCripto"
            >
                <div class="campo">
                    <label for="moneda">Moneda:</label>
                    <select 
                        id="moneda"
                        v-model="cotizar.moneda"
                    >
                        <option value="">-- Selecciona --</option>
                        <option 
                        v-for="moneda in monedas" 
                        :key="moneda.codigo"
                        :value="moneda.codigo"
                        >{{ moneda.texto }}</option>
                    </select>
                </div>

                <div class="campo">
                    <label for="cripto">Criptomoneda:</label>
                    <select 
                        id="cripto"
                        v-model="cotizar.criptomoneda"
                    >
                        <option value="">-- Selecciona --</option>
                        <option 
                        v-for="criptomoneda in cryptomonedas" 
                        :key="criptomoneda.CoinInfo.id"
                        :value="criptomoneda.CoinInfo.Name"
                        >{{ criptomoneda.CoinInfo.FullName }}</option>
                    </select>
                </div>

                <input type="submit" value="Cotizar" />
            </form>
            
            <div 
            v-if="mostrarResultado"
            class="contenedor-resultado">
                <h2>Cotización</h2>

                <div class="resultado">
                    <img :src="'https://cryptocompare.com/'+cotizacion.IMAGEURL" alt="imagen cripto">

                    <div>
                        <p>El precio es de : <span>{{ cotizacion.PRICE }}</span></p>
                        <p>El precio mas alto del dia: <span>{{ cotizacion.HIGHDAY }}</span></p>
                        <p>El precio mas bajo del dia: <span>{{ cotizacion.LOWDAY }}</span></p>
                        <p>Variacion ultimas 24 horas: <span>{{ cotizacion.CHANGEPCT24HOUR }} %</span></p>
                        <p>Ultima Actualización: <span>{{ cotizacion.LASTUPDATE }}</span></p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>

</style>
