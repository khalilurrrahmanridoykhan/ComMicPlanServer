<!--
Copyright 2017 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <tr class="field-key-row" :class="{ success: fieldKey.id === highlighted }">
    <td class="display-name">
      <span v-tooltip.text>{{ fieldKey.displayName }}</span>
    </td>
    <td>
      <time-and-user :iso="fieldKey.createdAt" :user="fieldKey.createdBy"/>
    </td>
    <td><date-time :iso="fieldKey.lastUsed"/></td>
    <td>
      <a v-if="fieldKey.token != null" ref="popoverLink" href="#"
        class="field-key-row-popover-link" role="button"
        @click.prevent="toggleQr">
        <span class="icon-qrcode"></span>{{ $t('seeCode') }}
      </a>
      <template v-else>
        {{ $t('accessRevoked') }}
      </template>
    </td>
    <td>
      <div class="dropdown">
        <button :id="actionsId" type="button"
          class="btn btn-default dropdown-toggle" data-toggle="dropdown"
          aria-haspopup="true" aria-expanded="false">
          <span class="icon-cog"></span><span class="caret"></span>
        </button>
        <ul class="dropdown-menu dropdown-menu-right"
          :aria-labelledby="actionsId">
          <li :class="{ disabled: fieldKey.token == null }">
            <a href="#" @click.prevent="$emit('revoke', fieldKey)">
              {{ $t('action.revokeAccess') }}&hellip;
            </a>
          </li>
        </ul>
      </div>
    </td>
  </tr>
</template>

<script>
import DateTime from '../date-time.vue';
import TimeAndUser from '../time-and-user.vue';

export default {
  name: 'FieldKeyRow',
  components: { DateTime, TimeAndUser },
  props: {
    fieldKey: {
      type: Object,
      required: true
    },
    highlighted: Number
  },
  emits: ['toggle-qr', 'revoke'],
  computed: {
    actionsId() {
      return `field-key-row-actions${this.fieldKey.id}`;
    }
  },
  methods: {
    toggleQr() {
      this.$emit('toggle-qr', this.fieldKey, this.$refs.popoverLink);
    }
  }
};
</script>

<style lang="scss">
.field-key-row {
  .table tbody & td { vertical-align: middle; }

  .display-name {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    // Clicking on this text displays an App User QR code for configuring ODK Collect.
    "seeCode": "See code",
    // This text is shown for an App User whose access has been revoked.
    "accessRevoked": "Access revoked",
    "action": {
      "revokeAccess": "Revoke access"
    }
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "seeCode": "Viz kód",
    "accessRevoked": "Přístup byl zrušen",
    "action": {
      "revokeAccess": "Odebrat přístup"
    }
  },
  "de": {
    "seeCode": "QR-Code",
    "accessRevoked": "Zugriffsberechtigung zurückgezogen",
    "action": {
      "revokeAccess": "Zugriffsberechtigung entziehen"
    }
  },
  "es": {
    "seeCode": "Ver código",
    "accessRevoked": "Acceso revocado",
    "action": {
      "revokeAccess": "Revocar el acceso"
    }
  },
  "fr": {
    "seeCode": "Voir le code",
    "accessRevoked": "Accès retiré",
    "action": {
      "revokeAccess": "Retirer l'accès"
    }
  },
  "id": {
    "seeCode": "Lihat kode",
    "accessRevoked": "Akses dicabut",
    "action": {
      "revokeAccess": "Cabut akses"
    }
  },
  "it": {
    "seeCode": "Mostra il codice",
    "accessRevoked": "Accesso revocato",
    "action": {
      "revokeAccess": "Revoca l'accesso utente"
    }
  },
  "ja": {
    "seeCode": "QRコードを表示",
    "accessRevoked": "アクセス権の取消済み",
    "action": {
      "revokeAccess": "アクセス権の取消"
    }
  },
  "sw": {
    "seeCode": "Angalia msimbo",
    "accessRevoked": "Ufikiaji umebatilishwa",
    "action": {
      "revokeAccess": "Batilisha ufikiaji"
    }
  },
  "zh-Hant": {
    "seeCode": "查看 QR code",
    "accessRevoked": "存取權限被撤銷",
    "action": {
      "revokeAccess": "撤銷存取權限"
    }
  }
}
</i18n>
