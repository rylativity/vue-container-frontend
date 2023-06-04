<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title class="ml-4">Docker Services</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn @click="startAutoCycle" v-if="services.length > 1">{{ autoCycle ? 'Stop' : 'Start' }} Auto Cycle</v-btn>
    </v-app-bar>

    <v-main>
      <v-container fluid>
        <v-row>
          <v-col v-for="(service, index) in services" :key="service.serviceName" cols="12" sm="6" md="4" lg="3">
            <service-card
              :service-name="service.serviceName"
              :port-bindings="service.portBindings"
              @navigate="navigateToService"
              :class="{ 'service-card': true, 'active-card': index === activeServiceIndex }"
            />
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <v-divider></v-divider>

    <v-card class="preview-card">
      <v-card-title>Preview</v-card-title>
      <v-card-text>
        <div class="iframe-container">
          <iframe :src="iframeURL" width="100%" height="100%"></iframe>
        </div>
      </v-card-text>
    </v-card>
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
      iframeURL: "",
      activeServiceIndex: 0,
      autoCycle: false,
      cycleTimer: null
    };
  },
  mounted() {
    // Fetch the docker-compose.yml file
    const filePath = "/static/docker-compose.yml";

    fetch(filePath)
      .then((response) => response.text())
      .then((data) => {
        this.parseDockerComposeFile(data);

        if (this.services.length > 0) {
          this.navigateToService(this.services[0].portBindings[0]);
        }
      })
      .catch((error) => console.error(error));
    
  },
  beforeUnmount() {
    this.stopAutoCycle();
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
    },
    startAutoCycle() {
      if (!this.autoCycle) {
        this.autoCycle = true;
        this.cycleTimer = setInterval(this.switchToNextService, 10000);
      } else {
        this.stopAutoCycle();
      }
    },
    stopAutoCycle() {
      if (this.autoCycle) {
        this.autoCycle = false;
        clearInterval(this.cycleTimer);
      }
    },
    switchToNextService() {
      this.activeServiceIndex = (this.activeServiceIndex + 1) % this.services.length;
      const portBinding = this.services[this.activeServiceIndex].portBindings[0];
      this.navigateToService(portBinding);
    }
  }
};
</script>

<style>

.active-card {
  animation: pulse 2s infinite;
}


.iframe-container {
  position: relative;
  padding-bottom: 56.25%; /* 16:9 aspect ratio, adjust as needed */
  height: 0;
  overflow: hidden;
}

iframe {
  position: absolute;
  width: 100%;
  height: 100%;
  border: none;
}

@keyframes pulse {
  0% {
    box-shadow: 0 0 0 0 rgba(0, 123, 255, 0.7);
  }
  70% {
    box-shadow: 0 0 0 10px rgba(0, 123, 255, 0);
  }
  100% {
    box-shadow: 0 0 0 0 rgba(0, 123, 255, 0);
  }
}
</style>
