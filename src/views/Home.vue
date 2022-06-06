<template>
  <div>
    <Banner />
    <div class="main-container">
      <h1>Monte o seu Pedido:</h1>
      <ProdutoForm />
    </div>
  </div>
</template>

<script>
import Banner from '../components/Banner'
import ProdutoForm from '../components/ProdutoForm'

export default {
  name: 'Home',
  components: {
    Banner,
    ProdutoForm
  }
}
</script>