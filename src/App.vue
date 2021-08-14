<template>
  <div id="app">
    <div>
      <nav class="nav">
        <div class="header">
          <h3 class="h3">Geovation Gather hub</h3>
        </div>
      </nav>
    </div>
    <index/>
  
  </div>
</template>

<script>
import index from "./components/Index.vue";
export default {
  name: "App",
  components: {
    index
  },
};


</script>

