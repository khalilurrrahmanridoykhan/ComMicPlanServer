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
  <div class="dataset-summary-row">
    <div class="row">
      <div class="col-xs-6 dataset-name-wrap">
        <div class="dataset-name text-overflow-ellipsis" v-tooltip.text>
          <dataset-link v-if="!dataset.isNew" :project-id="projectId"
            :name="dataset.name"/>
          <template v-else>
            {{ dataset.name }}
          </template>
        </div>
        <div v-if="dataset.isNew" class="dataset-new">
          <span class="icon-plus-circle"></span>
          {{ $t('new') }}
        </div>
      </div>
      <!-- eslint-disable-next-line vuejs-accessibility/click-events-have-key-events, vuejs-accessibility/interactive-supports-focus -->
      <div class="col-xs-6 properties-count" role="button" @click.prevent="toggleExpanded">
        {{ $tcn('common.propertiesCount', dataset.properties.length, { inform: $n(inFormProperties.length, 'default') }) }}
        <!-- eslint-disable-next-line vuejs-accessibility/anchor-has-content -->
        <a href="javascript:void(0)" class="expand-button">
          <span v-if="!expanded" class="icon-caret-left"></span>
          <span v-else class="icon-caret-down"></span>
        </a>
      </div>
    </div>
    <div v-show="expanded" class="property-list">
      <i18n-list v-slot="{ value: property }" :list="inFormProperties">
        <span>{{ property.name }}</span>
        <span v-if="property.isNew">
          <span class="icon-plus-circle property-new" v-tooltip.sr-only></span>
          <span class="sr-only">&nbsp;{{ $t('addedByThisDraft') }}</span>
        </span>
      </i18n-list>
      <span v-if="inFormProperties.length === 0" class="no-properties">{{ $t('entity.noProperties') }}</span>
    </div>
  </div>
</template>

<script>
import DatasetLink from '../link.vue';
import I18nList from '../../i18n/list.vue';

export default {
  name: 'DatasetSummaryRow',
  components: { DatasetLink, I18nList },
  props: {
    dataset: {
      type: Object,
      required: true
    },
    projectId: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      expanded: false
    };
  },
  computed: {
    inFormProperties() {
      return this.dataset.properties.filter(p => p.inForm);
    }
  },
  methods: {
    toggleExpanded() {
      this.expanded = !this.expanded;
    }
  }
};

</script>

<style lang="scss">
@import '../../../assets/scss/mixins';

.dataset-summary-row {
    @include text-block;

    .text-overflow-ellipsis {
      @include text-overflow-ellipsis;
    }

    .dataset-name-wrap{
      display: flex;

      .dataset-name {
        font-weight: bold;
        font-size: 18px;
      }
      .dataset-new {
          vertical-align: super;
          color: $color-success;
          margin-left: 2px;
          min-width: 46px;
      }
    }

    .property-new {
        margin-left: 2px;
        color: $color-success;
        vertical-align: super;
    }

    .properties-count {
        line-height: 28px;
    }

    a.expand-button {
        color: #666;
        font-size: 20px;
        vertical-align: middle;

        &:focus {
            background-color: inherit;
        }
    }

    .expand-button {
      margin-left: 5px;
    }

    .property-list {
      hyphens: auto;
      overflow-wrap: break-word;

      .no-properties { @include italic; }
    }
}

</style>

<i18n lang="json5">
{
  "en": {
    // This is shown when an Entity List is new
    "new": "new!",
    // This is shown when mouse hovers over plus icon of new Entity Property
    "addedByThisDraft": "Added by this Draft"
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "new": "nový!",
    "addedByThisDraft": "Přidáno podle tohoto návrhu"
  },
  "de": {
    "new": "Neu!",
    "addedByThisDraft": "Hinzugefügt von diesem Entwurf"
  },
  "es": {
    "new": "¡nuevo!",
    "addedByThisDraft": "Añadido por este borrador"
  },
  "fr": {
    "new": "nouveau !",
    "addedByThisDraft": "Ajouté par cette ébauche"
  },
  "id": {
    "new": "baru!"
  },
  "it": {
    "new": "nuovo!",
    "addedByThisDraft": "Aggiunto da questa bozza"
  },
  "sw": {
    "new": "mpya!",
    "addedByThisDraft": "Imeongezwa na Rasimu hii"
  },
  "zh-Hant": {
    "new": "新增！",
    "addedByThisDraft": "已由草稿新增"
  }
}
</i18n>
