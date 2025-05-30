<!--
Copyright 2020 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <div id="form-head">
    <breadcrumbs v-if="project.dataExists" :links="breadcrumbLinks"/>
    <div id="form-head-form-nav" class="row">
      <div class="col-xs-12">
        <div class="row">
          <!-- Using .col-xs-6 so that if the form name is long, it is not
          behind #form-head-draft-nav. -->
          <div class="col-xs-6">
            <div v-if="form.dataExists" class="h1" v-tooltip.text>
              {{ form.nameOrId }}
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-xs-6">
            <ul id="form-head-form-tabs" class="nav nav-tabs">
              <!-- Using rendersFormTabs rather than canRoute(), because we want
              to render the tabs even if the form does not have a published
              version (in which case canRoute() will return `false`). -->
              <li v-if="rendersFormTabs" :class="formTabClass('')"
                role="presentation">
                <router-link :to="tabPath('')"
                  v-tooltip.aria-describedby="formTabDescription">
                  {{ $t('common.tab.overview') }}
                </router-link>
              </li>
              <!-- No v-if, because anyone who can navigate to the form should
              be able to navigate to .../versions and .../submissions. -->
              <li :class="formTabClass('versions')" role="presentation">
                <router-link :to="tabPath('versions')"
                  v-tooltip.aria-describedby="formTabDescription">
                  {{ $t('formHead.tab.versions') }}
                </router-link>
              </li>
              <li :class="formTabClass('submissions')" role="presentation">
                <router-link :to="tabPath('submissions')"
                  v-tooltip.aria-describedby="formTabDescription">
                  {{ $t('resource.submissions') }}
                  <span v-if="form.dataExists" class="badge">
                    {{ $n(form.submissions, 'default') }}
                  </span>
                </router-link>
              </li>
              <li v-if="rendersFormTabs" :class="formTabClass('public-links')"
                role="presentation">
                <router-link :to="tabPath('public-links')"
                  v-tooltip.aria-describedby="formTabDescription">
                  {{ $t('formHead.tab.publicAccess') }}
                  <span v-if="form.dataExists" class="badge">
                    {{ $n(form.publicLinks, 'default') }}
                  </span>
                </router-link>
              </li>
              <li v-if="rendersFormTabs" :class="formTabClass('settings')"
                role="presentation">
                <router-link :to="tabPath('settings')"
                  v-tooltip.aria-describedby="formTabDescription">
                  {{ $t('common.tab.settings') }}
                  <span v-if="form.dataExists" class="badge">
                    {{ $t(`formState.${form.state}`) }}
                  </span>
                </router-link>
              </li>
            </ul>
          </div>
          <div v-if="rendersDraftNav" id="form-head-draft-nav"
            class="col-xs-6" :class="{ 'draft-exists': formDraft.isDefined() }">
            <span id="form-head-draft-nav-title">{{ $t('resource.draft') }}</span>
            <button v-show="formDraft.isEmpty()"
              id="form-head-create-draft-button" type="button"
              class="btn btn-primary" @click="$emit('create-draft')">
              <span class="icon-plus-circle"></span>{{ $t('draftNav.action.create') }}
            </button>
            <ul v-show="formDraft.isDefined()" class="nav nav-tabs">
              <li v-if="canRoute(tabPath('draft'))" :class="tabClass('draft')"
                role="presentation">
                <router-link :to="tabPath('draft')">
                  {{ $t('common.status') }}
                </router-link>
              </li>
              <li v-if="canRoute(tabPath('draft/attachments'))"
                :class="tabClass('draft/attachments')" role="presentation">
                <router-link :to="tabPath('draft/attachments')">
                  {{ $t('resource.formAttachments') }}
                  <template v-if="attachments.dataExists">
                    <span v-show="attachments.missingCount !== 0" class="badge">
                      {{ $n(attachments.missingCount, 'default') }}
                    </span>
                  </template>
                </router-link>
              </li>
              <li v-if="canRoute(tabPath('draft/testing'))"
                :class="tabClass('draft/testing')" role="presentation">
                <router-link :to="tabPath('draft/testing')">
                  {{ $t('formHead.draftNav.tab.testing') }}
                </router-link>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Breadcrumbs from '../breadcrumbs.vue';

import useRoutes from '../../composables/routes';
import useTabs from '../../composables/tabs';
import { useRequestData } from '../../request-data';

export default {
  name: 'FormHead',
  components: { Breadcrumbs },
  emits: ['create-draft'],
  setup() {
    // The component does not assume that this data will exist when the
    // component is created.
    const { project, form, formDraft, attachments, resourceStates } = useRequestData();
    const { dataExists } = resourceStates([project, form, formDraft, attachments]);

    const { projectPath, formPath, canRoute } = useRoutes();
    const { tabPath, tabClass } = useTabs(formPath());
    return {
      project, form, formDraft, attachments, dataExists,
      projectPath, formPath, canRoute, tabPath, tabClass
    };
  },
  computed: {
    rendersFormTabs() {
      return this.project.dataExists && this.project.permits(['form.update']);
    },
    formTabDescription() {
      return this.form.dataExists && this.form.publishedAt == null
        ? this.$t('formNav.tabTitle')
        : null;
    },
    rendersDraftNav() {
      return this.dataExists &&
        (this.formDraft.isDefined() || this.project.permits('form.update'));
    },
    breadcrumbLinks() {
      return [
        { text: this.project.dataExists ? this.project.nameWithArchived : this.$t('resource.project'), path: this.projectPath() },
        { text: this.$t('resource.forms'), path: this.projectPath(), icon: 'icon-file' }
      ];
    }
  },
  methods: {
    formTabClass(path) {
      const htmlClass = this.tabClass(path);
      if (this.form.dataExists && this.form.publishedAt == null)
        htmlClass.disabled = true;
      return htmlClass;
    }
  }
};
</script>

