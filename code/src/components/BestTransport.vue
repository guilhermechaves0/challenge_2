<template>
  <div class="title">
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-navbar-brand class="ml-2">
        <b>{{ appName }}</b>
      </b-navbar-brand>
    </b-navbar>
    <div class="container">
      <div class="row">
      <div class="form-group">
        <label for="nome_envio">Envio:</label>
        <select id="envio" nome="cidade" v-model="cidade" style="width: 100%">
          <option>São Paulo</option>
          <option>Belo Horizontr</option>
          <option>Curitiba</option>
          <option>Fortaleza</option>
          <option>Campinas</option>
          <option>Natal</option>
          <option>Manaus</option>
          <option>Salvador</option>
          <option>Rio de Janeiro</option>
          <option>Recife</option>
        </select>
      </div>

      <div class="form-group">
        <label for="nome_peso">Peso:</label>
        <input type="text" id="peso" nome="peso" v-model="peso" style="width: 100%" />
      </div>
      
      <button class="btn btn-success" type="button" @click="calculo" style="margin-top:1rem">Analisar</button>
    </div>
    <div>
      <p>Estas são as melhores alternativas de frete que encontramos para você.</p>
      <div>
        Frete mais barato: {{distribuidora_barata.name}} - R$ {{distribuidora_barata.value}} - {{distribuidora_barata.lead_time}}
      </div>
      <div>
        Frete mais rapido: {{distribuidora_rapida.name}} - R$ {{distribuidora_rapida.value}} - {{distribuidora_rapida.lead_time}}
      </div>
    </div>
    </div>
  </div>
</template>

<script>
import { BNavbar, BNavbarBrand } from "bootstrap-vue";

export default {
  components: {
    BNavbar,
    BNavbarBrand,
  },
  data() {
    return {
      appName: "Melhor Frete",
      cidade: "",
      peso: "",
      dados: [],
      distribuidora_barata: {},
      distribuidora_rapida: {},
    };
  },
  created() {
    // Implemente aqui o GET dos dados da API REST
    // para que isso ocorra na inicialização da pagina
    this.buscarDados();
  },
  methods: {
    // Implemente aqui os metodos utilizados na pagina
    buscarDados() {
      this.$axios
        .get("http://localhost:3000/transport")
        .then((response) => {
          this.dados = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    calculo() {
      //Funcao que calcula o frete
      const distribuidora = this.dados.filter((item) => (
        item.city === this.cidade
      ));

      let valor_menor = 999999;
      let mais_rapido = 999999;

      distribuidora.forEach((item) => {
        if (this.peso > 100) {
          const valor = this.peso * Number(item.cost_transport_heavy.replace("R$ ", ''));
          const tempo = Number(item.lead_time.replace("h", ''));

          if (valor < valor_menor) {
            valor_menor = valor;
            this.distribuidora_barata = {...item, value: valor.toFixed(2)};
          }

          if (tempo < mais_rapido) {
            mais_rapido = tempo;
            this.distribuidora_rapida = {...item, value: valor.toFixed(2)};
          }
        } 
        else {
          const valor = this.peso * Number(item.cost_transport_light.replace("R$ ", ''));

          if (valor < valor_menor) {
            valor_menor = valor;
            this.distribuidora_barata = {...item, value: valor.toFixed(2)};
          }

          if (item.lead_time < mais_rapido) {
            mais_rapido = item.lead_time;
            this.distribuidora_rapida = {...item, value: valor.toFixed(2)};
          }
        }

      })
    },
  },
};
</script>

<style scoped>
.title .navbar {
  background-color: #00aca6 !important;
}

.title .navbar-brand {
  margin-left: 20px;
}
</style>