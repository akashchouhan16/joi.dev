<template>
  <div ref="nav" class="mobile-nav">
    <div class="mobile-nav-flex-wrapper">
      <div class="mobile-nav-flex-left">
        <ul class="mobile-links">
          <li class="mobile-links-li">
            <a class="mobile-link" title="Home" href="/">Home</a>
          </li>
          <li class="mobile-links-li">
            <a class="mobile-link" title="API" href="/api">API</a>
          </li>
          <li class="mobile-links-li">
            <div
              class="mobile-link"
              title="Resources"
              @click="showMenu('resources')"
            >
              Resources
            </div>
          </li>
          <li class="mobile-links-li">
            <div
              class="mobile-link"
              title="Policies"
              href="/policies/coc"
              @click="showMenu('policies')"
            >
              Policies
            </div>
          </li>
          <li class="mobile-links-li">
            <div
              class="mobile-link"
              title="Family"
              href="/module"
              @click="showMenu('modules')"
            >
              Modules
            </div>
          </li>
        </ul>
      </div>
      <div class="mobile-nav-flex-right">
        <div class="mobile-nav-flex-right-background"></div>
        <div class="mobile-nav-right-content">
          <div id="mobile-resources" class="hide">
            <h5 class="mobile-content-header">Resources</h5>
            <ul class="mobile-content-ul">
              <li class="mobile-link mobile-tutorial-link">
                <a title="Changelog" href="/resources/changelog">Changelog</a>
              </li>
              <li class="mobile-link mobile-tutorial-link">
                <a title="Changelog" href="/resources/status">Module Status</a>
              </li>
            </ul>
          </div>
          <div id="mobile-policies" class="hide">
            <h5 class="mobile-content-header">Policies</h5>
            <ul class="mobile-content-ul">
              <li class="mobile-link mobile-tutorial-link">
                <a title="COC" href="/policies/coc">Code of Conduct</a>
              </li>
              <li class="mobile-link mobile-tutorial-link">
                <a title="COC" href="/policies/contributing">Contributing</a>
              </li>
              <li class="mobile-link mobile-tutorial-link">
                <a title="COC" href="/policies/license">License</a>
              </li>
              <li class="mobile-link mobile-tutorial-link">
                <a title="Security" href="/policies/security">Security</a>
              </li>
              <li class="mobile-link mobile-tutorial-link">
                <a title="Style Guide" href="/policies/styleguide"
                  >Style Guide</a
                >
              </li>
            </ul>
          </div>
          <div id="mobile-modules" class="hide">
            <h5 class="mobile-content-header">Modules</h5>
            <ul class="module-ul mobile-content-ul">
              <li
                v-for="name in getModules"
                :key="name"
                :name="name"
                class="mobile-link mobile-tutorial-link mobile-module-link"
                :title="name"
              >
                <div
                  :id="name + '-wrapper'"
                  :href="'/family/' + name"
                  class="mobile-family-link mobile-family-plus"
                  @click="triggerMenu(name)"
                >
                  {{ name }}
                </div>
                <ul :id="name + '2'" class="mobile-subul">
                  <li class="mobile-sublink">
                    <a :href="'/module/' + name">Home</a>
                  </li>
                  <li
                    v-if="moduleInfo[name].api === true"
                    class="mobile-sublink"
                  >
                    <a :href="'/module/' + name + '/api'">API</a>
                  </li>
                  <li class="mobile-sublink">
                    <a :href="'/module/' + name + '/changelog'">Changelog</a>
                  </li>
                </ul>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const page = require('../../static/lib/');
const moduleInfo = require('../../static/lib/moduleInfo.json');

