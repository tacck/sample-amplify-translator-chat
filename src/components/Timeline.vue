<template>
  <v-row>
    <v-col class="pb-0" cols="12" v-for="message in messages" :key="message.id">
      <v-card class="ma-4">
        <v-row>
          <v-col cols="12" class="px-5 text-h5 font-weight-light">
            {{ message.userName }}</v-col
          >
        </v-row>
        <v-row>
          <v-col class="py-0" cols="6">
            <v-card outlined color="grey lighten-3">
              <v-card-subtitle class="caption">Original</v-card-subtitle>
              <v-card-text class="body-1">
                {{ message.originalMessage }}</v-card-text
              >
            </v-card></v-col
          >
          <v-col class="px-8" cols="6">{{ message.translatedMessage }}</v-col>
        </v-row>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    messages: {
      type: Array,
    },
  },
}
</script>

<style></style>
