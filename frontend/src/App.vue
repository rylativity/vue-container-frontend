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
            <service-card
              :service-name="service.serviceName"
              :port-bindings="service.portBindings"
              @navigate="navigateToService"
            />
          </v-col>
        </v-row>
      </v-container>
    

    <v-divider></v-divider>

    <v-card>
      <v-card-title>Preview</v-card-title>
      <v-card-text>
        <iframe :src="iframeURL" width="100%" height="400"></iframe>
      </v-card-text>
    </v-card>
  </v-main>
  </v-app>
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
      drawer: true,
      miniVariant: false,
      services: [],
      iframeURL: ""
    };
  },
  mounted() {
    // Fetch the docker-compose.yml file
    const filePath = "/static/docker-compose.yml";

    fetch(filePath)
      .then((response) => response.text())
      .then((data) => this.parseDockerComposeFile(data))
      .catch((error) => console.error(error));
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
    navigateToService(portBinding) {
      const portNumber = portBinding.split(":")[0];
      this.iframeURL = `http://localhost:${portNumber}`;
    }
  }
};
</script>

<style>
/* Your styles here */
</style>
