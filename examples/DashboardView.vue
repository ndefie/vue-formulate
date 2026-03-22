<template>
  <div class="dashboard">
    <header class="dashboard__header">
      <h1 class="dashboard__title">Vue Formulate</h1>
      <p class="dashboard__subtitle">The easiest way to build forms with Vue</p>
    </header>

    <section class="dashboard__section">
      <h2 class="dashboard__section-title">Quick Actions</h2>
      <div class="dashboard__actions">
        <button
          v-for="action in quickActions"
          :key="action.id"
          class="dashboard__action-card"
          @click="$emit('navigate', action.view)"
        >
          <span class="dashboard__action-icon">{{ action.icon }}</span>
          <span class="dashboard__action-label">{{ action.label }}</span>
          <span class="dashboard__action-desc">{{ action.description }}</span>
        </button>
      </div>
    </section>

    <section class="dashboard__section">
      <h2 class="dashboard__section-title">Try a Form</h2>
      <div class="dashboard__demo">
        <FormulateForm
          v-model="formValues"
          class="dashboard__form"
          @submit="handleSubmit"
        >
          <FormulateInput
            name="name"
            type="text"
            label="Full Name"
            placeholder="Jane Doe"
            validation="required"
          />
          <FormulateInput
            name="email"
            type="email"
            label="Email Address"
            placeholder="jane@example.com"
            validation="required|email"
          />
          <FormulateInput
            name="role"
            type="select"
            label="Role"
            :options="roleOptions"
            validation="required"
          />
          <FormulateInput
            name="message"
            type="textarea"
            label="Message"
            placeholder="Tell us about your project..."
            help="Optional. Keep it brief."
          />
          <FormulateInput
            type="submit"
            label="Submit"
          />
        </FormulateForm>

        <div
          v-if="submitted"
          class="dashboard__submission"
        >
          <h3>Submitted values:</h3>
          <pre>{{ JSON.stringify(submittedValues, null, 2) }}</pre>
          <button
            class="dashboard__reset-btn"
            @click="resetForm"
          >Reset</button>
        </div>
      </div>
    </section>

    <section class="dashboard__section">
      <h2 class="dashboard__section-title">Input Types at a Glance</h2>
      <div class="dashboard__stats">
        <div
          v-for="stat in inputStats"
          :key="stat.label"
          class="dashboard__stat-card"
        >
          <span class="dashboard__stat-count">{{ stat.count }}</span>
          <span class="dashboard__stat-label">{{ stat.label }}</span>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'DashboardView',
  data () {
    return {
      formValues: {},
      submitted: false,
      submittedValues: null,
      roleOptions: [
        { value: '', label: '-- Select a role --' },
        { value: 'developer', label: 'Developer' },
        { value: 'designer', label: 'Designer' },
        { value: 'manager', label: 'Project Manager' },
        { value: 'other', label: 'Other' }
      ],
      quickActions: [
        {
          id: 'text',
          icon: '✏️',
          label: 'Text Inputs',
          description: 'text, email, password, url, number & more',
          view: 'text'
        },
        {
          id: 'textarea',
          icon: '📝',
          label: 'Textarea',
          description: 'Multi-line text input fields',
          view: 'textarea'
        },
        {
          id: 'select',
          icon: '▼',
          label: 'Select',
          description: 'Dropdown and multi-select fields',
          view: 'select'
        },
        {
          id: 'box',
          icon: '☑',
          label: 'Checkboxes & Radios',
          description: 'Boolean and option-group inputs',
          view: 'box'
        },
        {
          id: 'file',
          icon: '📎',
          label: 'File Upload',
          description: 'Single and multi-file upload inputs',
          view: 'file'
        },
        {
          id: 'slider',
          icon: '⟷',
          label: 'Slider',
          description: 'Range/slider inputs',
          view: 'slider'
        },
        {
          id: 'button',
          icon: '▶',
          label: 'Buttons',
          description: 'Submit and action buttons',
          view: 'button'
        },
        {
          id: 'group',
          icon: '⊞',
          label: 'Groups',
          description: 'Repeatable field groups',
          view: 'group'
        }
      ],
      inputStats: [
        { count: '13+', label: 'Text variants' },
        { count: '4', label: 'Box types' },
        { count: '6', label: 'Group modes' },
        { count: '30+', label: 'Validation rules' }
      ]
    }
  },
  methods: {
    handleSubmit (values) {
      this.submittedValues = values
      this.submitted = true
    },
    resetForm () {
      this.formValues = {}
      this.submitted = false
      this.submittedValues = null
    }
  }
}
</script>

<style lang="scss">
@import '../themes/snow/snow.scss';

.dashboard {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2em 1em;

  @media (min-width: 756px) {
    padding: 2em;
  }

  &__header {
    text-align: center;
    padding: 2em 0 1em;
    border-bottom: 2px solid $formulate-green;
    margin-bottom: 2em;
  }

  &__title {
    font-size: 2.5em;
    color: $formulate-green;
    margin: 0 0 0.25em;
  }

  &__subtitle {
    color: $formulate-gray-ddd;
    font-size: 1.1em;
    margin: 0;
  }

  &__section {
    margin-bottom: 3em;

    &-title {
      font-size: 1.25em;
      color: $formulate-gray-ddd;
      border-bottom: 1px solid $formulate-gray;
      padding-bottom: 0.5em;
      margin-bottom: 1.25em;
    }
  }

  &__actions {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1em;
  }

  &__action-card {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    background: #fff;
    border: 1px solid $formulate-gray;
    border-radius: 6px;
    padding: 1.25em;
    cursor: pointer;
    text-align: left;
    transition: border-color 0.2s, box-shadow 0.2s;

    &:hover {
      border-color: $formulate-green;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
    }
  }

  &__action-icon {
    font-size: 1.75em;
    margin-bottom: 0.5em;
  }

  &__action-label {
    font-weight: 600;
    font-size: 0.95em;
    color: $formulate-gray-ddd;
    margin-bottom: 0.25em;
  }

  &__action-desc {
    font-size: 0.8em;
    color: lighten($formulate-gray-ddd, 20%);
    line-height: 1.4;
  }

  &__demo {
    display: grid;
    gap: 2em;

    @media (min-width: 756px) {
      grid-template-columns: 1fr 1fr;
    }
  }

  &__form {
    background: #fff;
    border: 1px solid $formulate-gray;
    border-radius: 6px;
    padding: 1.5em;
  }

  &__submission {
    background: $formulate-gray;
    border-radius: 6px;
    padding: 1.5em;

    h3 {
      margin: 0 0 0.75em;
      color: $formulate-gray-ddd;
      font-size: 1em;
    }

    pre {
      background: #fff;
      border: 1px solid darken($formulate-gray, 10%);
      border-radius: 4px;
      padding: 1em;
      overflow: auto;
      font-size: 0.85em;
      margin: 0 0 1em;
    }
  }

  &__reset-btn {
    background: $formulate-green;
    color: #fff;
    border: none;
    border-radius: 4px;
    padding: 0.5em 1em;
    cursor: pointer;
    font-size: 0.9em;

    &:hover {
      background: darken($formulate-green, 10%);
    }
  }

  &__stats {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 1em;
  }

  &__stat-card {
    background: $formulate-green;
    color: #fff;
    border-radius: 6px;
    padding: 1.5em 1em;
    text-align: center;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5em;
  }

  &__stat-count {
    font-size: 2em;
    font-weight: 700;
    line-height: 1;
  }

  &__stat-label {
    font-size: 0.85em;
    opacity: 0.9;
  }
}
</style>
