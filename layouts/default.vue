<template>
  <v-app dark>
    <v-navigation-drawer v-model="drawer" fixed app>
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar app dark fixed flat color="primary">
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title>English Quiz</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Irregular verbs',
          to: '/',
        },
        {
          icon: 'mdi-lightbulb-on',
          title: 'Past simple quiz',
          to: '/quiz/1',
        },
        {
          icon: 'mdi-lightbulb-on-outline',
          title: 'Past participle quiz',
          to: '/quiz/2',
        },
        {
          icon: 'mdi-translate',
          title: 'Translate (RO -> EN)',
          to: '/quiz/3',
        },
        {
          icon: 'mdi-translate',
          title: 'Translate (EN -> RO)',
          to: '/quiz/4',
        },
        {
          icon: 'mdi-volume-high',
          title: 'Speech infinitive',
          to: '/quiz/5',
        },
        {
          icon: 'mdi-volume-high',
          title: 'Speech past simple',
          to: '/quiz/6',
        },
        {
          icon: 'mdi-volume-high',
          title: 'Speech past participle',
          to: '/quiz/7',
        },
      ],
      title: 'Vuetify.js',
    }
  },
}
</script>
