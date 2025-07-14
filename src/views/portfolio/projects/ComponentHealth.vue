<template>
  <div>Abfahrt.</div>
</template>

<script>
export default {
  name: 'ComponentHealth',
  props: {
    uuid: String,
  },
  components: {
    // subcomponents
  },
  data() {
    return {};
  },
  methods: {
    fetchMetrics() {
      let url = `${this.$api.BASE_URL}/${this.$api.URL_COMPONENT}/${this.uuid}/health`;
      this.axios.get(url).then((response) => {
        // TODO
      });
    },
  },
  watch: {},
  beforeMount() {},
  mounted() {
    this.fetchMetrics();
  },
};
</script>
