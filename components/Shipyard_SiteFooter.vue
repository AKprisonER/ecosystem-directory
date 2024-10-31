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
// General Styles
#site-footer {
  background: linear-gradient(180deg, #021C36, #041727); // Gradient background
  color: #e0e5ff;
  padding: 4rem 0;
  @include small {
    padding: 2rem 0;
  }
}

// Link Hover Effects
::v-deep .subheading a,
::v-deep .copyright a {
  text-decoration: underline transparent;
  color: #6bc4ce;
  transition: color 0.3s, text-decoration-color 0.3s;
  &:hover {
    color: #3d8f96;
    text-decoration-color: currentColor;
  }
}

// Top Panel Styles
.panel-top {
  margin-bottom: 4rem;
  @include small {
    margin-bottom: 1rem;
  }
}

.heading {
  font-size: 1.75rem;
  line-height: 1.2;
  font-weight: 600;
  color: #e0e5ff;
}

.subheading {
  margin-top: 0.5rem;
  margin-right: 0.5rem;
  color: #b3b3b3;
}

// Mailchimp Form
::v-deep #mailchimp-form {
  margin-top: 1rem;
  .panel-top {
    display: flex;
    flex-direction: row;
    margin-bottom: 0.5rem;
    @include mini {
      flex-direction: column;
      margin-bottom: 0.5rem;
    }
  }
  .panel-bottom {
    span {
      padding-left: 0.25rem;
    }
  }
  input {
    &[type="email"] {
      padding: 0.5rem;
      line-height: 1.5;
      flex: 1;
      @include borderRadius_Medium;
    }
    &[type="submit"] {
      padding: 0 1rem;
      font-weight: 600;
      background: linear-gradient(90deg, #6bc4ce, #3d8f96); // Gradient button
      color: #ffffff;
      transition: background 0.3s, transform 0.3s;
      @include mini {
        margin-top: 0.5rem;
        padding: 0.5rem;
      }
      &:hover {
        transform: scale(1.05); // Slight scale effect on hover
        background: #34797d; // Darker teal on hover
      }
    }
  }
}

// Bottom Panel Styles
#footer-navigation {
  margin-bottom: 2rem;
  a {
    color: #e0e5ff;
    margin-right: 1.6875rem;
    &:hover {
      color: #6bc4ce;
      text-decoration: underline;
    }
  }
}

.social-icons-container {
  @include small {
    margin: 2rem 0;
  }
}

::v-deep .copyright {
  color: #b3b3b3;
  font-size: 0.875rem;
  svg {
    display: inline-block;
    vertical-align: middle;
    width: 1rem;
    margin-right: 0.25rem;
  }
}
</style>
