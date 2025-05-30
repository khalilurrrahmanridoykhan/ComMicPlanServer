<!--
Copyright 2023 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->

<template>
  <summary-item id="linked-forms" icon="link">
    <template #heading>
      {{ linkedForms.length }}
    </template>
    <template #body>
      <p>{{ $tc('formsConsumeData', linkedForms.length) }}</p>
      <table v-if="linkedForms.length > 0" class="table">
        <tbody>
          <tr v-for="(form) in linkedForms" :key="form.xmlFormId">
            <td>
              <form-link :form="form"
                :to="publishedFormPath(form.projectId, form.xmlFormId)"
                v-tooltip.text/>
            </td>
          </tr>
        </tbody>
      </table>
    </template>
  </summary-item>
</template>

<script setup>
import { computed } from 'vue';

import FormLink from '../../form/link.vue';
import SummaryItem from '../../summary-item.vue';

import useRoutes from '../../../composables/routes';
import { useRequestData } from '../../../request-data';

defineOptions({
  name: 'LinkedForms'
});

const { dataset } = useRequestData();
const linkedForms = computed(() => dataset.linkedForms);

const { publishedFormPath } = useRoutes();
</script>

<style lang="scss">
@import '../../../assets/scss/mixins';

#linked-forms {
  margin-bottom: 10px;

  .table{
    margin-bottom: 10px;

    td {
      @include text-overflow-ellipsis;
      padding: 8px 0;
    }
    > tbody > tr:first-child > td {
      border-top: none;
    }
    a {
      font-size: 16px;
    }
  }

}
</style>

<i18n lang="json5">
{
  "en": {
    // Number of form(s) is shown separately above this text
    "formsConsumeData": "Form uses this Entity List | Forms use this Entity List",
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "de": {
    "formsConsumeData": "Formular verwendet diese Entitätsliste | Formulare verwenden diese Entitätsliste"
  },
  "es": {
    "formsConsumeData": "El formulario utilizan esta lista de entidades | Los formularios utilizan esta lista de entidades | Los formularios utilizan esta lista de entidades"
  },
  "fr": {
    "formsConsumeData": "Formulaire utilise cette liste d'entités | Formulaires utilisent cette liste d'entités | Formulaires utilisent cette liste d'entités"
  },
  "it": {
    "formsConsumeData": "Il formulario utilizza questo elenco di entità | I formulari utilizzano questo elenco di entità | I formulari utilizzano questo elenco di entità"
  },
  "sw": {
    "formsConsumeData": "Fomu hutumia Orodha hii ya Huluki | Fomu hutumia Orodha hii ya Huluki"
  },
  "zh-Hant": {
    "formsConsumeData": "表單使用此實體列表"
  }
}
</i18n>
