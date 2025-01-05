<template>
  <div class="host-app">
    <button @click="sortEmails" class="sort-button">Sort Emails</button>
    <div class="iframe-container">
      <iframe
        v-for="(url, index) in iframes"
        :key="index"
        :ref="`iframe${index + 1}`"
        :src="url"
        class="iframe"
        @load="() => onIframeLoad(index)"
      ></iframe>
    </div>
  </div>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from "vue";
import emailsDataSource from "../data/emails.json";

const iframes = ["http://localhost:8081", "http://localhost:8082"];

const getIframeByUrl = (url) => document.querySelector(`iframe[src="${url}"]`);

const sendEmails = () => {
  const emails = emailsDataSource;
  const iframe1 = getIframeByUrl(iframes[0]);
  iframe1.contentWindow.postMessage({ type: "LIST_EMAILS", emails }, "*");
};

const sortEmails = () => {
  const iframe2 = getIframeByUrl(iframes[1]);
  iframe2.contentWindow.postMessage({ type: "SORT_EMAILS" }, "*");
};

const relayMessage = (event) => {
  if (event.data?.type === "TOGGLE_EMAIL") {
    const iframe2 = getIframeByUrl(iframes[1]);
    iframe2.contentWindow.postMessage(event.data, "*");
  }
};

const onIframeLoad = (index) => {
  if (index === 0) {
    sendEmails();
  }
};

onMounted(() => {
  window.addEventListener("message", relayMessage);
});

onBeforeUnmount(() => {
  window.removeEventListener("message", relayMessage);
});
</script>

<style>
.host-app {
  color: #32af32 !important;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.sort-button {
  margin: 20px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

.iframe-container {
  display: flex;
  justify-content: center;
}

.iframe {
  margin: 0 10px;
  border: 1px solid #ccc;
  width: 600px;
  height: 400px;
}
</style>
