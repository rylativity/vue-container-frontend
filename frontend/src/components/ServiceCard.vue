<template>
  <v-card @click="handleCardClick">
    <v-card-title class="service-card-title">{{ serviceName }}</v-card-title>
    <v-card-text>
      <v-list>
        <v-list-item v-for="binding in portBindings" :key="binding" @click="handlePortClick(binding)">
          <v-list-item-title class="clickable">{{ binding }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-card-text>
  </v-card>
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
    handleCardClick() {
      this.$emit("navigate", this.portBindings[0]);
    },
    handlePortClick(portBinding) {
      this.$emit("navigate", portBinding);
    }
  }
};
</script>

<style>
.service-card-title {
  background-color: #007bff; /* Replace with your preferred color */
  color: #fff;
  padding: 10px;
  font-weight: bold;
}
.clickable {
  cursor: pointer;
}
</style>
