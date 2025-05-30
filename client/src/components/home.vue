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
  <div>
    <div id="home-heading">{{ $t('heading[0]') }}</div>
    <home-summary/>
    <page-body>
      <!-- <div id="home-news-container">
        <home-news/>
        <home-config-section v-if="config.home.title != null"
          :title="config.home.title" :body="config.home.body"/>
      </div> -->
      <project-list/>
    </page-body>
  </div>
</template>

<script setup>
import { defineAsyncComponent, inject } from 'vue';

import HomeNews from './home/news.vue';
import HomeSummary from './home/summary.vue';
import PageBody from './page/body.vue';
import ProjectList from './project/list.vue';

import useProjects from '../request-data/projects';
import { loadAsync } from '../util/load-async';
import { noop } from '../util/util';

defineOptions({
  name: 'Home'
});

const HomeConfigSection = defineAsyncComponent(loadAsync('HomeConfigSection'));

const projects = useProjects();
projects.request({ url: '/v1/projects?forms=true&datasets=true' }).catch(noop);

const config = inject('config');
</script>

<style lang="scss">
@import '../assets/scss/variables';

#home-heading {
  background: $color-subpanel-background;
  color: $color-accent-primary;
  font-size: 35px;
  font-weight: 600;
  letter-spacing: -0.03em;
  margin-left: -15px;
  margin-right: -15px;
  padding: 20px 15px 15px 15px;
}

#home-news-container {
  display: flex;
  > * { flex: 1; }
}
</style>

<i18n lang="json5">
{
  "en": {
    "heading": [
      "Welcome to Central."
    ]
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "heading": [
      "Vítejte v Centralu."
    ]
  },
  "de": {
    "heading": [
      "Willkommen bei Central"
    ]
  },
  "es": {
    "heading": [
      "Bienvenido a Central."
    ]
  },
  "fr": {
    "heading": [
      "Bienvenue sur Central"
    ]
  },
  "id": {
    "heading": [
      "Selamat datang di Central."
    ]
  },
  "it": {
    "heading": [
      "Benvenuto su Central"
    ]
  },
  "sw": {
    "heading": [
      "Karibu Central"
    ]
  },
  "zh-Hant": {
    "heading": [
      "歡迎來到 Central."
    ]
  }
}
</i18n>
