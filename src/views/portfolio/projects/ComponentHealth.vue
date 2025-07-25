<template>
  <div class="p-4 rounded shadow">
    <h2 class="text-xl font-bold mb-4">Component Health Metrics</h2>

    <div v-if="metrics" class="metrics grid grid-cols-1 md:grid-cols-2 gap-4">
      <div class="summary">
        <dl>
          <div class="mb-2">
            <dt class="font-semibold">Package URL</dt>
            <dd>{{ metrics.purlCoordinates ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Stars</dt>
            <dd>{{ metrics.stars ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Forks</dt>
            <dd>{{ metrics.forks ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Contributors</dt>
            <dd>{{ metrics.contributors ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Weekly Commits</dt>
            <dd>{{ metrics.commitFrequencyWeekly?.toFixed(2) ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Open Issues</dt>
            <dd>{{ metrics.openIssues ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Open PRs</dt>
            <dd>{{ metrics.openPRs ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Last Commit</dt>
            <dd>{{ formattedLastCommit ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Bus Factor</dt>
            <dd>{{ metrics.busFactor ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Dependents</dt>
            <dd>{{ metrics.dependents ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Files</dt>
            <dd>{{ metrics.files ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Scorecard Score</dt>
            <dd>{{ metrics.scorecardScore?.toFixed(2) ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Average Issue Age (days)</dt>
            <dd>{{ metrics.avgIssueAgeDays ?? 'N/A' }}</dd>
          </div>
          <div class="mb-2">
            <dt class="font-semibold">Archived</dt>
            <dd>
              {{
                metrics.repoArchived === true
                  ? 'Yes'
                  : metrics.repoArchived === false
                    ? 'No'
                    : 'N/A'
              }}
            </dd>
          </div>
        </dl>
      </div>

      <div class="features">
        <h3 class="text-lg font-semibold mb-2">Repository Features</h3>
        <ul class="list-disc list-inside">
          <li>
            Readme:
            {{
              metrics.hasReadme === true
                ? '✔️'
                : metrics.hasReadme === false
                  ? '❌'
                  : 'N/A'
            }}
          </li>
          <li>
            Code of Conduct:
            {{
              metrics.hasCodeOfConduct === true
                ? '✔️'
                : metrics.hasCodeOfConduct === false
                  ? '❌'
                  : 'N/A'
            }}
          </li>
          <li>
            Security Policy:
            {{
              metrics.hasSecurityPolicy === true
                ? '✔️'
                : metrics.hasSecurityPolicy === false
                  ? '❌'
                  : 'N/A'
            }}
          </li>
        </ul>
      </div>

      <div class="scorecard col-span-full">
        <h3 class="text-lg font-semibold mb-2">Scorecard Checks</h3>
        <div v-if="metrics.scorecardChecks && metrics.scorecardChecks.length">
          <ul class="space-y-4">
            <li
              v-for="check in metrics.scorecardChecks"
              :key="check.name"
              class="p-3 border rounded"
            >
              <h5 class="font-semibold flex justify-between">
                <span>
                  <strong>{{ check.name }}:</strong> {{ check.score }}/10
                </span>
              </h5>
              <p class="text-sm mb-1 font-italic">{{ check.description }}</p>
              <p class="text-sm mb-1">
                <strong>Reason:</strong> {{ check.reason }}
              </p>
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

    <div v-else-if="gotNoData" class="text-center py-10">
      <span>No health metadata available.</span>
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
      gotNoData: false,
    };
  },
  computed: {
    formattedLastCommit() {
      const date = new Date(this.metrics.lastCommit);
      return isNaN(date.getTime()) ? 'N/A' : date.toLocaleString();
    },
  },
  methods: {
    fetchMetrics() {
      const url = `${this.$api.BASE_URL}/${this.$api.URL_COMPONENT}/${this.uuid}/health`;
      this.axios
        .get(url)
        .then((response) => {
          this.metrics = response.data;
        })
        .catch((err) => {
          if (err.response && err.response.status === 404) {
            this.gotNoData = true;
          }
        });
    },
  },
  mounted() {
    this.fetchMetrics();
  },
};
</script>
