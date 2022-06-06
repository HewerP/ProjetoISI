<template>
  <div id="pedidos-table" v-if="pedidos">
    <div>
      <div id="pedidos-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Endereço:</div>
        <div>Pagamento:</div>
        <div>Produtos:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="pedidos-table-rows">
      <div class="pedidos-table-row" v-for="pedido in pedidos" :key="pedido.id">
        <div class="order-number">{{ pedido.id }}</div>
        <div>{{ pedido.nome }}</div>
        <div>{{ pedido.endereco }}</div>
        <div>{{ pedido.pagamento }}</div>
        <div>
          <ul v-for="(produto, index) in pedido.produtos" :key="index">
            <li>{{ produto }}</li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updatePedidos($event, pedido.id)">
            <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="pedido.status == s.tipo">
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deletePedidos(pedido.id)">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <h2>Não há pedidos no momento!</h2>
  </div>
</template>
<script>
  export default {
    name: "Dashboard",
    data() {
      return {
        pedidos: null,
        pedido_id: null,
        status: []
      }
    },
    methods: {
      async getPedidos() {
        const req = await fetch('http://localhost:3000/pedidos')

        const data = await req.json()

        this.pedidos = data

        // Resgata os status de pedidos
        this.getStatus()

      },
      async getStatus() {

        const req = await fetch('http://localhost:3000/status')

        const data = await req.json()

        this.status = data

      },
      async deletePedidos(id) {

        const req = await fetch(`http://localhost:3000/pedidos/${id}`, {
          method: "DELETE"
        });

        const res = await req.json()

        this.getPedidos()

      },
      async updatePedidos(event, id) {

        const option = event.target.value;

        const dataJson = JSON.stringify({status: option});

        const req = await fetch(`http://localhost:3000/pedidos/${id}`, {
          method: "PATCH",
          headers: { "Content-Type" : "application/json" },
          body: dataJson
        });

        const res = await req.json()

        console.log(res)

      }
    },
    mounted () {
    this.getPedidos()
    }
  }
</script>

<style scoped>
  #pedidos-table {
    max-width: 1200px;
    margin: 0 auto;
  }

  #pedidos-table-heading,
  #pedidos-table-rows,
  .pedidos-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #pedidos-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  .pedidos-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }

  #pedidos-table-heading div,
  .pedidos-table-row div {
    width: 19%;
  }

  #pedidos-table-heading .order-id,
  .pedidos-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn {
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
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>