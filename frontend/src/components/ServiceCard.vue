<template>
    <div class="service-card" @click="navigateToService">
      <h3>{{ serviceName }}</h3>
      <ul>
        <li v-for="port in portBindings" :key="port">
          <strong>Port:</strong> {{ port }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      serviceName: {
        type: String,
        required: true
      },
      portBindings: {
        type: Array,
        required: true
      }
    },
    methods: {
      navigateToService() {
        const port = this.portBindings[0].split(":")[0]; // Extract the first port in the format "hostPort:containerPort"
        window.location.href = `http://localhost:${port}`;
      }
    }
  };
  </script>
  
  <style scoped>
  .service-card {
    border: 1px solid #ccc;
    padding: 10px;
    margin-bottom: 10px;
    cursor: pointer;
  }
  </style>
  