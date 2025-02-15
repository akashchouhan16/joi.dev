<template>
  <div class="landing-table-wrapper">
    <table class="landing-table">
      <thead>
        <tr class="header-row">
          <th class="version-header">Version</th>
          <th class="license-header">License</th>
          <th class="node-header">Node</th>
          <th class="ci-header">CI</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="version in module.versions"
          :key="version.name + version.license"
          class="module-row landing-table-row"
        >
          <td>
            <div class="module-version-wrapper">
              <div class="version-name">{{ version.name }}</div>
              <a
                class="version-helmet"
                :href="
                  name === 'joi'
                    ? `/api/?v=${version.name}`
                    : newRepos[name][version.name].api
                    ? `/module/${name}/api?v=${version.name}`
                    : `https://github.com/hapijs/${name}/tree/${version.branch}`
                "
              >
                <img
                  src="/img/joiTransparent.png"
                  alt="joi logo"
                  class="version-img"
                />
              </a>
              <a
                :href="
                  'https://github.com/hapijs/' +
                  name +
                  '/tree/' +
                  version.branch
                "
                target="_blank"
              >
                <img
                  src="/img/githubLogo.png"
                  alt="github logo"
                  class="version-img"
                />
              </a>
            </div>
          </td>
          <td class="module-license">{{ version.license }}</td>
          <td class="table-version">{{ version.node }}</td>
          <td class="status-badge">
            <a
              :href="
                module.versions.length > 1
                  ? `https://github.com/hapijs/${name}/branches/actions?query=workflow%3Aci`
                  : `https://github.com/hapijs/${name}/actions?query=workflow%3Aci`
              "
              target="_blank"
            >
              <img
                :id="`ci${name}${version.name}`"
                :src="`https://github.com/hapijs/${name}/workflows/ci/badge.svg?branch=${version.branch}`"
                alt="Build Status"
                class="hide"
                @load="swapImg(`ci${name}${version.name}`, version.branch)"
              />
            </a>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
const moduleInfo = require('../../static/lib/moduleInfo.json');
import _ from 'lodash';

export default {
  props: ['module', 'name'],
  data() {
    return {
      img: {
        0: '<div class="status-code status-unknown"></div>',
        76: '<div class="status-code status-unknown"></div>',
        // github action failing
        79: '<div class="status-code status-failing"></div>',
        81: '<div class="status-code status-failing"></div>',
        // github action pass
        86: '<div class="status-code status-passing"></div>',
        // github action no status
        96: '<div class="status-code status-unknown"></div>',
        98: '<div class="status-code status-unknown"></div>',
        126: '<div class="status-code status-passing"></div>',
        149: '<div class="status-code status-unknown"></div>',
        156: '<div class="status-code status-passing"></div>',
        160: '<div class="status-code status-failing"></div>',
        nonMaster: '<div class="status-code status-nonMaster"></div>',
      },
      newRepos: moduleInfo,
    };
  },
  computed: {
    getModules() {
      return this.$store.getters.loadModules;
    },
  },
  methods: {
    camelName(name) {
      return _.camelCase(name);
    },
    async swapImg(id, branch) {
      let badge = await document.getElementById(id);
      // this uses the width of the svg image to determine what to swap it out with
      if (branch === 'master' || !branch) {
        badge.parentNode.innerHTML = await this.img[badge.naturalWidth];
      } else {
        badge.parentNode.innerHTML = await this.img['nonMaster'];
      }
    },
  },
};
</script>

<style lang="scss">
@import '../../assets/styles/statusTable.scss';

.landing-table-wrapper {
  width: 80%;
  min-width: 700px;
  margin: 0;
}

.landing-table {
  margin: 0;
}

.landing-table .version-header {
  width: 16.6%;
}

.landing-table .ci-header,
.landing-table .license-header,
.landing-table .node-header {
  width: 16.6%;
  text-align: center;
  font-weight: 900;
}

.landing-table tbody {
  border: 3px solid $dark-white;
}

.landing-table-row td {
  padding: 10px;
}

.landing-table-row .module-license,
.table-version {
  text-align: center !important;
}

.landing-table-row .status-badge {
  vertical-align: middle;
}

@media (prefers-color-scheme: dark) {
  .landing-table tbody {
    border-color: $blackest !important;
  }
}
</style>
