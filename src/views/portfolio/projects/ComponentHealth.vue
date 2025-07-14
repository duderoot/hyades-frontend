<template>
  <div class="p-4 rounded shadow">
    <h2 class="text-xl font-bold mb-4">Component Health Metrics</h2>

    <div v-if="metrics" class="metrics grid grid-cols-1 md:grid-cols-2 gap-4">
      <div class="summary">
        <dl>
          <div class="mb-2">
            <dt class="font-semibold">Package URL</dt>
            <dd>{{ metrics.purl }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Stars</dt>
            <dd>{{ metrics.stars }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Forks</dt>
            <dd>{{ metrics.forks }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Contributors</dt>
            <dd>{{ metrics.contributors }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Weekly Commits</dt>
            <dd>{{ metrics.commitFrequencyWeekly.toFixed(2) }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Open Issues</dt>
            <dd>{{ metrics.openIssues }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Open PRs</dt>
            <dd>{{ metrics.openPRs }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Last Commit</dt>
            <dd>{{ formattedLastCommit }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Bus Factor</dt>
            <dd>{{ metrics.busFactor }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Dependents</dt>
            <dd>{{ metrics.dependents }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Files</dt>
            <dd>{{ metrics.files }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Scorecard Score</dt>
            <dd>{{ metrics.scorecardScore.toFixed(2) }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Average Issue Age (days)</dt>
            <dd>{{ metrics.avgIssueAgeDays }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Archived</dt>
            <dd>{{ metrics.repoArchived ? 'Yes' : 'No' }}</dd>
          </div>
        </dl>
      </div>

      <div class="features">
        <h3 class="text-lg font-semibold mb-2">Repository Features</h3>
        <ul class="list-disc list-inside">
          <li>Readme: {{ metrics.hasReadme ? '✔️' : '❌' }}</li>
          <li>Code of Conduct: {{ metrics.hasCodeOfConduct ? '✔️' : '❌' }}</li>
          <li>
            Security Policy: {{ metrics.hasSecurityPolicy ? '✔️' : '❌' }}
          </li>
        </ul>
      </div>

      <div class="scorecard col-span-full">
        <h3 class="text-lg font-semibold mb-2">Scorecard Checks</h3>
        <div v-if="scorecardChecks.length">
          <ul class="space-y-4">
            <li
              v-for="check in scorecardChecks"
              :key="check.name"
              class="p-3 border rounded"
            >
              <h4 class="font-semibold flex justify-between">
                <span>{{ check.name }}</span>
                <span class="score">{{ check.score }}/10</span>
              </h4>
              <p class="text-sm mb-1">{{ check.description }}</p>
              <a
                :href="check.documentationUrl"
                target="_blank"
                class="text-blue-600 underline text-sm"
                >Read more</a
              >
            </li>
          </ul>
        </div>
        <div v-else>
          <p>No scorecard checks available.</p>
        </div>
      </div>
    </div>

    <div v-else class="text-center py-10">
      <span>Loading metrics...</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ComponentHealth',
  props: {
    uuid: String,
  },
  data() {
    return {
      metrics: null,
      scorecardChecks: [],
    };
  },
  computed: {
    formattedLastCommit() {
      if (!this.metrics || !this.metrics.lastCommit) return 'N/A';
      return new Date(this.metrics.lastCommit).toLocaleString();
    },
  },
  methods: {
    fetchMetrics() {
      const url = `${this.$api.BASE_URL}/${this.$api.URL_COMPONENT}/${this.uuid}/health`;
      this.axios
        .get(url)
        .then((response) => {
          this.metrics = response.data;
          try {
            this.scorecardChecks = response.data.scorecardChecksJson
              ? JSON.parse(response.data.scorecardChecksJson)
              : [];
          } catch (e) {
            console.error('Failed to parse scorecard checks JSON', e);
            this.scorecardChecks = [];
          }
        })
        .catch((err) => {
          console.error('Error fetching metrics', err);
        });
    },
  },
  mounted() {
    this.fetchMetrics();
  },
};
</script>
