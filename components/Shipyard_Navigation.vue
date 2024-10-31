<template>
  <section
    v-if="navigation"
    id="header-navigation"
    :class="headerNavigationClasses">

    <div class="grid-noGutter">
      <div :class="['modal-background', { 'show-background': navOpen, 'transition-out': modalClosing }]"></div>

      <div class="col">
        <div class="navigation-content">

          <a :href="navigation.index.href" tabindex="0" class="logo-link focus-visible">
            <Shipyard_Logo id="site-logo" />
          </a>

          <div
            :class="['hamburger-icon', 'focus-visible', {'close-icon' : navOpen}]"
            tabindex="0"
            @click="toggleNav"
            @keyup.enter="toggleNav">
          </div>

          <div :class="['navigation', { 'modal-open' : navOpen, 'transition-out': modalClosing }]">
            <div class="links-container">
              <div v-for="(link, index) in navigation.header" :key="index" class="navigation-link-item">
                <component
                  :is="link.type"
                  v-if="!link.links"
                  :to="link.disabled ? '' : link.href"
                  :href="link.disabled ? '' : link.href"
                  :disabled="link.disabled"
                  :target="link.target"
                  class="navigation-link onhover-line focus-visible">
                  {{ link.label }}
                </component>
                <div v-show="link.links" class="navigation-dropdown">
                  <Shipyard_NavigationDropdown :items="link">
                    <template #dropdown-icon>
                      <Shipyard_ChevronIcon />
                    </template>
                  </Shipyard_NavigationDropdown>
                </div>
              </div>
            </div>
            <div :class="['social-icon-container', { 'visible': navOpen }]">
              <Shipyard_SocialIcons />
            </div>
          </div>

        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { mapGetters } from 'vuex';
import Throttle from 'lodash/throttle';

const checkScreenWidth = (instance) => {
  if (!window.matchMedia('(max-width: 768px)').matches && instance.navOpen) {
    instance.toggleNav();
  }
};

export default {
  name: 'ShipyardNavigation',

  data() {
    return {
      navOpen: false,
      resize: false,
      scroll: false,
      modalClosing: false,
      scrollPosition: 0,
      showBackground: false,
      forceNavigationVisible: true,
    };
  },

  computed: {
    ...mapGetters({
      navigation: 'global/navigation',
      filterPanelOpen: 'filters/filterPanelOpen',
    }),
    headerNavigationClasses() {
      const showBackground = this.showBackground;
      const forceVisible = this.forceNavigationVisible;
      let compiled = '';
      if (forceVisible) compiled += 'force-visible ';
      if (showBackground) compiled += 'show-background ';
      return compiled;
    },
  },

  watch: {
    scrollPosition(newVal, oldVal) {
      const showBackground = this.showBackground;
      const forceVisible = this.forceNavigationVisible;

      if (newVal === 0 && showBackground) {
        this.showBackground = false;
      } else if (newVal > 0 && !showBackground) {
        this.showBackground = true;
      }

      if (newVal === 0) {
        this.forceNavigationVisible = true;
      } else if (newVal < oldVal && !forceVisible) {
        this.forceNavigationVisible = true;
      } else if (newVal > 80 && newVal > oldVal && forceVisible) {
        this.forceNavigationVisible = false;
      }
    },
  },

  mounted() {
    this.resize = Throttle(() => {
      checkScreenWidth(this);
    }, 310);
    this.scroll = () => {
      this.updateScrollPosition();
    };
    window.addEventListener('resize', this.resize);
    window.addEventListener('scroll', this.scroll);
    this.updateScrollPosition();
  },

  beforeDestroy() {
    if (this.resize) window.removeEventListener('resize', this.resize);
    if (this.scroll) window.removeEventListener('scroll', this.scroll);
  },

  methods: {
    toggleNav() {
      if (this.navOpen) {
        this.modalClosing = true;
        setTimeout(() => {
          this.modalClosing = false;
          document.body.classList.remove('no-scroll');
          this.navOpen = !this.navOpen;
        }, 300);
      } else {
        document.body.classList.add('no-scroll');
        this.navOpen = !this.navOpen;
      }
    },
    updateScrollPosition() {
      this.scrollPosition = window.scrollY;
    },
  },
};
</script>

<style lang="scss" scoped>
// ///////////////////////////////////////////////////////////////////// General
#header-navigation {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: $navigationHeight;
  z-index: 9999;
  transform: translateY(-$navigationHeight);
  transition: transform 250ms ease-in-out, background 0.3s ease;
  background: $pinkBlueGradient; // Apply gradient to header
  &.force-visible {
    transform: translateY(0);
  }
  &.show-background {
    background: $pinkBlueGradient; // Keep gradient background when scrolled
  }
}

[class*="grid"],
[class*="col"],
.navigation-content {
  height: 100%;
}

.navigation-content {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

#site-logo,
.navigation-link {
  cursor: pointer;
}

.logo-link {
  @include borderRadius_Medium;
}

#site-logo {
  display: block;
  position: relative;
  width: 8rem;
  opacity: 1.0;
  z-index: 100;
  transition: opacity 0.3s cubic-bezier(0.4, 0.0, 0.2, 1.0);
  &:hover {
    opacity: 0.75;
  }
}

// Navigation link styling
.navigation-link {
  color: #FF5CAC; // Pink color for links
  font-weight: 500;
  transition: color 0.3s;
  &:hover {
    color: #4F76FF; // Blue on hover
    text-decoration: underline;
  }
}

// Modal background for mobile navigation
.modal-background {
  display: none;
  background: rgba(0, 0, 0, 0.5); // Dark overlay for contrast on gradient
  @include customMaxMQ(768px) {
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
    z-index: 99;
  }
  &.show-background {
    display: inline;
  }
}

.hamburger-icon {
  display: none;
  position: relative;
  z-index: 1000;
  height: 14px;
  width: 2rem;
  @include customMaxMQ(768px) {
    display: inline;
  }
  &:before,
  &:after {
    content: '';
    position: absolute;
    width: 100%;
    border-top: 2px solid #ffffff; // White color for hamburger lines
    transition: transform 300ms cubic-bezier(0.4, 0.0, 0.2, 1.0);
  }
  &:before {
    top: 0;
  }
  &:after {
    bottom: 0;
  }
  &.close-icon:before {
    transform: rotate(45deg) translate(3px, 4px);
  }
  &.close-icon:after {
    transform: rotate(-45deg) translate(3px, -4px);
  }
}

// Social icon container for mobile
.social-icon-container {
  display: none;
  &.visible {
    @include customMaxMQ(768px) {
      display: inline;
      margin: 2rem 0 0 5rem;
    }
  }
}
</style>
