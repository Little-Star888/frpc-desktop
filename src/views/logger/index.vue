<script lang="ts" setup>
import IconifyIconOffline from "@/components/IconifyIcon/src/iconifyIconOffline";
import Breadcrumb from "@/layout/compoenets/Breadcrumb.vue";
import {
  on,
  onListener,
  removeRouterListeners,
  removeRouterListeners2,
  send
} from "@/utils/ipcUtils";
import { useDebounceFn } from "@vueuse/core";
import { ElMessage } from "element-plus";
import { defineComponent, onMounted, onUnmounted, ref } from "vue";
import { useI18n } from "vue-i18n";
import { ipcRouters, listeners } from "../../../electron/core/IpcRouter";

defineComponent({
  name: "Logger"
});

const { t } = useI18n();

const loggerContent = ref(
  `<div class="text-white">${t("logger.content.empty")}</div>`
);

const formatLogContent = (logContent: string) => {
  const logs = logContent
    .split("\n")
    .filter(f => f)
    .map(m => {
      if (m.indexOf("[E]") !== -1) {
        return `<div class="text-[#FF0006]">${m}</div> `;
      } else if (m.indexOf("[I]") !== -1) {
        return `<div class="text-[#48BB31]">${m}</div> `;
      } else if (m.indexOf("[D]") !== -1) {
        return `<div class="text-[#0070BB]">${m}</div> `;
      } else if (m.indexOf("[W]") !== -1) {
        return `<div class="text-[#BBBB23]">${m}</div> `;
      } else {
        return `<div class="text-[#BBBBBB]">${m}</div> `;
      }
    });
  return logs.reverse().join("");
};
const refreshStatus = ref(false);
const logLoading = ref(true);
// const isWatch = ref(false);

onMounted(() => {
  send(ipcRouters.LOG.getFrpLogContent);
  on(ipcRouters.LOG.getFrpLogContent, data => {
    if (data) {
      loggerContent.value = formatLogContent(data as string);
    }

    logLoading.value = false;
    if (refreshStatus.value) {
      // 刷新逻辑
      ElMessage({
        type: "success",
        message: t("logger.message.refreshSuccess")
      });
      refreshStatus.value = false;
    }
  });
  on(ipcRouters.LOG.openFrpcLogFile, () => {
    ElMessage({
      type: "success",
      message: t("logger.message.openSuccess")
    });
  });
  onListener(listeners.watchFrpcLog, data => {
    send(ipcRouters.LOG.getFrpLogContent);
  });
});

const openLocalLog = useDebounceFn(() => {
  send(ipcRouters.LOG.openFrpcLogFile);
}, 1000);

const refreshLog = useDebounceFn(() => {
  // ElMessage({
  //   type: "warning",
  //   icon: "<IconifyIconOffline icon=\"file-open-rounded\" />",
  //   message: "正在刷新日志..."
  // });
  refreshStatus.value = true;
  logLoading.value = true;
  send(ipcRouters.LOG.getFrpLogContent);
}, 300);

onUnmounted(() => {
  removeRouterListeners(ipcRouters.LOG.getFrpLogContent);
  removeRouterListeners(ipcRouters.LOG.openFrpcLogFile);
  removeRouterListeners2(listeners.watchFrpcLog);
});
</script>
<template>
  <div class="main">
    <breadcrumb>
      <el-button plain type="primary" @click="refreshLog">
        <IconifyIconOffline icon="refresh-rounded" />
      </el-button>
      <el-button plain type="primary" @click="openLocalLog">
        <IconifyIconOffline icon="file-open-rounded" />
      </el-button>
    </breadcrumb>
    <div class="app-container-breadcrumb" v-loading="logLoading">
      <div
        class="w-full h-full p-2 bg-[#2B2B2B] rounded drop-shadow-lg overflow-y-auto"
        v-html="loggerContent"
      ></div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
::-webkit-scrollbar-track-piece {
  background-color: transparent;
}
</style>
