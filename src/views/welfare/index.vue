<script lang="ts" setup>
import { defineComponent, onMounted, onUnmounted, reactive, ref } from "vue";
import Breadcrumb from "@/layout/compoenets/Breadcrumb.vue";
import IconifyIconOffline from "@/components/IconifyIcon/src/iconifyIconOffline";

defineComponent({
  name: "Welfare"
});

const loading = ref(1);

const welfareNodes = ref([]);
</script>

<template>
  <div class="main">
    <breadcrumb />
    <div class="app-container-breadcrumb pr-2" v-loading="loading > 0">
      <div class="w-full">
        <template v-if="welfareNodes && welfareNodes.length > 0">
          <el-row :gutter="20">
            <el-col
              v-for="version in welfareNodes"
              :key="version.id"
              :lg="6"
              :md="8"
              :sm="12"
              :xl="6"
              :xs="12"
              class="mb-[20px]"
            >
              <div
                class="w-full download-card bg-white rounded p-4 drop-shadow flex justify-between items-center"
              >
                <div class="left">
                </div>

                <div class="right">
                </div>
              </div>
            </el-col>
          </el-row>
        </template>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
