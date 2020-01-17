<template>
    <div class="sky" ref="sky">
        <canvas ref="canvas"></canvas>
        <div class="bg-img"></div>
        <div class="filter"></div>
    </div>
</template>
<script>
export default {
    mounted() {
        let _this = this;
        _this.$refs.sky.width = window.innerWidth;
        _this.$refs.sky.height = window.innerHeight;
        function Star(id, x, y) {
            this.id = id;
            this.x = x;
            this.y = y;
            this.r = Math.floor(Math.random() * 2) + 1;
            var alpha = (Math.floor(Math.random() * 10) + 1) / 10 / 2;
            this.color = "rgba(255, 255, 255," + alpha + ")"
        }
        Star.prototype.draw = function() {
            ctx.fillStyle = this.color; // 填充色
            ctx.shadowBlur = this.r * 2; // 阴影
            ctx.beginPath(); // 起始路径
            ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false); // 弧
            ctx.closePath(); // 闭合路径
            ctx.fill(); // 图型填充
        }
        Star.prototype.move = function() {
            this.y -= .5;
            if (this.y <= -10) this.y = _Height + 10;
            this.draw();
        }
        Star.prototype.die = function() {
            stars[this.id] = null;
            delete stars[this.id];
        }
        function Dot(id, x, y, r) {
            this.id = id;
            this.x = x;
            this.y = y;
            this.r = Math.floor(Math.random() * 5) + 1;
            this.maxLinks = 2;
            this.speed = .5;
            this.a = .5;
            this.aReduction = .005;
            this.color = "rgba(255,255,255,"+this.a + ")";
            this.linkColor = "rgba(255,255,255,"+this.a / 4 + ")";
            this.dir = Math.floor(Math.random()*140)+200;
        }

        Dot.prototype.draw = function() {
            ctx.fillStyle = this.color;
            ctx.shadowBlur = this.r * 2;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
            ctx.closePath();
            ctx.fill();
        }

        Dot.prototype.link = function() {
            if (this.id == 0) return;
            var previousDot1 = getPreviousDot(this.id, 1);
            var previousDot2 = getPreviousDot(this.id, 2);
            var previousDot3 = getPreviousDot(this.id, 3);
            if (!previousDot1) return;
            ctx.strokeStyle = this.linkColor;
            ctx.moveTo(previousDot1.x, previousDot1.y);
            ctx.beginPath();
            ctx.lineTo(this.x, this.y);
            if (previousDot2 != false) ctx.lineTo(previousDot2.x, previousDot2.y);
            if (previousDot3 != false) ctx.lineTo(previousDot3.x, previousDot3.y);
            ctx.stroke();
            ctx.closePath();
        }

        function getPreviousDot(id, stepback) {
            if (id == 0 || id - stepback < 0) return false;
            if (typeof dots[id - stepback] != "undefined") return dots[id - stepback];
            else return false;//getPreviousDot(id - stepback);
        }
        Dot.prototype.move = function() {
            this.a -= this.aReduction;
            if (this.a <= 0) {
                this.die();
                return
            }
            this.color = "rgba(255,255,255," + this.a + ")";
            this.linkColor = "rgba(255,255,255," + this.a / 4 + ")";
            this.x = this.x + Math.cos(degToRad(this.dir))*this.speed,
            this.y = this.y + Math.sin(degToRad(this.dir))*this.speed;

            this.draw();
            this.link();
        }

        Dot.prototype.die = function() {
            dots[this.id] = null;
            delete dots[this.id];
        }
        
        var canvas = _this.$refs.canvas,
            ctx = canvas.getContext("2d"),
            _Width = _this.$refs.sky.width,
            _Height = _this.$refs.sky.height,
            mouseMoving = false,
            mouseMoveChecker,
            mouseX,
            mouseY,
	        dots = [],
            stars = [],
	        dotsMinDist = 2,
	        maxDistFromCursor = 50,
            initStarsPopulation = 200;
        setCanvasSize();
        __init__();
        function setCanvasSize() {
            canvas.setAttribute("width", _Width)
            canvas.setAttribute("height", _Height)
        }
        function __init__() {
            ctx.strokeStyle = "white";
            ctx.shadowColor = "white";
            for (var i = 0; i < initStarsPopulation; i++) {
                stars[i] = new Star(i, Math.floor(Math.random() * _Width), Math.floor(Math.random() * _Height));
            }
            ctx.shadowBlur = 0;
            animate();
        }
        function animate() {
            ctx.clearRect(0, 0, _Width, _Height);
            for (var i in stars) {
                stars[i].move();
            }
            for (var i in dots) {
                dots[i].move();
            }
            drawIfMouseMoving();
            requestAnimationFrame(animate);
        }
        window.onmousemove = function(e){
            mouseMoving = true;
            mouseX = e.clientX;
            mouseY = e.clientY;
            clearInterval(mouseMoveChecker);
            mouseMoveChecker = setTimeout(function() {
                mouseMoving = false;
            }, 100);
        }
        function drawIfMouseMoving(){
            if (!mouseMoving) return;

            if (dots.length == 0) {
                dots[0] = new Dot(0, mouseX, mouseY);
                dots[0].draw();
                return;
            }

            var previousDot = getPreviousDot(dots.length, 1);
            var prevX = previousDot.x; 
            var prevY = previousDot.y; 

            var diffX = Math.abs(prevX - mouseX);
            var diffY = Math.abs(prevY - mouseY);

            if (diffX < dotsMinDist || diffY < dotsMinDist) return;

            var xVariation = Math.random() > .5 ? -1 : 1;
            xVariation = xVariation*Math.floor(Math.random()*maxDistFromCursor)+1;
            var yVariation = Math.random() > .5 ? -1 : 1;
            yVariation = yVariation*Math.floor(Math.random()*maxDistFromCursor)+1;
            dots[dots.length] = new Dot(dots.length, mouseX+xVariation, mouseY+yVariation);
            dots[dots.length-1].draw();
            dots[dots.length-1].link();
        }
        //setInterval(drawIfMouseMoving, 17);

        function degToRad(deg) {
            return deg * (Math.PI / 180);
        }
    }
}
</script>
<style lang="less" scoped>
    .sky{
        background: radial-gradient(65% 105% at bottom center, #f7f7b6 10%, #e96f92 40%,#75517d  65%, #1b2947);
    }
    .bg-img{
        position: fixed;
        width: 100%;
        height: 100%;
        top:0;
        background-image: url(../../assets/bgImages/xkbg.png);
        background-repeat: repeat-x;
        background-position: center bottom;
    }
    .filter{
        position: fixed;
        width: 100%;
        height: 100%;
        top:0;
        background: #f00000;
        mix-blend-mode: overlay;
        animation: colorChange 30s ease-in-out infinite;
        animation-fill-mode: both;
    }
    @keyframes colorChange {
        0%,
        100% {
            opacity: 0;
        }
        50% {
            opacity: 0.9;
        }
    }
</style>