export default {
  data() {
    return {
      moduleInfo: moduleInfo,
    };
  },
  computed: {
    getModules() {
      return this.$store.getters.loadModules;
    },
  },
  methods: {
    closeNav() {
      this.$refs.nav.parentNode.classList.remove('show-nav');
      let overlay = document.querySelector('.mobile-overlay');
      overlay.classList.remove('show-nav');
      let body = document.body;
      body.classList.remove('no-scroll');
    },
    triggerMenu(name) {
      let wrapper = document.querySelector('#' + name + '-wrapper');
      if (wrapper.classList.contains('mobile-family-plus')) {
        document.querySelector('#' + name + 2).classList.add('nav-display');
        wrapper.classList.add('mobile-family-minus');
        wrapper.classList.remove('mobile-family-plus');
      } else {
        document.querySelector('#' + name + 2).classList.remove('nav-display');
        wrapper.classList.remove('mobile-family-minus');
        wrapper.classList.add('mobile-family-plus');
      }
    },
    showMenu(name) {
      let visible = document.querySelector('.visible');
      if (visible) {
        visible.classList.remove('visible');
        visible.classList.add('hide');
      }
      let menu = document.querySelector('#mobile-' + name);
      menu.classList.add('visible');
      menu.classList.remove('hide');
    },
  },
};
</script>

<style lang="scss">
@import '../../assets/styles/variables.scss';
@import '../../assets/styles/markdown.scss';

.mobile-nav {
  position: relative;
  width: 100%;
  min-height: calc(100vh - 50px);
  z-index: 20;
  background: $off-white;
}

.mobile-hapi {
  height: 50px;
  margin: 0;
}

.mobile-nav-flex-wrapper {
  position: relative;
  display: flex;
  justify-content: space-between;
  min-height: calc(100vh - 50px);
}

.mobile-nav-flex-left {
  position: sticky;
  top: 0;
  width: 30%;
  align-self: flex-start;
  height: auto;
  overflow-y: auto;
  padding-bottom: 100px;
}

.mobile-nav-flex-right {
  position: relative;
  width: 70%;
  overflow-y: auto;
  padding: 0 0 50px;
  border-left: 1px solid $dark-white;
  background: white;
}

.mobile-nav-flex-right-background {
  position: sticky;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  margin: 0 auto;
  height: 100%;
  width: 200px;
  opacity: 0.1;
  background-image: url('../../static/img/helmet.png');
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
}

.mobile-nav-right-content {
  position: absolute;
  top: 0;
}

.mobile-content-header {
  font-size: 18px;
  font-weight: 700;
  color: $black;
  margin: 21px 0 0 0;
  padding-left: 10px;
  border-bottom: 1px solid $dark-white;
}

.mobile-content-ul {
  margin-top: 0;
  display: flex;
  flex-direction: column;
}

.mobile-family-link:after {
  content: '\002B';
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  font-size: 20px;
  top: 9px;
  bottom: 0;
  left: 0;
  height: 31px;
  width: 15px;
  z-index: 100;
}

.mobile-family-minus:after {
  content: '\2212';
  color: inherit;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  font-size: 20px;
  top: 9px;
  bottom: 0;
  left: 0;
  height: 31px;
  width: 15px;
  z-index: 100;
}

.mobile-links {
  list-style-type: none;
  margin: 0;
  padding: 0;
  display: block;
}

.mobile-links-li {
  display: block;
  padding: 10px 0;
  padding-right: 0px !important;
  margin: 0;
  text-align: center;
  border-bottom: 1px solid $dark-white;
}

.mobile-link {
  position: relative;
  display: inline;
  color: $orange;
  font-size: 16px;
  font-weight: 700;
  box-sizing: border-box;
  border-radius: 6px;
  padding: 10px 20px;
  margin: 0;
  text-decoration: none;
  -webkit-transition: 0.2s linear;
  transition: 0.2s linear;
}

.mobile-tutorial-link {
  font-size: 16px;
  list-style-type: none;
}

.module-ul {
  margin: 0 0 0 20px;
}

.mobile-subul {
  display: none;
  margin: 0 0 5px 5px;
}

.mobile-sublink {
  position: relative;
  color: $orange;
  font-weight: 400;
  box-sizing: border-box;
  border-radius: 6px;
  padding: 5px 5px 0 5px;
  margin: 0;
  text-decoration: none;
  -webkit-transition: 0.2s linear;
  transition: 0.2s linear;
  font-size: 14px;
  list-style-type: none;
}

.mobile-module-link {
  padding-bottom: 0;
}

.mobile-helmet {
  display: block;
  width: 35px;
  margin: 17.5px auto 17.5px auto;
}
</style>
