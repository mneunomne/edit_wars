<template>
  <v-app id="app">
    <div class="gradient" >
    </div>
    <client-only>
      <NarrativesGraph class='backgroundgraph' v-if="isHomeRoute || isNarrativesRoute || isAboutRoute">
      </NarrativesGraph>
    </client-only>
    <Menu @onclose="onMenuClose" :isOpen="isMenuOpen"></Menu>
      <v-app-bar
        app
        flat
        elevation="0"
        class="topbar"
        color="transparent"
        justify-space-around
      >
      <transition name="fade">
        <div
          v-show="!this.isIntroFirstStep && delayOver"
          @click="onClickHome"
          class="title"
        >
          EDIT WARS
        </div>
      </transition>

        <!--
        <NuxtLink
          class="topbar_item justify-end about"
          to="/about"
        >
          {{ $t('about') }}
        </NuxtLink>
        -->
        <v-spacer></v-spacer>
        <transition name="fademenu">
          <div
            class="menu"
            v-if="!isIntroFirstStep && !isMenuOpen"
            @click="onClickMenu"
          >
            MENU
          </div>
        </transition>
      </v-app-bar>
    <v-main class="main">
      <transition name="fade">
        <nuxt class="content" :key="$route.params.id" />
      </transition>
    </v-main>
  </v-app>
</template>

<script>
import EventBus from '@/utils/event-bus'
import NarrativesGraph from '../components/NarrativesGraph.vue'

export default {
  head: {
    title: 'Edit Wars - Home',
    meta: [
      {
        hid: 'description',
        name: 'description',
        content: 'Deconstructing Russian Propaganda Narratives'
      }
    ],
    script: [
      { src: 'https://identity.netlify.com/v1/netlify-identity-widget.js' }
    ]
  },
  scrollToTop: false,
  components: { NarrativesGraph },
  computed: {
    isNarrativesRoute () {
      if (!process.browser) {
        return null
      }
      return $nuxt.$route.path === '/narratives'
    },
    isAboutRoute () {
      if (!process.browser) {
        return null
      }
      return $nuxt.$route.path === '/about'
    },
    isHomeRoute() {
      if (!process.browser) {
        return null
      }
      return $nuxt.$route.path === '/'
    },
    isEnglish() {
      return this.$i18n.locale === 'en'
    },
    languageToChange() {
      return this.isEnglish ? 'ru' : 'en'
    }
  },
  mounted() {
    console.log('$nuxt.$route.path.slice(-1)', $nuxt.$route.path.slice(-1))
    if ($nuxt.$route.path.slice(-1) === '/') {
      this.$router.push({ path: $nuxt.$route.path.substr(0, $nuxt.$route.path.length-1) })
    }
    setTimeout(() => {
      this.delayOver = true
    }, 250)
    if ($nuxt.$route.path !== '/') {
      this.isIntroFirstStep = false
    } else {
      this.isIntroFirstStep = true
    }
    EventBus.$on('introfirst', () => {
      this.isIntroFirstStep = true
    })
    EventBus.$on('introsecond', () => {
      this.isIntroFirstStep = false
    })
  },
  data() {
    return {
      isMenuOpen: false,
      isIntroFirstStep: true,
      delayOver: false
    }
  },
  methods: {
    onClickHome() {
      if ($nuxt.$route.path === '/') {
        location.reload();
      } else {
        this.$router.push({ path: '/' })
      } 
    },
    isMobile() {
      let check = false
      ;(function (a) {
        if (
          /(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i.test(
            a
          ) ||
          /1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i.test(
            a.substr(0, 4)
          )
        )
          check = true
      })(navigator.userAgent || navigator.vendor || window.opera)
      return check
    },
    onChangeLanguage() {
      if (this.isEnglish) {
        this.$i18n.locale = 'ru'
      } else {
        this.$i18n.locale = 'en'
      }
    },
    onMenuClose() {
      console.log('out')
      this.isMenuOpen = false
    },
    onClickMenu() {
      console.log('on click menu')
      this.isMenuOpen = true
    }
  },
  watch: {}
}
</script>

<style lang="sass">
.v-toolbar__content
  z-index: 1000 !important
  padding: 0px 0px !important
</style>

<style lang="sass" scoped>
#app
  background-color: white
  -webkit-transition: background-color 1s ease-in
          transition: background-color 1s ease-in
  -webkit-transition-delay: 0.5s
          transition-delay: 0.5s
.main
  color: white
  &.blur
    backdrop-filter: blur(3px) !important

.backgroundgraph
  position: fixed
  top: 0
  left: 0
  width: 100vw
  height: 100vh
  z-index: 5
.title
  cursor: pointer
  user-select: none
  color: black !important
  font-size: 54px !important
  z-index: 100
  font-family: Space Mono !important
  font-weight: 700 !important
  margin-left: -2px
  margin-top: 40px
  @media only screen and (max-width: 480px)
    font-size: 26px !important
    margin-top: 0px
.topbar
  color: white
  z-index: 1000 !important
  width: 100vw
  padding: 0px 30px 0px 30px
  @media only screen and (max-width: 480px)
    padding: 0px 12px 0px 12px !important

.menu
  cursor: pointer
  font-family: Space Mono
  color: black
  font-weight: 400
  font-size: 32px
  user-select: none
  text-transform: uppercase
  margin-top: 30px
  @media only screen and (max-width: 480px)
    font-size: 26px !important
    margin-top: -1px
  &:hover
    font-style: italic

.gradient
  width: 100vw !important
  height: 150px
  pointer-events: none !important
  top: 0
  position: fixed
  z-index: 100
  background-image: linear-gradient(0deg,hsla(0,0%,100%,0),#fff)
  </style>
