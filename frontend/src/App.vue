<template>
  <div class="app">
    <h1>Available Docker Services</h1>
    <div v-for="service in services" :key="service.serviceName">
      <service-card :service-name="service.serviceName" :port-bindings="service.portBindings" />
    </div>
  </div>
</template>

<script>
import ServiceCard from "./components/ServiceCard.vue";
import yaml from "js-yaml";

export default {
  components: {
    ServiceCard
  },
  data() {
    return {
      services: []
    };
  },
  mounted() {
    // Call the API to retrieve the docker-compose.yml file
    // Replace this with your own logic to fetch the file
    // Here, we'll use a hardcoded example for demonstration purposes
    const dockerComposeFile = `
      version: "3"
      services:
        web:
          image: nginx:latest
          ports:
            - "80:80"
        db:
          image: mysql:latest
          ports:
            - "3306:3306"
    `;

    this.parseDockerComposeFile(dockerComposeFile);
  },
  methods: {
    parseDockerComposeFile(fileContent) {
      const parsedData = yaml.load(fileContent);
      const services = [];

      if (parsedData && parsedData.services) {
        for (const serviceName in parsedData.services) {
          if (Object.prototype.hasOwnProperty.call(parsedData.services, serviceName)) {
            const service = parsedData.services[serviceName];
            if (service.ports) {
              const portBindings = service.ports.map((port) => port.toString());
              services.push({
                serviceName,
                portBindings
              });
            }
          }
        }
      }

      this.services = services;
    }
  }
};
</script>
