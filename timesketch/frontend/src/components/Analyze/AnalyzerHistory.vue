<!--
Copyright 2019 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
<template>
  <div class="card card-accent-background" style="margin-top:15px;">
    <header class="card-header">
      <p class="card-header-title">
        Analysis history
      </p>
      <span class="card-header-icon" aria-label="close">
        <span class="delete" v-on:click="$emit('closeHistory')"></span>
      </span>
    </header>
    <div class="card-content">
      <b-table
        v-if="analyses"
        :data="analyses"
        :current-page.sync="currentPage"
        :per-page="perPage"
        paginated
        pagination-simple
        pagination-position="bottom"
        default-sort-direction="desc"
        sort-icon="arrow-down"
        sort-icon-size="is-small"
        icon-pack="fas"
        icon-prev="chevron-left"
        icon-next="chevron-right"
        default-sort="created_at"
      >
        <b-table-column field="created_at" label="Date" width="150" sortable v-slot="props">
          {{ new Date(props.row.created_at) | moment('YYYY-MM-DD HH:mm') }}
        </b-table-column>

        <b-table-column field="name" label="Analyzer" sortable v-slot="props">
          {{ props.row.analyzer_name }}
        </b-table-column>

        <b-table-column field="result" label="Result" sortable v-slot="props">
          {{ props.row.result }}
        </b-table-column>

        <b-table-column field="status" label="Status" sortable v-slot="props" width="40">
          {{ props.row.status[0].status }}
        </b-table-column>
      </b-table>

      <span v-if="!analyses">No logs available. You need to run one of the analyzers first.</span>
    </div>
  </div>
</template>

<script>
import ApiClient from '../../utils/RestApiClient'

export default {
  props: ['timeline'],
  data() {
    return {
      analyses: [],
      currentPage: 1,
      perPage: 5,
    }
  },
  computed: {
    sketch() {
      return this.$store.state.sketch
    },
  },
  created() {
    ApiClient.getSketchTimelineAnalysis(this.sketch.id, this.timeline.id)
      .then(response => {
        this.analyses = response.data.objects[0]
      })
      .catch(e => {})
  },
}
</script>
