<template>
  <div>
    <v-progress-linear
      v-if="!categories"
      indeterminate
    />
    <v-container v-else>
      <v-row justify="center">
        <div class="ma-6">
          <h2>
            {{ $t('commands.allCommands') }}
          </h2>
          <v-responsive width="100%" />
          <h3 class="text-center">
            {{ commands }} {{ $t('commands.commands') }}
          </h3>
        </div>
        <v-responsive width="100%" />
        <v-expansion-panels
          class="float-right ma-2"
          style="max-width: 800px"
        >
          <v-expansion-panel
            v-for="category in categories"
            :key="category.name"
            :style="{background: changeTheme()}"
            :title="category.name"
          >
            <v-expansion-panel-content>
              <div
                v-for="command in category.commands"
                :key="command.name"
              >
                <p>
                  <v-chip small>
                    {{ command.name }}
                  </v-chip>
                  <span v-if="!isMobile">
                    - {{ command.help }}
                  </span>
                  <v-dialog
                    v-model="command.dialog"
                    max-width="500"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn
                        plain
                        icon
                        small
                        class="float-right"
                        v-bind="attrs"
                        v-on="on"
                      >
                        <v-icon>mdi-information</v-icon>
                      </v-btn>
                    </template>
                    <v-card :color="changeTheme()">
                      <v-card-title>{{ $t('commands.command') }} {{ command.name }}</v-card-title>
                      <v-card-text>
                        <div class="mb-1">
                          <h3 class="text--primary">
                            {{ $t('commands.description') }}
                          </h3>
                          {{ command.help }}
                        </div>
                        <div>
                          <h3 class="text--primary mb-1">
                            {{ $t('commands.usage') }}
                          </h3>
                          <v-chip small>
                            {{ command.name }} {{ command.arguments }}
                          </v-chip>
                        </div>
                      </v-card-text>
                      <v-divider />
                      <v-card-actions>
                        <v-spacer />
                        <v-btn
                          plain
                          @click="command.dialog = false"
                        >
                          Close
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </p>
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios'
import config from '../../config.json'

export default {
  name: 'Commands',
  data: () => ({
    commands: 0,
    categories: null
  }),
  computed: {
    isMobile () {
      return this.$vuetify.display.xs
    }
  },
  mounted () {
    document.title = 'Commands - Module';

    this.init()
  },
  methods: {
    init () {
      axios.get(new URL(`/commands/${this.$i18n.locale}`, config.apiUrl).href).then(({ data }) => {
        this.commands = data.commands
        this.categories = data.categories
      })
    },
    changeTheme () {
      if (this.$vuetify.theme.current.dark) {
        return this.$vuetify.theme.themes.dark.colors.background
      }
      return this.$vuetify.theme.themes.light.colors.background
    }
  }
}
</script>
