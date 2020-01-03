<template>
    <canvas class="canvas" ref="canvas">您的浏览器不支持canvas标签，请您更换浏览器</canvas>
</template>
<script>
export default {
    data(){
        return{
            canvas: "",
            ctx: "",
            w: "",
            h: "",
            words: [],
            text: "",
            x: ""
        }
    },
    mounted(){
        this.__initCanvas__()
    },
    methods:{
        __initCanvas__(){ // 初始化canvas
            this.canvas = this.$refs.canvas;
            this.ctx = this.canvas.getContext("2d");
            this.w = this.canvas.width = window.innerWidth;
            this.h = this.canvas.height = window.innerHeight;
            this.ctx.fillStyle = this.color();
            this.words = Array(256).join("1").split(""); // 生成一个有256个元素的数组,slipt去除空格
            this.ctx.font="10px 微软雅黑"; //只设置一个不生效，一定要两个属性都设
            setInterval(() => {
                this.draw()
            }, 100);
        },
        draw(){
			this.ctx.fillStyle='rgba(0,0,0,0.05)';
			this.ctx.fillRect(0, 0, this.w, this.h);
			this.ctx.fillStyle = this.color3();
			this.words.map((e, index) => { 
				this.text = String.fromCharCode(Math.ceil(65 + Math.random() * 57)); //转换为键盘上值
				this.x = index * 10;
				this.ctx.fillText(this.text, this.x, e)
				this.words[index] = ( e > 758 + Math.random() * 484 ? 0: e + 10 );
            });//数组元素的一个映射
		},
        color(){
			return "#" + (item => { //这是一个神奇的色值计算公式
				return new Array(7 - item.length).join("0") + item;
			})((Math.random()*0x1000000 << 0).toString(16))
		}
    }
}
</script>