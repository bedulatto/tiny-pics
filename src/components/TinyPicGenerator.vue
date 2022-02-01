<template>
  <v-container>
    <v-container>
      <v-card>
        <v-container>
          <v-row justify="center">
            <v-col cols="12">
              <v-file-input
                type="file"
                name="myImage"
                accept="image/png, image/gif, image/jpeg"
                chips
                multiple
                v-model="files"
                @change="onChangeListener"
              ></v-file-input>
            </v-col>
            <v-col cols="2">
              <v-btn
                text
                block
                color="primary"
                :disabled="images.length == 0"
                @click="downloadAll"
              >
                All <v-icon>mdi-download</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-container>
      </v-card>
    </v-container>
    <v-container v-if="images.length > 0">
      <v-card>
        <v-container>
          <v-row>
            <v-col v-for="image in images" :key="image.src" cols="2">
              <v-card light>
                <v-img fluid :src="image.src" />
                <v-card-text class="py-1">
                  {{ image.name }}
                </v-card-text>
                <v-card-actions>
                  <v-btn color="primary" text block @click="removeSrc(image)">
                    <v-icon>mdi-download</v-icon>
                  </v-btn>
                </v-card-actions>
                <v-btn
                  color="error"
                  fab
                  absolute
                  left
                  top
                  x-small
                  @click="removeSrc(image)"
                >
                  <v-icon>mdi-close</v-icon>
                </v-btn>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-card>
    </v-container>
  </v-container>
</template>
<script>
export default {
  data: () => ({
    files: [],
    images: [],
  }),
  methods: {
    onChangeListener(files) {
      var vm = this;
      files.forEach((file) => {
        var reader = new FileReader();

        reader.onload = function (e) {
          var img = document.createElement("img");
          img.onload = function () {
            var MAX_WIDTH = 10;
            var MAX_HEIGHT = 10;

            var width = img.width;
            var height = img.height;

            // Change the resizing logic
            if (width > height) {
              if (width > MAX_WIDTH) {
                height = height * (MAX_WIDTH / width);
                width = MAX_WIDTH;
              }
            } else {
              if (height > MAX_HEIGHT) {
                width = width * (MAX_HEIGHT / height);
                height = MAX_HEIGHT;
              }
            }

            var canvas = document.createElement("canvas");
            canvas.width = width;
            canvas.height = height;
            var ctx = canvas.getContext("2d");
            ctx.drawImage(img, 0, 0, width, height);

            // Show resized image in preview element
            var dataurl = canvas.toDataURL(file.type);

            vm.images.push({ src: dataurl, name: file.name });
          };
          img.src = e.target.result;
        };
        reader.readAsDataURL(file);
      });
    },
    removeSrc(image) {
      this.images.splice(this.images.indexOf(image), 1);
    },
    downloadAll() {
      if (this.images.length == 0) return;
      this.images.forEach((image) => {
        let link = document.createElement("a");
        link.href = image.src;
        link.download = "lazy_" + image.name;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
      });
    },
  },
};
</script>