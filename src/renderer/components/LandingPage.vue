<template>
  <div id="wrapper">
    <VueFullScreenFileDrop @drop="onDrop" />
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
import "vue-full-screen-file-drop/dist/vue-full-screen-file-drop.css";

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
      const { info } = arg;
      console.log(info);
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

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  padding: 15px;
}
</style>
