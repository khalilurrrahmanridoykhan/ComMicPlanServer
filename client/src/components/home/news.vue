<!--
Copyright 2022 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <page-section id="home-news" horizontal>
    <template #heading>
      <span>{{ $t('title') }}</span>
    </template>
    <template #body>
      <iframe :src="iframeSrc" :title="$t('title')"></iframe>
    </template>
  </page-section>
</template>

<script setup>
import { computed, inject } from 'vue';
import PageSection from '../page/section.vue';

defineOptions({
  name: 'HomeNews'
});

const container = inject('container');
const { i18n: globalI18n } = container;
const locale = computed(() => globalI18n.locale);

const iframeSrc = computed(() => `https://getodk.github.io/central/news.html?outdatedVersionWarning=false&lang=${locale.value}`);

</script>

<style lang="scss">
#home-news {
  margin-bottom: 25px;

  iframe {
    border-width: 0;
    height: 40px;
    width: calc(100% - 35px);
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    // This is a title shown above a section of the page.
    "title": "News"
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "title": "Novinky"
  },
  "de": {
    "title": "Neuigkeiten"
  },
  "es": {
    "title": "Novedades"
  },
  "fr": {
    "title": "Nouvelles"
  },
  "id": {
    "title": "Berita"
  },
  "it": {
    "title": "Novità"
  },
  "sw": {
    "title": "Habari"
  },
  "zh-Hant": {
    "title": "新訊息"
  }
}
</i18n>
