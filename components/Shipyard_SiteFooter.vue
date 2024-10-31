<template>
  <footer v-if="pageData" id="site-footer">
    <section class="panel-top">
      <div class="grid-spaceBetween-noGutter">
        <div class="col-5_sm-12">
          <div class="flex-shrink lg:max-w-sm xl:max-w-xl mb-4 lg:mb-0">
            <h2 class="heading">Stay Informed</h2>
            <p class="subheading">
              <a target="_blank" href="https://ipfs.fyi/newsletter">Sign up</a> for the Ink Chain newsletter for
              the latest on releases, upcoming developments, community events, and more.
            </p>
          </div>
        </div>

        <div class="col-5_sm-12" data-push-left="off-1_sm-0">
          <div class="project-add-inner">
            <h2 class="heading">Add Your Project</h2>
            <div class="subheading">
              Don’t see your project here?
              <a href="https://airtable.com/shrjwvk9pAeAk0Ci7" target="_blank">Send it our way</a>.
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="panel-bottom">
      <div class="grid-spaceBetween-noGutter">
        <div v-if="navigation" class="col-7_sm-12">
          <nav id="footer-navigation">
            <component
              :is="link.type"
              v-for="(link, index) in navigation.footer"
              :key="index"
              :to="link.disabled ? '' : link.href"
              :href="link.disabled ? '' : link.href"
              :disabled="link.disabled"
              :target="link.target"
              class="navigation-link onhover-opacity focus-visible">
              {{ link.label }}
            </component>
          </nav>
        </div>

        <div class="col-4_sm-12">
          <Shipyard_SocialIcons />
        </div>

        <div class="col-12">
          <div ref="copyright" class="copyright">
            <template v-if="pageData.copyright.length">
              <Shipyard_CopyrightLogo />
              <component
                :is="getElementTag(element.type)"
                v-for="(element, index) in pageData.copyright"
                :key="`copyright-element-${index}`"
                :href="element.href"
                target="_blank"
                class="focus-visible">{{ element.content ? element.content : '' }}</component>
            </template>
          </div>
        </div>
      </div>
    </section>
  </footer>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
  name: 'ShipyardSiteFooter',
  computed: {
    ...mapGetters({
      siteContent: 'global/siteContent',
      navigation: 'global/navigation'
    }),
    pageData() {
      const siteContent = this.siteContent;
      if (siteContent.hasOwnProperty('general')) {
        return siteContent.general.footer_content;
      }
      return false;
    }
  },
  methods: {
    getElementTag(string) {
      switch (string) {
        case 'text':
          return 'span';
        case 'link':
          return 'a';
        default:
          return 'span';
      }
    }
  }
};
</script>

<style lang="scss" scoped>
// Footer Background and General Styles
#site-footer {
  background: $pinkBlueGradient; // Apply gradient to footer background
  color: #ffffff;
  padding: 3rem 1rem;
  @include small {
    padding: 2rem 1rem;
  }
}

// Panel Top Styling
.panel-top {
  margin-bottom: 3rem;
  color: #ffffff;
  @include small {
    margin-bottom: 1rem;
  }
}

.heading {
  font-size: 1.75rem;
  font-weight: 600;
  color: #ffffff; // White for headings
}

.subheading {
  color: #f1f3f2; // Light text for subheadings on gradient
  a {
    color: #FF5CAC; // Pink for links in subheading
    &:hover {
      color: #4F76FF; // Blue on hover
    }
  }
}

// Footer Navigation Links
#footer-navigation {
  a {
    color: #FF5CAC; // Pink for footer links
    font-weight: 500;
    transition: color 0.3s;
    &:hover {
      color: #4F76FF; // Blue on hover
      text-decoration: underline;
    }
  }
}

// Social Icons Container
.social-icons-container {
  @include small {
    margin: 1.5rem 0;
  }
}

// Copyright Section
.copyright {
  font-size: 0.875rem;
  color: #f1f3f2; // Light color for copyright text
  svg {
    display: inline-block;
    vertical-align: middle;
    width: 1rem;
    margin-right: 0.25rem;
  }
}
</style>
