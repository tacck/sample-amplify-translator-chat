<template>
  <v-container>
    <v-row>
      <v-col class="pb-0" cols="12" sm="8">
        <v-text-field
          v-model="userName"
          label="User Name"
          outlined
        ></v-text-field
      ></v-col>
      <v-col class="pb-0" cols="6" sm="2">
        <v-select
          :items="languages"
          label="Original Language"
          outlined
          v-model="fromLang"
        ></v-select>
      </v-col>
      <v-col class="pb-0" cols="6" sm="2">
        <v-select
          :items="languages"
          label="Translate to"
          outlined
          v-model="toLang"
        ></v-select>
      </v-col>
      <v-col class="pt-0" cols="12">
        <v-text-field
          v-model="message"
          label="Message"
          outlined
          append-outer-icon="mdi-send"
          @click:append-outer="sendMessage"
        ></v-text-field></v-col
    ></v-row>
    <Timeline></Timeline>
  </v-container>
</template>

<script>
import { Predictions } from 'aws-amplify'
import Timeline from '../components/Timeline'

export default {
  components: { Timeline },
  data: function () {
    return {
      userName: '',
      message: '',
      fromLang: '',
      toLang: '',
      languages: ['en', 'ja'],
    }
  },
  methods: {
    sendMessage: function () {
      console.log('sendMessage', this.message)
      Predictions.convert({
        translateText: {
          source: {
            text: this.message,
            language: 'ja',
            // language : "es" // defaults configured on aws-exports.js
            // supported languages https://docs.aws.amazon.com/translate/latest/dg/how-it-works.html#how-it-works-language-codes
          },
          // targetLanguage: "en"
          targetLanguage: 'en',
        },
      })
        .then((result) => console.log({ result }))
        .catch((err) => console.log({ err }))
    },
  },
}
</script>

<style></style>
