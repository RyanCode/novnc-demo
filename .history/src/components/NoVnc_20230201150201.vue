<template>
  <div id="screen" style="width: 100%; height: 100%"></div>
</template>

<script>
import RFB from "@novnc/novnc/core/rfb";
export default {
  mounted() {
    this.connectVnc();
  },
  data() {
    return {
      path: "ws://192.168.20.42:12345",
    };
  },
  methods: {
    // vnc连接断开的回调函数
    disconnectedFromServer(msg) {
      if (msg.detail.clean) {
        // 根据 断开信息的msg.detail.clean 来判断是否可以重新连接
        this.connectVnc();
      } else {
        //这里做不可重新连接的一些操作
        console.log("链接失败,重新链接中-------" + msg);
        setTimeout(() => {
          this.connectVnc();
        },10000);
      }
    },
    // 连接成功的回调函数
    connectedToServer(msg) {
      // console.log('success', msg)
    },
    //连接vnc的函数
    connectVnc() {
      const PASSWORD = "admin";
      let rfb = new RFB(document.getElementById("screen"), this.path, {
        // 向vnc 传递的一些参数，比如说虚拟机的开机密码等
        credentials: { password: PASSWORD },
      });
      rfb.addEventListener("connect", this.connectedToServer);
      rfb.addEventListener("disconnect", this.disconnectedFromServer);
      rfb.scaleViewport = false; //scaleViewport指示是否应在本地扩展远程会话以使其适合其容器。禁用时，如果远程会话小于其容器，则它将居中，或者根据clipViewport它是否更大来处理。默认情况下禁用。
      rfb.resizeSession = false; //是一个boolean指示是否每当容器改变尺寸应被发送到调整远程会话的请求。默认情况下禁用
      console.log(rfb);
      this.rfb = rfb;
    },
  },
};
</script>
<style scoped>
#screen canvas{
  overflow: auto;
}
</style>
