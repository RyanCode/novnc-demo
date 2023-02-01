<!--
 * @Author: RyanWilson
 * @Date: 2022-09-12 20:15:22
-->
<template>
  <div id="terminal" ref="terminal"></div>
</template>

<script>
import { Terminal } from "xterm";
import { FitAddon } from "xterm-addon-fit";
import "xterm/css/xterm.css";
export default {
  data() {
    return {
      term: "",
      socket: "",
      path: "ws://127.0.0.1:30010",
    };
  },
  mounted() {
    this.initXterm();
    this.initSocket();
  },
  methods: {
    initSocket: function () {
      if (typeof WebSocket === "undefined") {
        alert("您的浏览器不支持socket");
      } else {
        var websocket = new WebSocket(this.path);
        websocket.onopen = () => {
          this.onOpen();
        };
        websocket.onmessage = (evt) => {
          this.onData(evt.data);
        };
        websocket.onerror = (evt) => {
          this.onError(evt);
        };
        websocket.onclose = (evt) => {
          this.onClose(evt);
        };
        websocket.onclose = () => {
          this.onClose();
        };
        this.socket = websocket;
      }
    },
    onOpen: function () {
      console.log("socket连接成功");
    },
    onError: function () {
      console.log("socket连接错误");
    },
    onData: function (msg) {
      console.log(msg)
      msg.text().then((data) => {
          this.term.write(data);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    onSend: function () {
      this.socket.send();
    },
    onClose: function () {
      console.log("socket已经关闭");
    },
    initXterm() {
      let _this = this;
      let term = new Terminal({
        rendererType: "canvas", //渲染类型
        // rows: _this.rows, //行数
        // cols: _this.cols, // 不指定行数，自动回车后光标从下一行开始
        convertEol: true, //启用时，光标将设置为下一行的开头
        disableStdin: false, //是否应禁用输入
        cursorBlink: true, //光标闪烁
        theme: {
          foreground: "#ECECEC", //字体
          background: "#000000", //背景色
          cursor: "help", //设置光标
          lineHeight: 20,
        },
      });
      // 创建terminal实例
      term.open(this.$refs["terminal"]);
      // 换行并输入起始符 $
      term.prompt = (_) => {
        term.write("\r\n\x1b[33m$\x1b[0m ");
      };
      // canvas背景全屏
      const fitAddon = new FitAddon();
      term.loadAddon(fitAddon);
      fitAddon.fit();

      window.addEventListener("resize", resizeScreen);
      function resizeScreen() {
        try {
          fitAddon.fit();
        } catch (e) {
          console.log("e", e.message);
        }
      }
      _this.term = term;
      _this.runFakeTerminal();
    },
    runFakeTerminal() {
      let term = this.term;
      if (term._initialized) return;
      // 初始化
      term._initialized = true;
      term.onData((raw) => {
        this.write(raw);
      });
    },
    write(data) {
      this.socket.send(this.stringToUint8Array(data));
    },
    stringToUint8Array(str) {
      var arr = [];
      for (var i = 0, j = str.length; i < j; ++i) {
        arr.push(str.charCodeAt(i));
      }
      var tmpUint8Array = new Uint8Array(arr);
      return tmpUint8Array;
    },
  },
  destroyed() {
    // 销毁监听
    this.socket.onclose = this.close;
  },
};
</script>