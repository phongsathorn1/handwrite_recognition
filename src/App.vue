<template>
  <div id="app">
    <div class="container">
      <h1>Digit recognition</h1>
    </div>
    <div class="container-grid">
      <div class="drawing-box" id="drawing-box">
        <canvas id="canvas" width="600" height="600"></canvas>
        <div class="cursor" id="cursor"></div>
      </div>
      <div class="answer-box">
        <h2>The answer is:</h2>
        <span class="answer">1</span>
      </div>
    </div>
  </div>
</template>

<script>
var draw = null;
var color = '#c8dad3'
var draw_history = []

export default {
  name: 'app',
  mounted(){
    draw = new Draw()
  }
}

class Draw{
  constructor(){
    this.c = document.getElementById('canvas')
    this.ctx = this.c.getContext('2d')

    this.mouseDown = false
    this.mouseX = 0
    this.mouseY = 0

    this.listen()
    this.redraw()
  }

  listen(){
    this.c.addEventListener('mousedown', (event)=>{
      this.mouseDown = true

      var rect = this.c.getBoundingClientRect();

      this.mouseX = (event.offsetX + rect.left) - 60
      this.mouseY = (event.offsetY + rect.top) - 60
      this.setDummyPoint()
    })

    this.c.addEventListener('mouseup', ()=>{
      if(this.mouseDown){
        this.setDummyPoint()
      }
      this.mouseDown = false
    })

    this.c.addEventListener('mouseleave', ()=>{
      if(this.mouseDown){
        this.setDummyPoint()
      }
      this.mouseDown = false
    })

    this.c.addEventListener('mousemove', (event)=>{
      this.mouseMove(event)

      var rect = this.c.getBoundingClientRect();

      if(this.mouseDown){
        this.mouseX = event.offsetX
        this.mouseY = event.offsetY

        let item = {
          isDummy: false,
          x: this.mouseX,
          y: this.mouseY,
          c: color,
          r: 12
        }

        draw_history.push(item)
        this.draw(item, draw_history.length)
      }
    })
  }

  createImage(){
    var image = new Image();
    image.id = "pic"
    image.src = canvas.toDataURL();
    console.log(image)
  }

  mouseMove(event){
    let x = event.offsetX
    let y = event.offsetY

    var cursor = document.getElementById('cursor')

    // cursor.style.transform = `translate(${x - 10}px, ${y - 10}px)`;
    cursor.style.top = `${y - 10}px`
    cursor.style.left = `${x - 10}px`
  }

  getDummyItem(){
    let lastPoint = draw_history[draw_history.length-1]
    console.log(lastPoint)

    return {
      isDummy: true,
      x: lastPoint.x,
      y: lastPoint.y,
      c: null,
      r: null
    }
  }

  setDummyPoint(){
    let item = this.getDummyItem()
    draw_history.push(item)
    this.draw(item, draw_history.length)
  }

  redraw(){
    this.ctx.clearRect(0, 0, this.c.width, this.c.height)

    if(!draw_history.length){
      return true
    }

    draw_history.forEach((item, i)=>{
      this.draw(item, i)
    })
  }

  draw(item, i){
    this.ctx.lineCap = 'round';
    this.ctx.lineJoin = 'round';

    let prevItem = draw_history[i-2]

    if(i < 2){
      return false
    }

    if(!item.isDummy && !draw_history[i-1].isDummy && !prevItem.isDummy){
      console.log('draw1')
      this.ctx.strokeStyle = item.c
      this.ctx.lineWidth = item.r

      this.ctx.beginPath()
      this.ctx.moveTo(prevItem.x, prevItem.y)
      this.ctx.lineTo(item.x, item.y)
      this.ctx.stroke()
      this.ctx.closePath()
    } else if (!item.isDummy) {
      this.ctx.strokeStyle = item.c
      this.ctx.lineWidth = item.r

      this.ctx.beginPath()
      this.ctx.moveTo(item.x, item.y)
      this.ctx.lineTo(item.x, item.y)
      this.ctx.stroke()
      this.ctx.closePath()
    }
  }
}

</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  padding: 20px 0px;
  background: #f2f6f5;
  height: 100vh;
}

.container{
  width: 1200px;
  margin: auto;
}

.container-grid{
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  width: 1200px;
  margin: auto;
}

.center{
  text-align: center;
}

.drawing-box{
  position: relative;
  grid-column: auto/span 8;
}

.answer-box{
  grid-column: auto/span 4;
  padding: 10px 50px;
  text-align: center;
}

.cursor {
	position: absolute;
	top: 0;
	left: 0;
	width: 20px;
	height: 20px;
	border-radius: 50%;
	border: 3px solid rgb(30, 30, 30);
	pointer-events: none;
	user-select: none;
	mix-blend-mode: difference;
	opacity: 0;
	// transition: opacity 1s;
}

canvas {
	// width: 100%;
	// height: calc(100vh - 60px);
	background-color: #63707e;
	cursor: none;
	
	&:hover + .cursor {
		opacity: 1;
	}
	
	&:active + .cursor {
		border-color: rgb(60, 60, 60);
	}
}

.answer{
  font-size: 5em;
}

</style>
