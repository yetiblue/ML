<template>
  <div>
    <script
      type="application/javascript"
      defer
      src="https://cdn.jsdelivr.net/npm/@runwayml/hosted-models@latest/dist/hosted-models.js"
    ></script>
    <div>
      <form @submit.prevent="sendImage">
        Upload Image Here
        <input type="file" @change="onFileChange" />
        <button type="submit">Upscale!</button>
      </form>
    </div>
    <button @click="getImage">Get Image Back!</button>
    <img class="img-fluid" :src="upscaledImage" alt />
  </div>
</template>

<script>
export default {
  methods: {
    onFileChange(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) {
        return;
      }
      this.form.inputs = files[0];
      this.createImage(files[0]);
    },
    createImage(file) {
      let reader = new FileReader();
      let vm = this;
      reader.onload = e => {
        vm.preview = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    getImage($axios) {
      const headers = {
        headers: {
          Accept: "application/json",
          Authorization: "Bearer qF1RMjR5k29vhWh8vKZ8Ng==",
          "Content-Type": "application/json"
        }
      };
      try {
        let upscaledImage = this.$axios.get(
          "https://srfbn-f3757352.hosted-models.runwayml.cloud/v1/info",
          headers
        );
        return { upscaledImage };
      } catch (error) {
        console.log(error);
      }
    },
    sendImage($axios) {
      // const inputs = {
      //     "input-image": <base 64 image>
      // };
      const headers = {
        headers: {
          Accept: "application/json",
          Authorization: "Bearer qF1RMjR5k29vhWh8vKZ8Ng==",
          "Content-Type": "application/json"
        }
      };
      let editedForm = this.form;
      if (editedForm.inputs.toString().indexOf("http://") != -1) {
        delete editedForm["headshot"];
      }

      let formData = new FormData();
      for (let data in editedForm) {
        formData.append(data, editedForm[data]);
      }
      try {
        this.$axios
          .post(
            "https://srfbn-f3757352.hosted-models.runwayml.cloud/v1/query",
            formData,
            headers
          )
          .then(response => response.json())
          .then(outputs => {
            const { output_image } = outputs;
            // use the outputs in your project
          });
      } catch (error) {
        console.log(error);
      }
    }
  },

  //post the image, and then when ready -> do a get request axios and display that object.image?
  data() {
    return {
      form: {
        inputs: null
      }
    };
  }
};
</script>

 
