<template>
  <div id="app">
    <mu-appbar :zDepth="0"  class="example-appbar" :class="{'nav-hide': !open}">
      <mu-icon-button @click="toggleNav" icon="menu" slot="left"/>
      <h2  @click="toIndex" slot="left">Home</h2>
      <mu-icon-button href="https://github.com/museui/muse-ui" icon="github_circle" style="margin-left:4em;">
      </mu-icon-button>
    </mu-appbar>
    <app-side @change="handleMenuChange" @close="toggleNav" :open="open" :docked="docked" />
    <div class="example-content" :class="{'nav-hide': !open}">
      <transition name="fade"  mode="out-in">
        <router-view></router-view>
      </transition>
    </div>
  </div>
</template>

<script>
import appSide from './components/AppSide'

export default {
  data () {
    const desktop = isDesktop()
    return {
      open: desktop,
      docked: desktop,
      desktop: desktop,
      title: ''
    }
  },
  mounted () {
    this.routes = this.$router.options.routes
    this.setTitle()
    this.changeNav()
    this.handleResize = () => {
      this.changeNav()
    }
    window.addEventListener('resize', this.handleResize)
    window.addEventListener('hashchange', () => {
      // this.setTitle()
    })
    this.$on("close",()=>{
      this.open=false;
    })
    
  },
  methods: {
    toggleNav () {
      this.open = !this.open
    },
    changeNav () {
      const desktop = isDesktop()
      this.docked = desktop
      if (desktop === this.desktop) return
      if (!desktop && this.desktop && this.open) {
        this.open = false
      }
      if (desktop && !this.desktop && !this.open) {
        this.open = true
      }
      this.desktop = desktop
    },
    handleMenuChange (path) {
      if (!this.desktop) this.open = false
    },
    setTitle () {
      let path = window.location.hash
      console.log(path)
      if (path && path.length > 1) path = path.substring(1)
      for (let i = 0; i < this.routes.length; i++) {
        var route = this.routes[i]
        if (route.path === path) {
          this.title = path.substring(1) || ''
          return
        }
      }
    },
    toIndex(){
      this.$router.push("/");
    }
  },
  destroyed () {
    window.removeEventListener('resize', this.handleResize)
  },
  components:{
    appSide
  }
}

function isDesktop () {
  return window.innerWidth > 993
}
</script>

<style lang="less">
@import "../src/styles/import.less";
.example-appbar{
  position: fixed;
  left: 256px;
  right: 0;
  top: 0;
  width: auto;
  transition: all .45s @easeOutFunction;
  &.nav-hide {
    left: 0;
  }
}

.example-content{
  padding-top: 56px;
  padding-left: 256px;
  transition: all .45s @easeOutFunction;
  &.nav-hide {
    padding-left: 0;
  }
}

.content-wrapper{
  padding: 48px 72px;
}

@media (min-width: 480px) {
  .example-content{
    padding-top: 64px;
  }
}

@media (max-width: 993px) {
  .example-appbar {
    left: 0;
  }
  .example-content{
    padding-left: 0;
  }
  .content-wrapper {
    padding: 24px 36px;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s ease;
}
.fade-enter, .fade-leave-active {
  opacity: 0
}

</style>
