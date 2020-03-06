<template>
  <div class="login-wrap" ref="login">
    <canvas id="canvas" ref="canvas"></canvas>
    <div class="login-form">
      <div class="form-cover"></div>
      <div class="form-loader">
        <div class="spinner">
          <svg class="spinner-circular" viewBox="25 25 50 50">
            <circle
              class="spinner-path"
              cx="50"
              cy="50"
              r="20"
              fill="none"
              stroke-width="4"
              stroke-miterlimit="10"
            />
          </svg>
        </div>
      </div>
      <div class="form-content">
        <h1 class="title">Welcome</h1>
        <div class="nickname-input">
          <input type="text" class="nickname-input__input"/>
          <div class="styled-input__placeholder">
            <span class="styled-input__placeholder-text">请输入用户名</span>
          </div>
          <div class="styled-input__circle"></div>
        </div>
        <div class="password-input">
          <input type="password" class="password-input__input"/>
          <div class="styled-input__placeholder">
            <span class="styled-input__placeholder-text">请输入用户密码</span>
          </div>
          <div class="styled-input__circle"></div>
        </div>
        <button class="styled-button" @click="enter">
          <span class="styled-button__real-text-holder">
            <span class="styled-button__real-text">账号登录</span>
            <span class="styled-button__moving-block face">
              <span class="styled-button__text-holder">
                <span class="styled-button__text">账号登录</span>
              </span>
            </span>
          </span>
          <span class="styled-button__moving-block back">
            <span class="styled-button__text-holder">
              <span class="styled-button__text">账号登录</span>
            </span>
          </span>
        </button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  mounted () {
    setTimeout(() => {
      document.body.classList.add('on-start')
    }, 100)
    setTimeout(() => {
      document.body.classList.add('document-loaded')
    }, 1800)
    let _this = this
    _this.$refs.login.width = window.innerWidth
    _this.$refs.login.height = window.innerHeight
    function Star (id, x, y) {
      this.id = id
      this.x = x
      this.y = y
      this.r = Math.floor(Math.random() * 2) + 1
      var alpha = (Math.floor(Math.random() * 10) + 1) / 10 / 2
      this.color = 'rgba(255, 255, 255,' + alpha + ')'
    }
    Star.prototype.draw = function () {
      ctx.fillStyle = this.color // 填充色
      ctx.shadowBlur = this.r * 2 // 阴影
      ctx.beginPath() // 起始路径
      ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false) // 弧
      ctx.closePath() // 闭合路径
      ctx.fill() // 图型填充
    }
    Star.prototype.move = function () {
      this.y -= 0.5
      if (this.y <= -10) { this.y = _Height + 10 }
      this.draw()
    }
    Star.prototype.die = function () {
      stars[this.id] = null
      delete stars[this.id]
    }
    function Dot (id, x, y, r) {
      this.id = id
      this.x = x
      this.y = y
      this.r = Math.floor(Math.random() * 5) + 1
      this.maxLinks = 2
      this.speed = 0.5
      this.a = 0.5
      this.aReduction = 0.005
      this.color = 'rgba(255,255,255,' + this.a + ')'
      this.linkColor = 'rgba(255,255,255,' + this.a / 4 + ')'
      this.dir = Math.floor(Math.random() * 140) + 200
    }

    Dot.prototype.draw = function () {
      ctx.fillStyle = this.color
      ctx.shadowBlur = this.r * 2
      ctx.beginPath()
      ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false)
      ctx.closePath()
      ctx.fill()
    }

    Dot.prototype.link = function () {
      if (this.id == 0) return; //eslint-disable-line
      var previousDot1 = getPreviousDot(this.id, 1)
      var previousDot2 = getPreviousDot(this.id, 2)
      var previousDot3 = getPreviousDot(this.id, 3)
      if (!previousDot1) { return }
      ctx.strokeStyle = this.linkColor
      ctx.moveTo(previousDot1.x, previousDot1.y)
      ctx.beginPath()
      ctx.lineTo(this.x, this.y)
      if (previousDot2 != false) ctx.lineTo(previousDot2.x, previousDot2.y); //eslint-disable-line
      if (previousDot3 != false) ctx.lineTo(previousDot3.x, previousDot3.y); //eslint-disable-line
      ctx.stroke()
      ctx.closePath()
    }

    function getPreviousDot (id, stepback) {
      if (id == 0 || id - stepback < 0) return false; //eslint-disable-line
      if (typeof dots[id - stepback] !== 'undefined') { return dots[id - stepback] } //eslint-disable-line
      else { return false } // getPreviousDot(id - stepback);
    }
    Dot.prototype.move = function () {
      this.a -= this.aReduction
      if (this.a <= 0) {
        this.die()
        return
      }
      this.color = 'rgba(255,255,255,' + this.a + ')'
      this.linkColor = 'rgba(255,255,255,' + this.a / 4 + ')';
      (this.x = this.x + Math.cos(degToRad(this.dir)) * this.speed), //eslint-disable-line
        (this.y = this.y + Math.sin(degToRad(this.dir)) * this.speed); //eslint-disable-line

      this.draw()
      this.link()
    }

    Dot.prototype.die = function () {
      dots[this.id] = null
      delete dots[this.id]
    }

    var canvas = _this.$refs.canvas, //eslint-disable-line
      ctx = canvas.getContext("2d"), //eslint-disable-line
      _Width = _this.$refs.login.width, //eslint-disable-line
      _Height = _this.$refs.login.height, //eslint-disable-line
      mouseMoving = false, //eslint-disable-line
      mouseMoveChecker, //eslint-disable-line
      mouseX, //eslint-disable-line
      mouseY, //eslint-disable-line
      dots = [], //eslint-disable-line
      stars = [], //eslint-disable-line
      dotsMinDist = 2, //eslint-disable-line
      maxDistFromCursor = 50, //eslint-disable-line
      initStarsPopulation = 200; //eslint-disable-line
    setCanvasSize()
    __init__()
    function setCanvasSize () {
      canvas.setAttribute('width', _Width)
      canvas.setAttribute('height', _Height)
    }
    function __init__ () {
      ctx.strokeStyle = 'white'
      ctx.shadowColor = 'white'
      for (var i = 0; i < initStarsPopulation; i++) {
        stars[i] = new Star(
          i,
          Math.floor(Math.random() * _Width),
          Math.floor(Math.random() * _Height)
        )
      }
      ctx.shadowBlur = 0
      animate()
    }
    function animate () {
      ctx.clearRect(0, 0, _Width, _Height)
      for (var i in stars) {
        stars[i].move()
      }
      for (var r in dots) {
        dots[r].move()
      }
      drawIfMouseMoving()
      requestAnimationFrame(animate)
    }
    window.onmousemove = function (e) {
      mouseMoving = true
      mouseX = e.clientX
      mouseY = e.clientY
      clearInterval(mouseMoveChecker)
      mouseMoveChecker = setTimeout(function () {
        mouseMoving = false
      }, 100)
    }
    function drawIfMouseMoving () {
      if (!mouseMoving) { return }

      if (dots.length == 0) { //eslint-disable-line
        dots[0] = new Dot(0, mouseX, mouseY)
        dots[0].draw()
        return
      }

      var previousDot = getPreviousDot(dots.length, 1)
      var prevX = previousDot.x
      var prevY = previousDot.y

      var diffX = Math.abs(prevX - mouseX)
      var diffY = Math.abs(prevY - mouseY)

      if (diffX < dotsMinDist || diffY < dotsMinDist) { return }

      var xVariation = Math.random() > 0.5 ? -1 : 1
      xVariation =
        xVariation * Math.floor(Math.random() * maxDistFromCursor) + 1
      var yVariation = Math.random() > 0.5 ? -1 : 1
      yVariation =
        yVariation * Math.floor(Math.random() * maxDistFromCursor) + 1
      dots[dots.length] = new Dot(
        dots.length,
        mouseX + xVariation,
        mouseY + yVariation
      )
      dots[dots.length - 1].draw()
      dots[dots.length - 1].link()
    }
    // setInterval(drawIfMouseMoving, 17);

    function degToRad (deg) {
      return deg * (Math.PI / 180)
    }
    this.inputAnimation()
  },
  methods: {
    enter () {
      this.$router.push({
        path: '/index'
      })
    },
    placeholderAnimationIn (parent, action) {
      var act = action ? 'add' : 'remove'
      var letters = parent.querySelectorAll('.letter')
      letters = [].slice.call(letters, 0)
      if (!action) letters = letters.reverse()
      letters.forEach(function (el, i) {
        setTimeout(function () {
          var contains = parent.classList.contains('filled')
          if ((action && !contains) || (!action && contains)) return
          el.classList[act]('active')
        }, 50 * i)
      })
    },
    inputAnimation () {
      const placeholders = document.querySelectorAll('.styled-input__placeholder-text')
      const inputs = document.querySelectorAll('input')
      console.log(placeholders, inputs)
      placeholders.forEach((el, i) => {
        var value = el.innerText
        var html = ''
        for (var _iterator = value, _isArray = Array.isArray(_iterator), _i = 0, _iterator = _isArray ? _iterator : _iterator[Symbol.iterator](); ;) { //eslint-disable-line
          var _ref
          // console.log(_iterator !== _isArray, _iterator[Symbol.iterator]())
          if (_isArray) {
            if (_i >= _iterator.length) break
            _ref = _iterator[_i++]
          } else {
            _i = _iterator.next()
            if (_i.done) break
            _ref = _i.value
          }

          var w = _ref
          if (!value) value = '&nbsp;'
          html += '<span class="letter">' + w + '</span>'
          console.log(html)
        }
        el.innerHTML = html
      })
      inputs.forEach(el => {
        var parent = el.parentNode
        el.addEventListener('focus', () => {
          parent.classList.add('filled')
          this.placeholderAnimationIn(parent, true)
        }, false)
        el.addEventListener('blur', () => {
          if (el.value.length) return
          parent.classList.remove('filled')
          this.placeholderAnimationIn(parent, false)
        }, false)
      })
    }

  }
}
</script>
<style>
canvas {
  position: absolute;
}
.login-wrap {
  position: fixed;
  height: 100%;
  width: 100%;
  background: #3f2766;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login-form {
  display: flex;
  position: relative;
  align-items: center;
  justify-content: center;
  width: 400px;
  height: 400px;
  flex-shrink: 0;
  padding: 20px;
  border-radius: 5px;
  z-index: 999;
}
.spinner {
  position: relative;
  margin: auto;
  width: 50px;
  height: 50px;
  transition: all 0.2s ease 0s;
}
.spinner-circular {
  transform-origin: center center;
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  margin: auto;
}
.spinner-path {
  stroke-dasharray: 1, 200;
  stroke-dashoffset: 0;
  animation: dash 1.3s ease forwards 0.5s;
  opacity: 0;
  stroke-linecap: round;
  stroke: blueviolet;
  animation-play-state: running;
}
@keyframes dash {
  0% {
    stroke-dasharray: 1, 200;
    opacity: 0;
  }
  50% {
    stroke-dasharray: 40, 200;
    opacity: 1;
  }
  100% {
    stroke-dasharray: 125, 200;
    opacity: 1;
  }
}
.form-loader {
  display: flex;
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  justify-content: center;
  align-items: center;
  z-index: -4;
  transition: all 0.5s ease;
}
.form-cover {
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  z-index: -4;
  border-radius: 7px;
  overflow: hidden;
  transition: all 0.3s ease 0.8s;
  box-shadow: 0 0 0 0 rgba(0, 0, 0, 0);
}
.form-cover:after {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  background: #4d317a;
  z-index: -4;
  border-radius: 50%;
  transition: all 1.5s ease 0.3s;
  transform: scale(0);
}
.form-cover:before {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  background: #ffffff;
  z-index: -5;
  border-radius: 50%;
  transition: all 0.5s ease;
  transform: scale(0);
}
body.on-start .form-cover:before {
  transform: scale(0.15);
}
body.document-loaded .form-loader {
  transform: scale(0);
  opacity: 0;
  visibility: hidden;
}
body.document-loaded .form-cover {
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.2);
}
body.document-loaded .form-cover:after {
  transform: scale(2);
}
body.document-loaded .form-cover:before {
  -webkit-transition: opacity 0.3s ease 0.8s, -webkit-transform 2s ease;
  transition: opacity 0.3s ease 0.8s, -webkit-transform 2s ease;
  transition: transform 2s ease, opactiy 0.3s ease 0.8s;
  transition: transform 2s ease, opactiy 0.3s ease 0.8s,
    -webkit-transform 2s ease;
  transform: scale(2);
  opacity: 0;
}
body.document-loaded .form-content {
  opacity: 1;
  transform: none;
}
.form-content {
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  position: relative;
  transform: translateY(10px);
  transition: all 0.3s ease 0.7s;
  flex-direction: column;
}
.title {
  font-size: 40px;
  color: #714cab;
  letter-spacing: 5px;
  margin: 15px 0 20px 0;
}
.form-content input {
  width: 100%;
  -webkit-appearance: none;
  border: none;
  font-size: 14px;
  background: none;
  letter-spacing: 3.5px;
  font-weight: 600;
  padding: 12px 15px;
}
.form-content .styled-input__placeholder {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    z-index: -1;
    padding-left: 45px;
    color: white;
}
.styled-input__circle {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: -2;
    overflow: hidden;
    border-radius: 3px;
}
.styled-input__circle:after {
    content: '';
    position: absolute;
    left: 16.5px;
    top: 19px;
    height: 14px;
    width: 14px;
    z-index: -2;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.15);
    box-shadow: 0 0 10px rgba(255, 255, 255, 0);
    -webkit-transition: opacity 1s ease, -webkit-transform 0.6s ease;
    transition: opacity 1s ease, -webkit-transform 0.6s ease;
    transition: transform 0.6s ease, opacity 1s ease;
    transition: transform 0.6s ease, opacity 1s ease, -webkit-transform 0.6s ease;
}
.styled-input__placeholder-text {
    -webkit-perspective: 500px;
    perspective: 500px;
    display: inline-block;
}
.styled-input__placeholder-text .letter {
    display: inline-block;
    vertical-align: middle;
    position: relative;
    -webkit-animation: letterAnimOut 0.25s ease forwards;
    animation: letterAnimOut 0.25s ease forwards;
    text-shadow: 0 0 5px;
}
.nickname-input,
.password-input {
  width: 100%;
  position: relative;
  margin-bottom: 25px;
  border-radius: 3px;
  transition: all 0.3s ease;
  border: 1px solid rgba(255, 255, 255, 0.2);
}
.styled-button {
  cursor: pointer;
  -webkit-appearance: none;
  -webkit-user-select: none;
  font-size: 14px;
  width: 100%;
  outline: none;
  background: none;
  border: none;
  text-transform: uppercase;
  padding: 12px 20px;
  position: relative;
  letter-spacing: 10px;
  transition: all 0.3s ease;
  overflow: hidden;
  background: #714cac;
  border-radius: 3px;
  color: #492e72;
  font-weight: 600;
  margin-bottom: 25px;
}
.styled-button:hover,
.styled-button:active {
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
  background: #714cab;
}
.styled-button__real-text-holder {
  position: relative;
}
.styled-button__real-text {
  color: transparent;
  display: inline-block;
}
.styled-button__text-holder {
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.5s ease;
}
.styled-button__moving-block {
  transition: all 0.5s ease;
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  overflow: hidden;
}
.styled-button__moving-block.back {
  color: #fff;
  transform: translateX(-100%);
}
.styled-button__moving-block.back .styled-button__text-holder {
  transform: translateX(100%);
}
.styled-button:hover .face,
.styled-button:active .face {
  transform: translateX(100%);
}
.styled-button:hover .face .styled-button__text-holder,
.styled-button:active .face .styled-button__text-holder {
  transform: translateX(-100%);
}
.styled-button:hover .back,
.styled-button:active .back {
  transform: translateX(0);
}
.styled-button:active .back .styled-button__text-holder,
.styled-button:hover .back .styled-button__text-holder {
  transform: translateX(0);
}
.styled-button__text-holder:active {
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}
@keyframes letterAnimIn{
  0%{
    transform: translate(0, 0);
  }
  25%{
    transform: translate(0,10px);
    color: #f7f7b6;
  }
  45%{
    transform: translate(0,10px);
    opacity: 0;
    color: #f7f7b6;
  }
  55%{
    transform: translate(0,10px);
    opacity: 0;
  }
  56%{
    transform: translate(-30px,-27px);
    opacity: 0;
    color: #1b2947;
  }
  76%{
    color: #1b2947;
    opacity: 1;
    transform: translate(-30px, -27px);
  }
  100%{
    transform: translate(-30px, -27px);
    opacity: 1;
  }
}

@keyframes letterAnimOut{
  0%{
    transform: translate(-30px, -27px);
    opacity: 1;
  }
  25%{
    transform: translate(-30px,-40px);
    opacity: 0;
  }
  45%{
    transform: translate(0,10px);
    opacity: 0;
  }
  55%{
    transform: translate(0,10px);
    opacity: 0;
    color: #f7f7b6;
  }
  56%{
    transform: translate(0,10px);
    color: #f7f7b6;
  }
  100%{
    transform: translate(0, 0);
  }
}
.styled-input__placeholder-text .letter.active{
    animation: letterAnimIn 0.25s ease forwards;
  }
  .password-input:hover,
  .nickname-input:hover{
    border-color: rgba(255,255,255,0.4);
  }
  .password-input.filled,
  .nickname-input.filled {
    border-color: rgba(255,255,255,0.2);
  }
  .password-input.filled .styled-input__circle:after,
  .nickname-input.filled .styled-input__circle:after{
    transform: scale(37);
    opacity: 0;
  }
</style>
