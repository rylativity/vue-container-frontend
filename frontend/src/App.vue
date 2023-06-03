<template>
  <v-app>

    <v-app-bar app color="primary" dark>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title class="ml-4">Docker Services</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container fluid>
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
import "vuetify/dist/vuetify.min.css";

export default {
  components: {
    ServiceCard
  },
  data() {
    return {
      drawer: true,
      miniVariant: false,
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
    },
    navigateToService(service) {
      const firstPort = service.portBindings[0];
      const portNumber = firstPort.split(":")[0];
      const url = `http://localhost:${portNumber}`;
      window.open(url, "_blank");
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Roboto:100,300,400,500,700,900");
</style>
