<template>
  <ul class="tests">
    <li
      :key="test.id"
      v-for="test in tests"
      class="tests-item"
      :class="{ failed: test.state === 'failed' }"
    >
      <div
        class="tests-topbar"
        @click="toggleTests(test)"
        :class="{ hasFailed: test.state === 'failed' }"
      >
        <p :class="{ hasFailed: test.state === 'failed' }">
          <v-icon
            v-if="test.state === 'passed'"
            :color="$vuetify.theme.themes.light.color.green"
            class="icon-success"
            size="15"
          >
            check
          </v-icon>
          <v-icon
            v-if="test.state === 'pending'"
            :color="$vuetify.theme.themes.light.color.blue"
            class="icon-pending"
            size="15"
          >
            pause
          </v-icon>
          <v-icon
            v-if="test.state === 'failed'"
            :color="$vuetify.theme.themes.light.color.red"
            class="icon-error"
            size="15"
          >
            close
          </v-icon>
          <span class="icon-skipped" v-if="test.state === 'skipped'">
            <svg
              version="1.1"
              id="Layer_1"
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              x="0px"
              y="0px"
              width="44px"
              height="44px"
              viewBox="0 0 44 44"
              enable-background="new 0 0 44 44"
              xml:space="preserve"
            >
              <g>
                <path d="M27,29H17V0h10V29z M27,44H17v-8h10V44z" />
              </g>
            </svg>
          </span>
          {{ test.title }}
        </p>

        <span v-if="test.duration">
          <v-icon size="18">timer</v-icon>
          {{ $moment.duration(test.duration).asSeconds() }}s
        </span>
      </div>
      <p
        class="tests-stacktrace tests-error"
        @click="toggleTests(test)"
        v-if="test.state === 'failed' && test.error_message"
        v-html="test.error_message"
      ></p>

      <p
        class="tests-stacktrace"
        v-if="testsOpened.includes(test.id) && test.state === 'failed'"
        v-html="test.stack_trace_formatted"
      ></p>
    </li>
  </ul>
</template>

<script>
  export default {
    props: {
      items: {
        type: Object.JSON,
        default: {}
      }
    },
    data() {
      return {
        tests: this.items,
        testsOpened: []
      }
    },
    mounted() {},
    methods: {
      toggleTests(test) {
        if (!this.testsOpened.includes(test.id)) {
          this.testsOpened.push(test.id)
        } else {
          const indexToDelete = this.testsOpened.indexOf(test.id)
          this.testsOpened = [
            ...this.testsOpened.slice(0, indexToDelete),
            ...this.testsOpened.slice(indexToDelete + 1)
          ]
        }
      }
    }
  }
</script>

<style lang="scss">
  .tests {
    margin-top: 5px;
    background: lighten($blue, 47%);
    border-radius: 4px;
    padding: 5px 0;
    transition: 0.4s ease-out;

    .icon-skipped {
      display: inline-flex;
    }

    @at-root .dark & {
      background: #2a2b33;
    }

    .icon-success {
      background: #c1f4d1;
    }
    .icon-error {
      background: #fbc6c3;
    }
    .icon-pending {
      background: #beeaf3;
    }

    .v-icon {
      width: 19px;
      height: 19px;
      border-radius: 50%;
      margin-right: 5px;
    }

    &-topbar {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 10px;

      &.hasFailed {
        transition: 0.4s ease-out;
        cursor: pointer;
        border-left: 2px solid transparent;

        &:hover {
          opacity: 0.7;
          border-left: 2px solid $red;
        }
      }
    }

    &-item {
      list-style-type: none;

      span {
        font-size: 14px;
        display: flex;
        align-items: center;

        &,
        .v-icon {
          color: #8ba7ad;
        }
      }

      p {
        margin: 5px 0;
        font-size: 15px;
        transition: 0.4s ease-out;

        @at-root .dark & {
          color: darken(white, 25%);
        }
      }

      &.failed {
        p {
          color: $red;
        }

        .tests-stacktrace {
          padding: 0px 20px;
          font-size: 14px !important;
          color: lighten(black, 20%);

          &:not(.tests-error) {
            font-family: Courier, sans-serif;
            font-size: 12px !important;
          }

          @at-root .dark & {
            color: white;
          }
        }

        .tests-error {
          cursor: pointer;
          transition: 0.4s ease-out;
          color: $red;

          @at-root .dark & {
            color: $red;
          }

          &:hover {
            opacity: 0.6;
          }
        }
      }
    }
  }

  @media screen and (max-width: 768px) {
    .tests {
      &-item {
        span {
          display: none;
        }
      }
    }
  }
</style>
