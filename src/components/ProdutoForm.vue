<template>
  <Message :msg="msg" v-show="msg" />
  <div>
    <form id="produto-form" method="POST" @submit="createProduto">
      <div class="input-container">
        <label for="nome">Nome do cliente:</label>
        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
      </div>
      <div class="input-container">
        <label for="nome">Endereço:</label>
        <input type="text" id="endereco" name="endereco" v-model="endereco" placeholder="Digite o seu endereço">
      </div>
      <div class="input-container">
        <label for="pagamento">Escolha a forma de pagamento:</label>
        <select name="pagemento" id="pagamento" v-model="pagamento">
          <option value="">Selecione o tipo de pagamento</option>
          <option v-for="pagamento in pagamentos" :key="pagamento.id" :value="pagamento.tipo">{{ pagamento.tipo }}</option>
        </select>
      </div>
      <div id="produtos-container" class="input-container">
        <label id="produtos-title" for="podutos">Selecione os Produtos que você deseja:</label>
        <div class="checkbox-container" v-for="produto in produtosdata" :key="produto.id">
          <input type="checkbox" name="produtos" v-model="produtos" :value="produto.tipo">
          <span>{{ produto.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input class="submit-btn" type="submit" value="Criar meu Pedido!">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message'

export default {
  name: "ProdutoForm",
  data() {
    return {
      pagamentos: null,
      produtosdata: null,
      nome: null,
      endereco: null,
      pagamento: null,
      produtos: [],
      status: "Solicitado",
      msg: null
    }
  },
  methods: {
    async getProdutos() {
      const req = await fetch('http://localhost:3000/mercado')
      const data = await req.json()

      this.pagamentos = data.pagamento
      this.produtosdata = data.produtos
    },
    async createProduto(e) {

      e.preventDefault()

      if( this.nome === "" || this.endereco === "" || this.pagamento === "" || this.produtos.length === 0 ){
        this.msg = "Preencha todos os campos!"

        // clear message
        setTimeout(() => this.msg = "", 3000)
        
      }else{
        const data = {
          nome: this.nome,
          endereco: this.endereco,
          pagamento: this.pagamento,
          produtos: Array.from(this.produtos),
          status: "Solicitado"
        }

        const dataJson = JSON.stringify(data)    

        const req = await fetch("http://localhost:3000/pedidos", {
          method: "POST",
          headers: { "Content-Type" : "application/json" },
          body: dataJson
        });

        const res = await req.json()

        console.log(res)

        this.msg = "Pedido realizado com sucesso!"

        // clear message
        setTimeout(() => this.msg = "", 3000)

        // limpar campos
        this.nome = ""
        this.endereco = ""
        this.pagamento = ""
        this.produtos = []
      }
      
    }
  },
  mounted () {
    this.getProdutos()
  },
  components: {
    Message
  }
}
</script>

<style scoped>
  #produto-form {
    max-width: 400px;
    margin: 0 auto;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;;
    padding: 5px 10px;
    border-left: 4px solid #fcba03;
  }

  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #produtos-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #produtos-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
  }

  .checkbox-container span,
  .checkbox-container input {
    width: auto;
  }

  .checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
  }

  .submit-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>