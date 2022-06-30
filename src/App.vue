<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="image/recorder.png"
          transition="scale-transition"
          width="40"
        />
      </div>
      <v-toolbar-title class="caption"> Voice recorder </v-toolbar-title>
    </v-app-bar>
    <v-main>
      <div class="container">
        <audio-recorder
          upload-url="http://127.0.0.1:8000/upload/"
          :attempts="1"
          :time="2"
          :before-recording="callback"
          :pause-recording="callback"
          :after-recording="select"
          :select-record="select"
          :before-upload="callback"
          :successful-upload="callback"
          :failed-upload="callback"
        />
        <div class="select-container">
          <v-select
            :items="labels"
            v-model="selected_label"
            label="Câu lệnh"
            item-text="disp"
            item-value="val"
            required
          ></v-select>
          <v-btn
            :disabled="!currentFile"
            @click="upload"
            depressed
            color="primary"
            >Gửi</v-btn
          >
        </div>
      </div>
      <v-snackbar v-model="error">
        Xảy ra lỗi vui lòng thử lại!

        <template v-slot:action="{ attrs }">
          <v-btn color="red" text v-bind="attrs" @click="error = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
      <v-snackbar v-model="success">
        Upload thành công!

        <template v-slot:action="{ attrs }">
          <v-btn color="blue" text v-bind="attrs" @click="success = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-main>

    <v-footer padless>
      <v-col class="text-center" cols="12">
        Make by — <a href="https://github.com/ngovandong">ngovandong</a>
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    error: false,
    success: false,
    labels: [
      { disp: "Bật đèn", val: "BD" },
      { disp: "Tắt đèn", val: "TD" },
      { disp: "Bật quạt", val: "BQ" },
      { disp: "Tắt quạt", val: "TQ" },
    ],
    selected_label: "BD",
    currentFile: null,
  }),
  methods: {
    callback(data) {
      console.log(data);
    },
    upload() {
      if (this.currentFile) {
        fetch(
          "https://afd8-116-105-214-161.ap.ngrok.io/upload/" +
            this.selected_label,
          {
            method: "PUT",
            body: this.currentFile,
          }
        )
          .then(() => (this.success = true))
          .catch(() => (this.error = true));
      }
    },
    select(data) {
      this.currentFile = new File([data.blob], "filename.wav");
    },
  },
};
</script>

<style scoped>
.container {
  margin: 4rem auto;
  max-width: 800px;
  display: flex;
  justify-content: center;
}
.select-container {
  margin: 2rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
</style>
