<template>
  <v-card>
    <v-card-title class="headline">
      {{$t('settings.webhooks.meal-planner-webhooks')}}
    </v-card-title>
    <v-card-text>
      <p v-html="$t('settings.webhooks.the-urls-listed-below-will-recieve-webhooks-containing-the-recipe-data-for-the-meal-plan-on-its-scheduled-day-currently-webhooks-will-execute-at', {time: time})"></p>

      <v-row dense align="center">
        <v-col cols="12" md="2" sm="5">
          <v-switch
            v-model="enabled"
            inset
            :label="$t('general.enabled')"
            class="my-n3"
          ></v-switch>
        </v-col>
        <v-col cols="12" md="3" sm="5">
          <TimePickerDialog @save-time="saveTime" />
        </v-col>
        <v-col cols="12" md="4" sm="5">
          <v-btn text color="info" @click="testWebhooks"> {{$t('settings.webhooks.test-webhooks')}} </v-btn>
        </v-col>
      </v-row>

      <v-row v-for="(url, index) in webhooks" :key="index" align="center" dense>
        <v-col cols="1">
          <v-btn icon color="error" @click="removeWebhook(index)">
            <v-icon>mdi-minus</v-icon>
          </v-btn>
        </v-col>
        <v-col>
          <v-text-field
            v-model="webhooks[index]"
            :label="$t('settings.webhooks.webhook-url')"
          ></v-text-field>
        </v-col>
      </v-row>
    </v-card-text>
    <v-card-actions>
      <v-row>
        <v-col>
          <v-btn icon color="success" @click="addWebhook">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-col>
        <v-col> </v-col>
        <v-col align="end">
          <v-btn text color="success" @click="saveWebhooks">
            {{$t('settings.webhooks.save-webhooks')}}
          </v-btn>
        </v-col>
      </v-row>
    </v-card-actions>
  </v-card>
</template>

<script>
import api from "../../../api";
import TimePickerDialog from "./TimePickerDialog";
export default {
  components: {
    TimePickerDialog,
  },
  data() {
    return {
      name: "main",
      webhooks: [],
      enabled: false,
      time: "",
    };
  },
  mounted() {
    this.getSiteSettings();
  },
  methods: {
    saveTime(value) {
      this.time = value;
    },
    async getSiteSettings() {
      let settings = await api.settings.requestAll();
      this.webhooks = settings.webhooks.webhookURLs;
      this.name = settings.name;
      this.time = settings.webhooks.webhookTime;
      this.enabled = settings.webhooks.enabled;
    },
    addWebhook() {
      this.webhooks.push(" ");
    },
    removeWebhook(index) {
      this.webhooks.splice(index, 1);
    },
    saveWebhooks() {
      const body = {
        name: this.name,
        webhooks: {
          webhookURLs: this.webhooks,
          webhookTime: this.time,
          enabled: this.enabled,
        },
      };
      api.settings.update(body);
    },
    testWebhooks() {
      api.settings.testWebhooks();
    },
  },
};
</script>

<style>
</style>