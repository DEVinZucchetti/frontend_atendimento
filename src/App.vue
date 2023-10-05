<template>
  <div class="main">
    <div class="container">
      <h1>MÁQUINA DO TICKET</h1>
      <br />
      <form @submit.prevent="criarTicket">
        <input v-model="nome" /> <br />
        <input v-model="cpf" /> <br />
        <select v-model="tipo">
          <option value="1">Normal</option>
          <option value="2">Preferencial</option>
        </select>
        <br />
        <button type="submit">CRIAR TICKET</button>
      </form>
    </div>

    <div class="sections">
      <div class="tv">
        <span>{{ tela1 }}</span>
        <button @click="() => chamarProximo(1)">Próximo</button>
      </div>
      <div class="tv">
        <span>{{ tela2 }}</span>
        <button @click="() => chamarProximo(2)">Próximo</button>
      </div>
      <div class="tv">
        <span>{{ tela3 }}</span>
        <button @click="() => chamarProximo(3)">Próximo</button>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios'

export default {
  data() {
    return {
      nome: '',
      cpf: '',
      tipo: '',
      tela1: '',
      tela2: '',
      tela3: ''
    }
  },
  mounted() {
    axios
      .get('http://localhost/api_atendimento/src/getInfoTv.php')
      .then((response) => {
        this.tela1 = response.data.tv1 ? response.data.tv1.name : ''
        this.tela2 = response.data.tv2 ? response.data.tv2.name : ''
        this.tela3 = response.data.tv3 ? response.data.tv3.name : ''
      })
      .catch(() => alert('SISTEMA FORA DO AR'))
  },
  methods: {
    criarTicket() {
      axios
        .post('http://localhost/api_atendimento/src/createTicket.php', {
          name: this.nome,
          cpf: this.cpf,
          type: this.tipo
        })
        .then(() => {
          alert('CRIADO COM SUCESSO')
        })
        .catch(() => alert('SISTEMA FORA DO AR'))
    },
    chamarProximo(numeroDoGuiche) {
      axios
        .post('http://localhost/api_atendimento/src/createService.php', {
          guiche: numeroDoGuiche
        })
        .then((response) => {
          if (response.status === 204) {
            alert('FILA VAZIA')
            return
          }

          if (numeroDoGuiche === 1) {
            this.tela1 = response.data.current.name + '-' + response.data.current.cpf
          } else if (numeroDoGuiche === 2) {
            this.tela2 = response.data.current.name + '-' + response.data.current.cpf
          } else if (numeroDoGuiche === 3) {
            this.tela3 = response.data.current.name + '-' + response.data.current.cpf
          }
        })
        .catch(() => alert('SISTEMA FORA DO AR'))
    }
  }
}
</script>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.container {
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  width: 500px;
  border: 1px solid #ccc;
  align-items: center;
  padding: 10px;
}

.container input,
select {
  width: 100%;
  height: 30px;
  font-size: 18px;
  margin: 10px 0;
}

.container button {
  text-align: center;
}

.sections {
  width: 900px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
}

.tv {
  width: 400px;
  height: 100px;
  border: 10px solid #000;
  border-radius: 20px;
  padding: 10px;

  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

.tv span {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 20px;
}
</style>
