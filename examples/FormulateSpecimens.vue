<template>
  <div
    v-if="testKey"
    id="app"
  >
    <FormulateForm
      :key="testKey"
      ref="testForm"
      name="testForm"
      @submit="submission"
    >
      <div
        class="proving-ground"
      >
        <div class="proving-ground-stage">
          <component
            :is="test.component"
            v-model="provingGroundValue"
            v-bind="test.props"
            v-on="test.props.listeners || {}"
          />
        </div>
        <pre class="proving-ground-values">{{ provingGroundValue }}</pre>
      </div>
    </FormulateForm>
  </div>
  <div
    v-else
    id="app"
  >
    <nav class="app-nav">
      <div class="app-nav__brand">Vue Formulate</div>
      <div class="app-nav__links">
        <button
          class="app-nav__link"
          :class="{ 'app-nav__link--active': activeView === 'dashboard' }"
          @click="activeView = 'dashboard'"
        >
          Dashboard
        </button>
        <button
          class="app-nav__link"
          :class="{ 'app-nav__link--active': activeView === 'specimens' }"
          @click="activeView = 'specimens'"
        >
          All Specimens
        </button>
        <button
          v-for="spec in specimenViews"
          :key="spec.id"
          class="app-nav__link"
          :class="{ 'app-nav__link--active': activeView === spec.id }"
          @click="activeView = spec.id"
        >
          {{ spec.label }}
        </button>
      </div>
    </nav>

    <main class="app-main">
      <DashboardView
        v-if="activeView === 'dashboard'"
        @navigate="activeView = $event"
      />

      <div
        v-else-if="activeView === 'specimens'"
        class="specimen-list"
      >
        <SpecimenButton />
        <SpecimenBox />
        <SpecimenFile />
        <SpecimenGroup />
        <SpecimenSelect />
        <SpecimenSlider />
        <SpecimenText />
        <SpecimenTextarea />
      </div>

      <div
        v-else
        class="specimen-list"
      >
        <component :is="activeSpecimenComponent" />
      </div>
    </main>
  </div>
</template>

<script>
import { has } from '../src/libs/utils'
import nanoid from 'nanoid/non-secure'
import DashboardView from './DashboardView.vue'
import SpecimenText from './specimens/SpecimenText'
import SpecimenTextarea from './specimens/SpecimenTextarea'
import SpecimenGroup from './specimens/SpecimenGroup'
import SpecimenFile from './specimens/SpecimenFile'
import SpecimenButton from './specimens/SpecimenButton'
import SpecimenBox from './specimens/SpecimenBox'
import SpecimenSlider from './specimens/SpecimenSlider'
import SpecimenSelect from './specimens/SpecimenSelect'

export default {
  name: 'App',
  components: {
    DashboardView,
    SpecimenButton,
    SpecimenBox,
    SpecimenText,
    SpecimenTextarea,
    SpecimenGroup,
    SpecimenFile,
    SpecimenSlider,
    SpecimenSelect
  },
  data () {
    return {
      activeView: 'dashboard',
      testKey: false,
      provingGroundValue: null,
      provingGroundSubmissionResolver: () => {},
      specimenViews: [
        { id: 'text', label: 'Text', component: 'SpecimenText' },
        { id: 'textarea', label: 'Textarea', component: 'SpecimenTextarea' },
        { id: 'select', label: 'Select', component: 'SpecimenSelect' },
        { id: 'box', label: 'Boxes', component: 'SpecimenBox' },
        { id: 'file', label: 'File', component: 'SpecimenFile' },
        { id: 'slider', label: 'Slider', component: 'SpecimenSlider' },
        { id: 'button', label: 'Buttons', component: 'SpecimenButton' },
        { id: 'group', label: 'Groups', component: 'SpecimenGroup' }
      ]
    }
  },
  computed: {
    activeSpecimenComponent () {
      const found = this.specimenViews.find(s => s.id === this.activeView)
      return found ? found.component : null
    }
  },
  mounted () {
    window.showTest = this.showTest.bind(this)
    window.getInputValue = this.inputValue.bind(this)
    window.getSubmittedValue = this.submittedValue.bind(this)
    window.submitForm = this.submitForm.bind(this)
    window.getVueInstance = () => this
  },
  methods: {
    showTest (data) {
      if (data.component) {
        this.testKey = nanoid(5)
        this.test = data
        if (has(data, 'value')) {
          this.provingGroundValue = data.value
        }
      } else {
        this.testKey = false
      }
    },
    inputValue () {
      return this.provingGroundValue
    },
    submission (data) {
      this.provingGroundSubmissionResolver(data)
    },
    submittedValue () {
      return new Promise(resolve => {
        this.provingGroundSubmissionResolver = resolve
        this.submitForm()
      })
    },
    submitForm () {
      this.$refs.testForm.formSubmitted()
    }
  }
}
</script>

<style lang="scss">
@import '../themes/snow/snow.scss';

body {
  margin: 0;
  padding: 0;
  font-family: $formulate-font-stack;
  background-color: #f7f8fa;
}

/* Navigation */
.app-nav {
  position: sticky;
  top: 0;
  z-index: 100;
  display: flex;
  align-items: center;
  background: #fff;
  border-bottom: 1px solid $formulate-gray;
  padding: 0 1em;
  min-height: 52px;
  overflow-x: auto;

  &__brand {
    font-weight: 700;
    color: $formulate-green;
    font-size: 1em;
    white-space: nowrap;
    margin-right: 1.5em;
    flex-shrink: 0;
  }

  &__links {
    display: flex;
    gap: 0.25em;
    align-items: center;
  }

  &__link {
    background: none;
    border: none;
    padding: 0.5em 0.75em;
    cursor: pointer;
    font-size: 0.85em;
    color: $formulate-gray-dark;
    border-radius: 4px;
    white-space: nowrap;
    transition: background-color 0.15s, color 0.15s;

    &:hover {
      background-color: $formulate-gray;
    }

    &--active {
      color: $formulate-green;
      font-weight: 600;
      background-color: lighten($formulate-green, 55%);
    }
  }
}

.app-main {
  min-height: calc(100vh - 52px);
}

h2 {
  display: block;
  width: 100%;
  position: sticky;
  top: 52px;
  background-color: white;
  padding: .5em 0;
  color: $formulate-green;
  border-bottom: 1px solid $formulate-gray;
  margin: 2em 0 0 0;
  z-index: 10;
}

.specimen-list {
  padding: 1em;
  max-width: 1200px;
  margin: 0 auto;
  @media (min-width: 756px) {
    padding: 2em;
  }
}

.specimens {
  @media (min-width: 500px) {
    display: flex;
    justify-content: flex-start;
    flex-wrap: wrap;
  }
  &:last-child {
    border-bottom: 0;
  }
}

.specimen {
  max-width: 400px;
  padding: 1em;
  box-sizing: border-box;

  @media (min-width: 500px) {
    width: 50%;
    border-bottom: 1px solid $formulate-gray;
    &:nth-of-type(odd) {
      border-right: 1px solid $formulate-gray;
    }
  }

  @media (min-width: 900px) {
    width: 33.332%;
    border-right: 1px solid $formulate-gray;

    &:nth-of-type(3n) {
      border-right: 0;
    }
  }
}

.proving-ground {
  box-sizing: border-box;
  display: flex;

  &-stage,
  &-values {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &-stage {
    flex: 0 0 50%;
    width: 50%;
    & > * {
      width: 300px;
    }
  }
  &-values {
    flex: 0 0 50%;
    width: 50%;
    background-color: $formulate-gray;
    margin: 0;
  }
}
</style>
