<template>
  <v-app id="app">
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      app
      class="hidden-sm-and-up"
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon color="cyan">{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <!-- Mobile -->
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
      flat
      class="grey lighten-4"
    >
      <v-avatar justify="space-around">
        <img
          src="logo.png"
          alt="Logo"
        >
      </v-avatar>
      <v-toolbar-title class="headline blue-grey--text text--darken-1 font-weight-condensed ml-2"  v-text="title" />
      <v-spacer />
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      
      
    </v-app-bar>
    
    <!-- Desktop -->
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
      flat
      class="hidden-sm-and-down  grey lighten-4"
    >
      <v-avatar>
        <img
          src="logo.png"
          alt="Logo"
        >
      </v-avatar>

      <v-toolbar-title class="headline blue-grey--text text--darken-1 font-weight-condensed ml-2" v-text="title" />
      <v-spacer />

      <v-toolbar-items>
         <v-btn
          v-for="(item, i) in items"
          :key="i"
            :to="item.to"
           :title="item.title"
           depressed
           color="grey lighten-4"
        >
         <v-icon color="cyan" class="mx-2">{{ item.icon }}</v-icon> 
         {{ item.title }}</v-btn>
        </v-toolbar-items>
      
    </v-app-bar>

    

    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer
      absolute
      app
      class="grey lighten-4"
    >
      <v-row justify="center">
        <span>&copy; {{ new Date().getFullYear() }}  {{ title }} </span>
      </v-row>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      clipped: true,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-circle-double',
          title: 'Basic',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Advanced',
          to: '/advanced'
        }
      ],
      miniVariant: false,
      right: true,
      title: 'Shortify'
    } 
  }
}
</script>

<style>
.shadow {
 -webkit-box-shadow: 0px 5px 11px 1px rgba(238,238,238,1);
-moz-box-shadow: 0px 5px 11px 1px rgba(238,238,238,1);
box-shadow: 0px 5px 11px 1px rgba(238,238,238,1);
}
</style>