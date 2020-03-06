<template>
  <div class="svg-nav">
    <div id="menu" :class="{expanded:expo_val}" @mousemove="mousemove($event)">
      <div class="hamburger" ref="hamburger" @click="expandedNav">
        <div class="line"></div>
        <div class="line"></div>
        <div class="line"></div>
      </div>
      <div class="menu-inner">
        <ul class="nav-list">
          <li class="nav-link" v-for="(item,index) in 12" :key="index">
            <svg id="box" data-v-30b56772="" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-box link-icon"><path data-v-30b56772="" d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"></path> <polyline data-v-30b56772="" points="3.27 6.96 12 12.01 20.73 6.96"></polyline> <line data-v-30b56772="" x1="12" y1="22.08" x2="12" y2="12"></line></svg>&nbsp;
            <span v-if="expo_val">nav-item</span>
          </li>
        </ul>
      </div>
      <svg
        id="blob"
        ref="blob"
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
      >
        <path id="blob-path" ref="blobPath"/>
      </svg>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      expanded: 'expanded',
      expo_val: false,
      x: 0,
      y: window.screen.availHeight / 2,
      curX: 10,
      curY: 0,
      tarX: 0,
      height: window.screen.availHeight,
      xItteration: 0,
      yItteration: 0,
      hoverZone: 150,
      expandAmount: 20
    }
  },
  mounted () {
    window.requestAnimationFrame(this.svgCurve)
  },
  methods: {
    expandedNav () {
      this.expo_val = !this.expo_val
    },
    mousemove (e) {
      this.x = e.pageX
      this.y = e.pageY
    },
    easeOutExpo (currentItteration, startValue, changeInValue, totalItterations) {
      return changeInValue * (-Math.pow(2, -10 * currentItteration / totalItterations) + 1) + startValue
    },
    svgCurve () {
      if ((this.curX > this.x - 1) && (this.curX < this.x - 1)) {
        this.xItteration = 0
      } else {
        if (this.expo_val) {
          this.tarX = 0
        } else {
          this.xItteration = 0
          if (this.x > this.hoverZone) {
            this.tarX = 0
          } else {
            this.tarX = -(((60 + this.expandAmount) / 100) * (this.x - this.hoverZone))
          }
        }
        this.xItteration++
      }
      if ((this.curY > this.y - 1) && (this.curY < this.y - 1)) {
        this.yItteration = 0
      } else {
        this.yItteration = 0
        this.yItteration++
      }
      this.curX = this.easeOutExpo(this.xItteration, this.curX, this.tarX - this.curX, 100)
      this.curY = this.easeOutExpo(this.yItteration, this.curY, this.y - this.curY, 100)
      var anchorDistance = 200
      var curviness = anchorDistance - 40
      var newCurve2 = 'M60,' + this.height + 'H0V0h60v' + (this.curY - anchorDistance) + 'c0,' + curviness + ',' + this.curX + ',' + curviness + ',' + this.curX + ',' + anchorDistance + 'S60,' + (this.curY) + ',60,' + (this.curY + (anchorDistance * 2)) + 'V' + this.height + 'z'
      this.$refs.blobPath.setAttribute('d', newCurve2)
      this.$refs.blob.setAttribute('width', (this.curX + 70))
      if (!this.expo_val) {
        this.$refs.hamburger.style.cssText = `transform: translate(${this.curX}px, ${this.curY}px)`
      } else {
        this.$refs.hamburger.style.cssText = 'transform: translate(0px, 40px); transition: all 0.5s ease'
      }
      window.requestAnimationFrame(this.svgCurve)
    }
  }
}
</script>
<style scoped>
  .svg-nav{
    position: fixed;
    height: 100%;
    width: 100%;
    top: 0;
    left: 0;
    background: #031a61;
  }
  #menu{
    position: fixed;
    height: 100%;
    left: 0;
    transition: all 0.3s ease;
    width: 60px;
    background: #fad075;
  }
  #menu.expanded{
    width: 200px;
  }
  .hamburger{
    position: absolute;
    margin-top: -10px;
    width: 20px;
    height: 20px;
    cursor: pointer;
    right: -10px;
    z-index: 101;
  }
  .hamburger .line{
    background: #fff;
    border-radius: 3px;
    width: 100%;
    height: 4px;
  }
  .hamburger .line:nth-child(2){
    margin: 3px 0;
  }
  .nav-list{
    width: 80%;
    top: 10px;
    position: absolute;
  }
  .nav-list .nav-link{
    color: #fff;
    padding: 20px 0;
    overflow: hidden;
    white-space: nowrap;
    transition: all 0.3s ease-in-out;
    cursor: pointer;
  }
  #box{
    position: absolute;
    left: 10px;
  }
  .nav-list .nav-link:hover{
    text-decoration: none;
    transition: all 0.4s ease-in-out;
    margin-left: 1px;
    color: #031a61;
  }
  #blob{
    position: absolute;
    top: 0;
    z-index: 1;
    right: 20px;
    transform: translateX(100%);
    height: 100%;
  }
  #blob-path{
    height: 100%;
    fill:#fad075;
  }
</style>
