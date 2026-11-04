<template>
  <Achievements ref="achievementsRef" />
  <main id="mainpart" class="mx-auto w-full px-4 jn-container">
    <div class="rounded-md">
      <IPCheck />
      <Connectivity />
      <WebRTC />
      <DNSLeaks />
      <SpeedTest />
      <AdvancedTools ref="advancedToolsRef" />
      <!-- IP 环境泄漏诊断（付费人工服务）：台账 2026-11-02 派单，闲鱼商品已上架。
           口径：¥39/单、24 小时内交付书面报告；只做诊断与合规整改建议，
           不卖账号、不卖代理、不承诺任何平台注册/审核结果。 -->
      <section class="my-6 rounded-md border border-secondary p-4 text-sm">
        <p class="font-semibold text-foreground">检测结果看不懂？人工诊断（付费）</p>
        <p class="mt-1 text-muted-foreground">
          在本站完成检测后，凭结果截图或链接下单，即可获得一份书面诊断报告：
          泄漏点清单、严重程度，以及针对你自己设备与账号的合规整改步骤，24 小时内交付，¥39/单。
          只做诊断与合规整改建议，不卖账号、不卖代理、不承诺注册结果。
        </p>
        <a href="https://m.tb.cn/h.jTXTy5u?tk=XS1r6ZNcS3n" target="_blank" rel="noopener"
          class="mt-3 inline-flex items-center gap-1 font-medium text-foreground underline underline-offset-4">
          前往闲鱼下单：IP 环境泄漏诊断 ¥39/单
        </a>
      </section>
    </div>
  </main>
  <FloatingDock :ready="showMaskButton" :mask-active="infoMaskLevel > 0">
    <InfoMask :showMaskButton.value="showMaskButton" :infoMaskLevel.value="infoMaskLevel"
      :toggleInfoMask="toggleInfoMask" />
    <ShareReport ref="shareReportRef" />
    <IPHistory />
    <QueryIP ref="queryIPRef" />
  </FloatingDock>
  <HelpModal ref="helpModalRef" />
  <Footer />
</template>

<script setup>
// The homepage (`/`): every top-level section plus the Advanced Tools cards;
// what every route shares lives in App.vue.
//
// Components — the test sections and the always-visible chrome load
// synchronously; everything the first paint can't show (dialogs, the
// below-fold Footer) is an async component so its code stays
// out of the route chunk and out of the mount's critical path. Their
// template refs are null until the chunk lands — consumers (use-shortcuts)
// must optional-chain.
import IPCheck from './IpInfos.vue';
import Connectivity from './ConnectivityTest.vue';
import WebRTC from './WebRtcTest.vue';
import DNSLeaks from './DnsLeaksTest.vue';
import SpeedTest from './SpeedTest.vue';
import AdvancedTools from './Advanced.vue';
import InfoMask from './widgets/InfoMask.vue';
import FloatingDock from './widgets/FloatingDock.vue';

// Vue + Store
import { ref, computed, onMounted, defineAsyncComponent } from 'vue';

// Async (off-critical-path) components
const Footer = defineAsyncComponent(() => import('./Footer.vue'));
const Achievements = defineAsyncComponent(() => import('./Achievements.vue'));
const QueryIP = defineAsyncComponent(() => import('./widgets/QueryIP.vue'));
const HelpModal = defineAsyncComponent(() => import('./widgets/Help.vue'));
const IPHistory = defineAsyncComponent(() => import('./widgets/IPHistory.vue'));
const ShareReport = defineAsyncComponent(() => import('./report/ShareReportDialog.vue'));
import { useMainStore } from '@/store';
import { useI18n } from 'vue-i18n';

// Composables
import { useInfoMask } from '@/composables/use-info-mask.js';
import { useRefreshOrchestrator } from '@/composables/use-refresh-orchestrator.js';
import { useShortcuts } from '@/composables/use-shortcuts.js';
import { useSectionTracking } from '@/composables/use-section-tracking.js';
import { useDocumentMeta } from '@/composables/use-document-meta.js';

defineOptions({ name: 'Home' });

const { t } = useI18n();
const store = useMainStore();
const configs = computed(() => store.configs);
const userPreferences = computed(() => store.userPreferences);

// Template refs — UI chrome only; the test sections are reached through the
// command bus (utils/app-commands.js), not refs.
const achievementsRef = ref(null);
const queryIPRef = ref(null);
const helpModalRef = ref(null);
const shareReportRef = ref(null);
const advancedToolsRef = ref(null);

// Info mask
const { infoMaskLevel, isInfosLoaded, showMaskButton, toggleInfoMask } = useInfoMask({
    store,
    t,
});

// Refresh / initial load sequence
const { loadingControl } = useRefreshOrchestrator({
    store,
    t,
    userPreferences,
    infoMaskLevel,
});

// Shortcuts
const { loadShortcuts } = useShortcuts({
    refs: {
        queryIPRef, helpModalRef, shareReportRef, advancedToolsRef,
        isInfosLoaded, toggleInfoMask,
    },
    store, t, configs, userPreferences,
});

// Scroll monitoring + section tracking (logic from widgets/Patch.vue)
useSectionTracking();

// Localized homepage head. Provide title/description explicitly via t() rather
// than leaning on use-document-meta's DEFAULT_META snapshot: that snapshot is
// taken at module load, before the (now async) locale messages land, so it would
// pin the head to index.html's English title.
useDocumentMeta(() => ({
    title: t('page.title'),
    description: t('page.description'),
    canonical: `${window.location.origin}/`,
}));

onMounted(() => {
    loadingControl();
    loadShortcuts();
});
</script>