<style lang="scss">
@import '../../assets/scss/mixins';

$draft-nav-padding: 23px;
$tab-li-margin-top: 5px;

.breadcrumbs + #form-head-form-nav .h1 {
  margin-top: 0;
}

#form-head-form-nav {
  background-color: $color-subpanel-background;
  border-bottom: 1px solid $color-subpanel-border-strong;

  .h1 {
    @include text-overflow-ellipsis;
    margin-bottom: -10px;
  }

  .nav-tabs > li {
    // It might be simpler to move this margin to the .nav-tabs element so that
    // fewer elements have margin.
    margin-top: $tab-li-margin-top;
  }
}

#form-head-form-tabs {
  margin-top: $draft-nav-padding;

  > li.active > a {
    &, &:hover, &:focus { background-color: $color-subpanel-active; }
  }
}

#form-head-draft-nav {
  background-color: #ddd;
  padding-top: $draft-nav-padding;
  position: relative;

  #form-head-draft-nav-title {
    color: #666;
    font-size: 12px;
    position: absolute;
    top: 7px;
  }
  &.draft-exists #form-head-draft-nav-title {
    left: /* .col-xs-6 padding-left */ 15px +
      /* .nav-tabs > li > a padding-left */ 8px;
  }

  #form-head-create-draft-button {
    /*
    6px =   1px (.nav-tabs > li > a has more top padding than .btn)
          + 1px (.nav-tabs > li > a has more bottom padding)
          + 1px (.nav-tabs > li > a has a bottom border)
          + 3px (because .nav-tabs > li > a has a higher font size?)
    */
    margin-bottom: 6px;
    margin-top: $tab-li-margin-top;
  }

  .nav-tabs > li.active > a {
    &, &:hover, &:focus { background-color: $color-page-background; }
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    "projectNav": {
      "action": {
        "back": "Back to Project Overview"
      }
    },
    "formNav": {
      // Tooltip text that will be shown when hovering over tabs for Form Overview, Submissions, etc.
      "tabTitle": "These functions will become available once you publish your Draft Form"
    },
    "draftNav": {
      "action": {
        "create": "Create a new Draft"
      },
    }
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "projectNav": {
      "action": {
        "back": "Zpět na přehled projektu"
      }
    },
    "formNav": {
      "tabTitle": "Tyto funkce budou k dispozici, jakmile zveřejníte svůj koncept formuláře"
    },
    "draftNav": {
      "action": {
        "create": "Vytvořit nový koncept"
      }
    }
  },
  "de": {
    "projectNav": {
      "action": {
        "back": "Zurück zur Projektübersicht"
      }
    },
    "formNav": {
      "tabTitle": "Diese Funktionen stehen zur Verfügung, wenn Sie Ihren Entwurf veröffentlicht haben."
    },
    "draftNav": {
      "action": {
        "create": "Neuen Entwurf erstellen"
      }
    }
  },
  "es": {
    "projectNav": {
      "action": {
        "back": "Volver a la descripción general del proyecto."
      }
    },
    "formNav": {
      "tabTitle": "Estas funciones estarán disponibles una vez que publique su borrador de formulario."
    },
    "draftNav": {
      "action": {
        "create": "Crear un nuevo borrador"
      }
    }
  },
  "fr": {
    "projectNav": {
      "action": {
        "back": "Retourner à l'aperçu du projet"
      }
    },
    "formNav": {
      "tabTitle": "Ces fonctions seront disponibles quand vous publierez votre ébauche"
    },
    "draftNav": {
      "action": {
        "create": "Créer une nouvelle ébauche"
      }
    }
  },
  "id": {
    "projectNav": {
      "action": {
        "back": "Kembali ke Gambaran Proyek"
      }
    },
    "formNav": {
      "tabTitle": "Fungsi ini akan tersedia setelah Anda menerbitkan draf formulir Anda"
    },
    "draftNav": {
      "action": {
        "create": "Buat draf baru"
      }
    }
  },
  "it": {
    "projectNav": {
      "action": {
        "back": "Torna alla panoramica del progetto"
      }
    },
    "formNav": {
      "tabTitle": "Queste funzioni diventeranno disponibili una volta che avrai pubblicato la tua bozza del formulario"
    },
    "draftNav": {
      "action": {
        "create": "Crea una nuova bozza"
      }
    }
  },
  "ja": {
    "projectNav": {
      "action": {
        "back": "プロジェクトの概要に戻る"
      }
    },
    "formNav": {
      "tabTitle": "これらの機能は下書きフォームが公開された際に有効になります。"
    },
    "draftNav": {
      "action": {
        "create": "新規下書きの作成"
      }
    }
  },
  "sw": {
    "projectNav": {
      "action": {
        "back": "Rudi kwa Muhtasari wa Mradi"
      }
    },
    "formNav": {
      "tabTitle": "Vipengele hivi vitapatikana mara tu utakapochapisha Rasimu ya Fomu yako"
    },
    "draftNav": {
      "action": {
        "create": "Unda Rasimu mpya"
      }
    }
  },
  "zh-Hant": {
    "projectNav": {
      "action": {
        "back": "返回專案概覽"
      }
    },
    "formNav": {
      "tabTitle": "一旦您發布草稿表單，這些功能將可用"
    },
    "draftNav": {
      "action": {
        "create": "建立新草稿"
      }
    }
  }
}
</i18n>
