<template>
  <div class="e-nuxt-container">
    <div class="e-nuxt-content">
      <h1>Hello World!</h1>
      <button v-on:click="openFile()">OpenFile</button>
      <label>
        <textarea>
          {{ code }}
        </textarea>
      </label>
    </div>
  </div>
</template>

<script>
import {remote} from "electron";

/**
 * 分割代入と呼ばれるjavascript構文です。
 *
 * const dialog = remote.dialog
 * const BrowserWindow = remote.BrowserWindow
 * とやっていることは一緒です。
 */
const {dialog, BrowserWindow} = remote;

export default {
  data() {
    return {
      code: "",
    }
  },
  methods: {

    /**
     *
     * @returns {Promise<void>}
     *
     * 非同期のものはfunction名の前にasyncを修飾し、
     * さらに、非同期となる処理の所の前にはawaitを修飾します。
     *
     * 今回の場合ですと、ダイアログを開く処理が非同期なのでdialogの前にawaitを修飾しています。
     *
     */
    async openFile() {
      // 現在フォーカスされているウィンドウを取得する。
      // https://www.electronjs.org/docs/api/browser-window
      const win = BrowserWindow.getFocusedWindow();
      // ダイアログを開く処理
      // https://www.electronjs.org/docs/api/dialog
      const fileNames = await dialog.showOpenDialog(win, {
        properties: ["openFile"],
        filters: [
          {
            name: "Document",
            // 選択可能な拡張子を選ぶ
            extensions: ["java"],
          },
        ],
      });

      console.log(fileNames);
      // Pathを取り出す
      console.log(fileNames.filePaths[0]);
    },
  }
}
</script>
