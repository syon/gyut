<template>
  <div id="wrapper">
    <VueFullScreenFileDrop @drop="onDrop">
      <div class="custom-content"></div>
    </VueFullScreenFileDrop>
    <main>
      <div class="doc">
        <table>
          <tbody>
            <tr>
              <th>width</th>
              <td><input v-model="order.width" type="number" min="1" /></td>
            </tr>
            <tr>
              <th>height</th>
              <td><input v-model="order.height" type="number" min="1" /></td>
            </tr>
          </tbody>
        </table>
      </div>
      <div>
        <p>ここにポイッとしてね</p>
      </div>
    </main>
  </div>
</template>

<script>
import { ipcRenderer } from "electron";
import VueFullScreenFileDrop from "vue-full-screen-file-drop";

export default {
  name: "LandingPage",
  components: { VueFullScreenFileDrop },
  data() {
    return {
      order: {
        width: 200,
        height: null
      }
    };
  },
  mounted() {
    ipcRenderer.on("gyut-sharp-reply", (event, arg) => {
      // const { info } = arg;
      // console.log(info);
    });
  },
  methods: {
    open(link) {
      this.$electron.shell.openExternal(link);
    },
    onDrop(formData, files) {
      Object.keys(files).forEach(x => {
        const file = files[x];
        const obj = {
          name: file.name,
          path: file.path,
          size: file.size,
          type: file.type,
          lastModified: file.lastModified
        };
        const params = {
          width: Number(this.order.width) || null,
          height: Number(this.order.height) || null
        };
        ipcRenderer.send("gyut-sharp-order", { file: obj, params });
      });
    }
  }
};
</script>

<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  padding: 15px;
}

.custom-content {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.1);
  font-size: 1em;
}
.custom-content:before {
  border: 2px dashed #fff;
  position: absolute;
  content: "";
  top: 10px;
  bottom: 10px;
  left: 10px;
  right: 10px;
}
</style>
