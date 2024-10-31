<template>
  <div v-if="featured.length > 0" id="featured-projects-slider">
    <div id="slider">
      <div
        id="card-row-container"
        ref="cardRowContainer"
        v-hammer:swipe.horizontal="onSwipe">
        <div
          id="card-row"
          ref="cardRow"
          :class="{ sliding: animate }"
          :style="{ left: `${left}px`, width: slidingRowWidth }">

          <div
            v-for="(project, index) in featured"
            :key="index"
            :style="{ width: `${cardWidth}px` }"
            class="click-wrapper"
            @click="projectCardClicked(project)">
            <Shipyard_ProjectCard
              :title="project.name"
              :slug="project.slug"
              :description="project.description.short"
              :logo="project.logo.icon"
              :url="project.primaryCta.url"
              :navigation-behavior="projectCardBehavior"
              :enable-image-alt="enableImageAlt"
              format="block-view" />
          </div>
        </div>
      </div>
    </div>

    <div id="slider-controls">
      <div id="slider-line">
        <div class="dummy-thumb" :style="`left: ${thumbPosition}px;`">
          <Shipyard_ChevronIcon class="chevron-left" />
          <Shipyard_ChevronIcon class="chevron-right" />
        </div>
        <input
          id="feature-range-slider"
          ref="sliderInput"
          v-model="range"
          type="range"
          step="0.1"
          class="focus-visible"
          :min="indices / 2"
          :max="indices * indices + 1">
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

const handleFeatureSliderResize = (instance) => {
  const display = instance.display
  if (window.matchMedia('(max-width: 25.9375rem)').matches) {
    if (display !== 1) { instance.display = 1 }
  } else if (window.matchMedia('(max-width: 40rem)').matches) {
    if (display !== 2) { instance.display = 2 }
  } else if (window.matchMedia('(max-width: 53.125rem)').matches) {
    if (display !== 3) { instance.display = 3 }
  } else {
    if (display !== 4) { instance.display = 4 }
  }
  if (instance.currentIndex > instance.indices) {
    instance.currentIndex = instance.indices
  }
  const cardWidth = instance.$refs.cardRowContainer.clientWidth / instance.display
  instance.animate = false
  instance.cardWidth = cardWidth
  instance.slidingRowWidth = cardWidth * instance.featured.length + 'px'
  instance.setSliderPosition()

  instance.inputWidth = instance.$refs.sliderInput.getBoundingClientRect().width
}

export default {
  name: 'ShipyardFeaturedProjectsSlider',
  props: {
    parent: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      currentIndex: 0,
      range: 0,
      resize: false,
      animate: true,
      left: 0,
      cardWidth: 0,
      display: 4,
      slidingRowWidth: '100%',
      inputWidth: false
    }
  },
  computed: {
    ...mapGetters({
      projects: 'projects/projects',
      settings: 'global/settings'
    }),
    featured () {
      return this.projects.filter(project => project.featured)
    },
    indices () {
      return this.featured.length - this.display
    },
    projectCardBehavior () {
      return parseInt(this.settings.visibility.disableSingulars)
    },
    enableImageAlt () {
      return this.settings.visibility.mediaAltAtts
    },
    thumbPosition () {
      const pos = (this.range - (this.indices / 2)) / ((this.indices * this.indices + 1) - (this.indices / 2))
      return Math.max(pos * (this.inputWidth - 48), 0)
    }
  },
  watch: {
    range (val) {
      this.animate = true
      const index = Math.trunc((val - (val % this.indices)) / this.indices)
      this.currentIndex = Math.max(0, Math.min(index, this.indices))
      this.setSliderPosition()
    }
  },
  mounted () {
    handleFeatureSliderResize(this)
    this.resize = () => { handleFeatureSliderResize(this) }
    window.addEventListener('resize', this.resize)
    this.$nextTick(() => { this.$emit('init') })
  },
  beforeDestroy () {
    if (this.resize) { window.removeEventListener('resize', this.resize) }
  },
  methods: {
    setSliderPosition () {
      this.left = (-1 * this.currentIndex) * this.cardWidth
    },
    onSwipe (e) {
      const ind = this.currentIndex
      const min = parseFloat(this.$refs.sliderInput.min)
      const max = parseFloat(this.$refs.sliderInput.max)
      const rng = max - min
      if (ind < this.indices && e.type === 'swipeleft') {
        this.range = min + (rng * (ind + 1) / this.indices)
      }
      if (ind > 0 && e.type === 'swiperight') {
        this.range = min + (rng * (ind - 1) / this.indices)
      }
    },
    projectCardClicked (project) {
      if (this.$Countly) {
        this.$Countly.trackEvent('Featured Slider | Project Card Clicked', {
          name: project.name,
          slug: project.slug,
          from: this.parent
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
// General Styles for Slider
#slider {
  margin: 0 $containerSingleColumn;
  overflow: hidden;
  background: $pinkBlueGradient; // Apply gradient background to slider
  padding: 1rem;
  border-radius: 12px;
  @include medium {
    margin: 0;
  }
}

#card-row {
  display: flex;
  flex-flow: row nowrap;
  align-items: flex-start;
  box-sizing: border-box;
  position: relative;
  left: 0px;
}

#card-row-container {
  width: 100%;
}

.sliding {
  transition: left 300ms ease-in-out;
}

// Card Styling
::v-deep .project-card {
  background: rgba(255, 255, 255, 0.1); // Semi-transparent on gradient
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  padding: 1rem;
  transition: transform 0.3s, box-shadow 0.3s;
  &:hover {
    transform: scale(1.05);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
  }
}

// Slider Controls
#slider-controls {
  display: flex;
  justify-content: center;
  margin: 1.5rem auto;
}

#feature-range-slider {
  width: 100%;
  appearance: none;
  height: 4px;
  background: rgba(255, 255, 255, 0.3); // Track color
  border-radius: 4px;
  outline: none;
  &::-webkit-slider-thumb {
    appearance: none;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: $pinkBlueGradient; // Gradient thumb
    cursor: pointer;
    transition: background 0.3s;
  }
}

// Slider Line
#slider-line {
  position: relative;
  display: block;
  width: 40%;
  @include tiny {
    width: 75%;
  }
  &:before {
    content: '';
    position: absolute;
    top: 50%;
    left: 0;
    width: 100%;
    transform: translateY(-50%);
    height: 1px;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.5);
  }
}

.dummy-thumb {
  position: absolute;
  background: #ffffff;
  transform: translateY(-50%);
  height: 21px;
  width: 53px;
  top: calc(50% + 1px);
  border: 2.5px solid #C6C8C7;
  border-radius: 6px;
}
</style>
