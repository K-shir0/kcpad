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
import dedent from "dedent";
import {readFile} from "fs";

/**
 * ## 分割代入
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
     * ファイルを開くための一連の処理をまとめたもの
     *
     * */
    async openFile() {
      // ファイル選択ダイアログを開き選択したファイルのパスを取得する処理。
      const path = await this.selectFile();

      readFile(path, (error, data) => {
        // もし読み取れなければエラー
        if (error != null) {
          alert("file open error.");
          return;
        }

        // 取得できてるかのテスト
        console.log(data.toString());

        // codeに内容を代入
        this.code = dedent`${data.toString()}`;
      });
    },

    /**
     * ファイルを選択するダイアログを開き、選択されたファイルのパスを取得する処理
     * @returns {Promise<string>} 選択されたファイルのパスです。
     *
     * ## async awaitとは
     * 非同期のものはfunction名の前にasyncを修飾し、
     * さらに、非同期となる処理の所の前にはawaitを修飾します。
     *
     * 今回の場合ですと、ダイアログを開く処理が非同期なのでdialogの前にawaitを修飾しています。
     *
     */
    async selectFile() {
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


      return fileNames.filePaths[0];
    },

  }
}
</script>
