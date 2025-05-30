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
  <tr class="project-form-row">
    <td class="col-icon">
      <span v-if="showIcon" class="icon-file"></span>
    </td>
    <td class="form-name">
      <template v-if="canLinkToFormOverview">
        <form-link :form="form"/>
      </template>
      <template v-else-if="canLinkToSubmissions">
        <form-link :form="form" :to="submissionsPath.all"/>
      </template>
      <template v-else>
        <template v-if="canLinkToEnketo">
          <a :href="enketoPath" target="_blank">{{ form.nameOrId }}</a>
        </template>
        <template v-else>
          {{ form.nameOrId }}
        </template>
      </template>

      <span v-if="showIdForDuplicateName" class="duplicate-form-id">({{ form.xmlFormId }})</span>
    </td>
    <template v-if="form.publishedAt != null">
      <td v-for="reviewState of visibleReviewStates" :key="reviewState" class="review-state">
        <span v-tooltip.no-aria="$t(`reviewState.${reviewState}`)">
          <template v-if="canLinkToSubmissions">
            <router-link :to="submissionsPath[reviewState]">
              <span>{{ $n(form.reviewStates[reviewState], 'default') }}</span>
              <span :class="reviewStateIcon(reviewState)"></span>
            </router-link>
          </template>
          <template v-else>
            <span>{{ $n(form.reviewStates[reviewState], 'default') }}</span>
            <span :class="reviewStateIcon(reviewState)"></span>
          </template>
        </span>
      </td>
      <td class="last-submission">
        <span v-tooltip.no-aria="lastSubmissionTooltip">
          <template v-if="form.lastSubmission != null">
            <template v-if="canLinkToSubmissions">
              <router-link :to="submissionsPath.all">
                <date-time :iso="form.lastSubmission" relative="past"
                  :tooltip="false"/>
                <span class="icon-clock-o"></span>
              </router-link>
            </template>
            <template v-else>
              <date-time :iso="form.lastSubmission" relative="past"
                :tooltip="false"/>
              <span class="icon-clock-o"></span>
            </template>
          </template>
          <template v-else>{{ $t('submission.noSubmission') }}</template>
        </span>
      </td>
      <td class="total-submissions">
        <span v-tooltip.no-aria="$t('common.totalSubmissions')">
          <template v-if="canLinkToSubmissions">
            <router-link :to="submissionsPath.all">
              <span>{{ $n(form.submissions, 'default') }}</span>
              <span class="icon-asterisk"></span>
            </router-link>
          </template>
          <template v-else>
            <span>{{ $n(form.submissions, 'default') }}</span>
            <span class="icon-asterisk"></span>
          </template>
        </span>
      </td>
    </template>
    <template v-else>
      <td class="not-published" colspan="5">
        <span>{{ $t('formState.unpublished') }}</span>
        <span class="icon-asterisk"></span>
      </td>
    </template>
  </tr>
</template>

<script>
import { DateTime } from 'luxon';

import DateTimeComponent from '../date-time.vue';
import FormLink from '../form/link.vue';

import useReviewState from '../../composables/review-state';
import useRoutes from '../../composables/routes';
import { enketoBasePath } from '../../util/util';
import { formatDateTime } from '../../util/date-time';
import { useRequestData } from '../../request-data';

export default {
  name: 'ProjectFormRow',
  components: { DateTime: DateTimeComponent, FormLink },
  props: {
    form: {
      type: Object,
      required: true
    },
    project: {
      type: Object,
      required: true
    },
    // Whether to show the Form icon or not
    // We show it only for the first row
    showIcon: {
      type: Boolean,
      required: true
    }
  },
  setup() {
    const { projects } = useRequestData();
    const { duplicateFormNamesPerProject } = projects.toRefs();
    const { formPath } = useRoutes();
    const { reviewStateIcon } = useReviewState();
    return {
      duplicateFormNamesPerProject,
      formPath,
      reviewStateIcon
    };
  },
  computed: {
    canLinkToFormOverview() {
      return this.project.permits('form.update');
    },
    canLinkToSubmissions() {
      return this.project.permits('submission.list');
    },
    canLinkToEnketo() {
      return this.form.publishedAt != null && this.form.state === 'open' && this.form.enketoId !== null;
    },
    visibleReviewStates: () => ['received', 'hasIssues', 'edited'],
    submissionsPath() {
      const submissionPath = this.formPath(
        this.form.projectId,
        this.form.xmlFormId,
        'submissions'
      );
      return {
        received: `${submissionPath}?reviewState=null`,
        edited: `${submissionPath}?reviewState=%27edited%27`,
        hasIssues: `${submissionPath}?reviewState=%27hasIssues%27`,
        all: submissionPath
      };
    },
    enketoPath() {
      const encodedId = encodeURIComponent(this.form.enketoId);
      return `${enketoBasePath}/${encodedId}`;
    },
    showIdForDuplicateName() {
      const formNames = this.duplicateFormNamesPerProject[this.project.id];
      if (formNames) {
        return formNames.has(this.form.nameOrId.toLocaleLowerCase());
      }
      return false;
    },
    lastSubmissionTooltip() {
      const { lastSubmission } = this.form;
      const header = this.$t('header.lastSubmission');
      if (lastSubmission == null) return header;
      const formatted = formatDateTime(DateTime.fromISO(lastSubmission));
      return `${header}\n${formatted}`;
    }
  }
};
</script>

<style lang="scss">
@import '../../assets/scss/mixins';

.project-form-row {
  td {
    font-size: 16px;
    padding: 4px 0px 4px 6px;
    color: #333;
    a { @include text-link; }
  }

  .duplicate-form-id {
    font-family: $font-family-monospace;
    font-size: 12px;
    padding-left: 6px;
  }

  .review-state, .total-submissions, .not-published {
    text-align: right;
    padding-right: 10px;
    width: 80px;
  }

  .last-submission{
    text-align: right;
    width: 170px;
  }

  [class*='icon'] {
    margin-left: 5px;
    color: #888;
  }
}
</style>
