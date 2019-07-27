<template>
  <div class="navigation-editor">
    <sortable-list
      v-if="items && items.length > 0"
      v-model="items"
      lock-axis="y">
      <sortable-item
        v-for="(item, index) in items"
        :index="index"
        :key="item.id">
        <div class="list-item-wrapper">
          <div class="list-item-input">
            <v-input
              :id="`title-${item.id}`"
              :value="item.title"
              type="text"
              icon-right-color=""
              placeholder="Link title"
              @input="(newValue) => updateItem(index, 'title', newValue)"
            />
          </div>

          <div class="list-item-input">
            <v-input
              :id="`link-${item.id}`"
              :value="item.link"
              type="text"
              icon-right-color=""
              placeholder="/url-path"
              @input="(newValue) => updateItem(index, 'link', newValue)"
            />
          </div>

          <div class="list-item-input">
            <v-select
              :value="item.target"
              :options="{
                'self': 'Same Tab',
                'new': 'New Tab'
              }"
              @input="(newValue) => updateItem(index, 'target', newValue)"
            />
          </div>

          <div class="list-item-delete">
            <input
              type="button"
              class="list-item-delete__button"
              @click="deleteItem(index)">
            <div class="list-item-delete__icon">
              <v-icon name="delete" />
            </div>
          </div>
        </div>
      </sortable-item>
    </sortable-list>
    <v-button
      class="add-new-link"
      type="button"
      @click="addNewItem">
      <span>
        <v-icon name="add" />
      </span>
      {{ $t("interfaces-navigation-editor-new_link") }}
    </v-button>
  </div>
</template>

<script>
import mixin from '@directus/extension-toolkit/mixins/interface'
import { ContainerMixin, ElementMixin, HandleDirective } from 'vue-slicksort'

const SortableList = {
  mixins: [ContainerMixin],
  render (createElement) {
    return createElement(
      'ul', {
        attrs: {
          'class': 'list'
        }
      },
      this.$slots.default
    )
  }
}

const SortableItem = {
  mixins: [ElementMixin],
  props: ['item'],
  directives: { handle: HandleDirective },
  render (createElement) {
    return createElement(
      'li', {
        attrs: {
          'class': 'list-item'
        }
      },
      [
        createElement('span', {
          attrs: {
            'class': 'list-handle'
          },
          directives: [
            {
              name: 'handle'
            }
          ]
        }),
        ...this.$slots.default
      ]

    )
  }
}

export default {
  components: {
    SortableList,
    SortableItem
  },
  mixins: [mixin],
  computed: {
    items: {
      get () {
        return this.value || []
      },
      set (newValue) {
        this.updateValue(newValue)
      }
    }
  },
  methods: {
    updateValue (value) {
      this.$emit(
        'input',
        value
      )
    },
    addNewItem () {
      const items = JSON.parse(JSON.stringify(this.items))

      items.push({
        title: 'New link',
        link: '/',
        target: 'self'
      })

      this.updateValue(items)
    },
    updateItem (index, field, value) {
      const items = JSON.parse(JSON.stringify(this.items))

      items[index][field] = value

      this.updateValue(items)
    },
    deleteItem (targetIndex) {
      const items = JSON.parse(JSON.stringify(this.items)).filter((item, index) => index !== targetIndex)
      this.updateValue(items)
    }
  }
}
</script>

<style lang="scss" scoped>
.navigation-editor {
  max-width: var(--width-medium);
}

.add-new-link {
  margin-top: 15px;
}

.list {
  list-style-type: none;
  margin: 0 auto;
  padding: 0;
  overflow: auto;
  background-color: var(--lightest-gray);
  border: var(--input-border-width) solid var(--lighter-gray);
  border-radius: var(--border-radius);
  width: 100%;
}

.list-item {
  display: flex;
  align-items: center;
  width: 100%;
  padding: 20px;
  background-color: var(--white);
  border-bottom: var(--input-border-width) solid var(--lighter-gray);
  box-sizing: border-box;
  user-select: none;
  -moz-user-select: none;
  color: var(--gray);

  &:last-of-type {
    border-bottom: 0;
  }
}

.list-item-image {
  width: 100%;
  max-width: 80px;
  margin-right: 10px;
}

.list-item-title {
  margin-right: 10px;
}

.list-item-wrapper {
  width: 100%;
  display: flex;
  align-items: start;
  justify-content: start;

  @media (max-width: 580px) {
    flex-direction: column;
  }
}

.list-item-input {
  margin-right: 10px;

  &:last-of-type {
    margin-right: 0;
    margin-left: auto;
  }
}

.list-item-delete {
  margin: auto;
  position: relative;

  &__button {
    position: absolute;
    cursor: pointer;
    color: var(--danger);
    margin: 0;
    padding: 0;
    width: 32px;
    height: 32px;
    border: 0;
    background: transparent;

    &:hover, &:focus, &:active {
      background: transparent;
    }
  }

  &__icon {
    display: flex;
    width: 32px;
    height: 32px;
    align-items: center;
    justify-content: center;
    z-index: -1;
  }
}

.list-item-descriptor {
  display: flex;
  margin-right: auto;
  align-items: center;
  justify-content: center;
}

.list-item-position-selector {
  display: flex;
  margin-left: auto;
  text-align: center;
  justify-content: end;

  span {
    margin-right: 10px;

    &:last-of-type {
      margin-right: 0;
    }
  }

  @media (max-width: 580px) {
    margin-top: 15px;
    margin-left: 0;
    margin-right: auto;
  }
}
</style>

<style lang="scss">
.list-item .list-handle {
  display: block;
  width: 18px;
  min-width: 18px;
  height: 18px;
  background-image: url('data:image/svg+xml;charset=utf-8,<svg xmlns="http://www.w3.org/2000/svg" width="50" height="50" viewBox="0 0 50 50"><path d="M0 7.5v5h50v-5H0zm0 15v5h50v-5H0zm0 15v5h50v-5H0z" color="%23000"/></svg>');
  background-size: contain;
  background-repeat: no-repeat;
  opacity: .25;
  margin-right: 20px;
  cursor: row-resize;
}
</style>
