<template>
  <header>
    <nav role="navigation" class="navbar">
      <div class="nav-container">
        <a class="logo" href="/" title="Back to home" aria-label="hapi-logo" />
        <div class="nav-collapse">
          <ul class="nav-links">
            <li class="nav-links-li">
              <a
                ref="api"
                :class="
                  getDisplay === 'api' ? 'nav-link nav-link-active' : 'nav-link'
                "
                title="API"
                href="/api"
                @click="addActive('api')"
                >API</a
              >
            </li>
            <li class="nav-links-li">
              <a
                ref="resources"
                :class="
                  getDisplay === 'resources'
                    ? 'nav-link nav-link-active'
                    : 'nav-link'
                "
                title="Resources"
                href="/resources/changelog"
                @click="addActive('resources')"
                >Resources</a
              >
            </li>
            <li class="nav-links-li">
              <a
                ref="policies"
                :class="
                  getDisplay === 'policies'
                    ? 'nav-link nav-link-active'
                    : 'nav-link'
                "
                title="Policies"
                href="/policies/coc"
                @click="addActive('policies')"
                >Policies</a
              >
            </li>
            <li class="nav-links-li">
              <a
                ref="family"
                :class="
                  getDisplay === 'family'
                    ? 'nav-link nav-link-active'
                    : 'nav-link'
                "
                title="Modules"
                href="/module?sort=name"
                @click="addActive('family')"
                >Modules</a
              >
            </li>
            <li class="nav-links-li">
              <a
                ref="tester"
                :class="
                  getDisplay === 'tester'
                    ? 'nav-link nav-link-active'
                    : 'nav-link'
                "
                title="Schema Tester"
                href="/tester"
                @click="addActive('tester')"
                >Sandbox</a
              >
            </li>
          </ul>
        </div>
        <a
          ref="hamburger"
          href="javascript:void(0);"
          class="hamburger"
          aria-label="hamburger"
          @click="showNav()"
        >
          <span class="hamburger-line hamburger-line-upper" />
          <span class="hamburger-line hamburger-line-middle" />
          <span class="hamburger-line hamburger-line-lower" />
        </a>
        <img
          ref="mobileClose"
          src="/img/close.png"
          class="mobile-close hide"
          alt="close"
          @click="closeNav()"
        />
      </div>
      <div ref="mobileNav" class="mobile-nav-wrapper">
        <div ref="overlay" class="mobile-overlay" @click="closeNav()"></div>
        <MobileNav />
      </div>
    </nav>
  </header>
</template>

<script>
import MobileNav from './MobileNav.vue';

export default {
  components: {
    MobileNav,
  },
  computed: {
    getDisplay() {
      return this.$store.getters.loadDisplay;
    },
  },
  methods: {
    showNav() {
      this.$refs.hamburger.classList.add('hide');
      this.$refs.mobileClose.classList.remove('hide');
      this.$refs.mobileNav.classList.add('show-nav');
      this.$refs.overlay.classList.add('show-nav');
      let body = document.body;
      body.classList.add('no-scroll');
    },
    closeNav() {
      this.$refs.mobileNav.classList.remove('show-nav');
      this.$refs.hamburger.classList.remove('hide');
      this.$refs.mobileClose.classList.add('hide');
      this.$refs.overlay.classList.remove('show-nav');
      let visible = document.querySelector('.visible');
      let minus = document.querySelectorAll('.mobile-family-minus');
      let display = document.querySelectorAll('.nav-display');
      for (let d of display) {
        d.classList.remove('nav-display');
      }
      for (let m of minus) {
        m.classList.remove('mobile-family-minus');
        m.classList.add('mobile-family-plus');
      }
      if (visible) {
        visible.classList.remove('visible');
        visible.classList.add('hide');
      }
      let body = document.body;
      body.classList.remove('no-scroll');
    },
    addActive(ref) {
      this.$store.commit('setDisplay', ref);
    },
  },
};
</script>

<style lang="scss">
@import '../../assets/styles/variables.scss';

.navbar {
  position: relative;
  width: 100%;
  margin: 0 auto;
  padding: 10px 50px;
  background: $white;
}

.nav-container {
  position: relative;
  height: 100%;
  top: 0;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.mobile-nav-wrapper {
  display: none;
  transition: all 0.5s ease-in-out;
}

.mobile-overlay {
  position: fixed;
  width: 100vw;
  height: 100vh;
  left: -100%;
  z-index: 10;
}

.nav-collapse {
  margin: 0;
}

.nav-links {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.nav-links-li {
  display: inline-block;
  margin: 0;
}

.nav-link {
  position: relative;
  margin: 10px;
  color: $orange;
  font-size: 1.1em;
  font-weight: 900;
  box-sizing: border-box;
  border-radius: 6px;
  padding: 5px 0;
  text-decoration: none;
  -webkit-transition: 0.2s linear;
  transition: 0.2s linear;
}

.nav-link:hover {
  color: $orange;
  text-decoration: none;
}

.nav-link:before,
.nav-link-active:before {
  width: 0;
  position: absolute;
  content: '';
  left: 0;
  right: 0;
  top: 0;
  height: 1px;
  background: $orange;
  -webkit-transition: 0.15s linear;
  transition: 0.15s linear;
}

.nav-link:after,
.nav-link-active:after {
  width: 0;
  position: absolute;
  content: '';
  left: 0;
  right: 0;
  bottom: 0;
  height: 1px;
  background: $orange;
  -webkit-transition: 0.15s linear;
  transition: 0.15s linear;
}

.nav-link:hover:after,
.nav-link:hover:before,
.nav-link-active:before,
.nav-link-active:after {
  width: 100%;
}

.hamburger {
  position: absolute;
  display: none;
}

.hamburger-line {
  display: block;
  margin: 5px 0;
  width: 20px;
  height: 2px;
  border-radius: 5px;
  background: $black;
}

.mobile-close {
  position: absolute;
  margin: 5px 0;
  width: 17px;
  height: 17px;
  cursor: pointer;
  margin: 0;
  left: 21px;
}

@media screen and (max-width: 900px) {
  .navbar {
    position: fixed;
    top: 0;
    height: 50px;
    width: 100%;
    max-width: 100vw;
    padding: 5px 0;
    border-bottom: 1px solid $dark-white;
  }

  .nav-container {
    justify-content: center;
  }

  .nav-collapse {
    display: none;
  }

  .mobile-nav-wrapper {
    margin: 0;
    display: block;
    position: absolute;
    top: 50px;
    left: -100%;
    width: 100%;
    max-width: 500px;
    height: 100vh;
    background: $off-white;
    z-index: 10;
    overflow-y: auto;
  }

  .show-nav {
    left: 0;
  }

  .no-scroll {
    overflow: hidden;
  }

  .hamburger {
    display: inline-block;
    left: 20px;
    top: 8px;
  }
}

@media print {
  .navbar {
    padding: 10px 0;
  }
  .nav-container {
    justify-content: flex-start;
  }

  .nav-collapse {
    display: none;
  }
}

@media (prefers-color-scheme: dark) {
  .navbar {
    background: $blacker !important;
    border-bottom: 1px solid $blacker !important;
  }

  #__layout .contain > header {
    background: $blacker;
  }
}
</style>
