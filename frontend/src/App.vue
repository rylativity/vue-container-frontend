<template>
  <v-app>
    <v-main>
      <v-container>
        <h1 class="display-2 mb-4">Docker Compose Profile</h1>
        <v-row>
          <v-col v-for="service in services" :key="service.serviceName" cols="12" sm="6" md="4" lg="3">
            <service-card :service-name="service.serviceName" :port-bindings="service.portBindings" />
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import ServiceCard from "./components/ServiceCard.vue";
import yaml from "js-yaml";
import { createApp } from "vue";
import Vuetify from "vuetify";
import "vuetify/dist/vuetify.min.css";

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

createApp().use(Vuetify).mount("#app");
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Roboto:100,300,400,500,700,900");
</style>
