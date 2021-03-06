# Using React with canvas element

[Original URL](https://medium.com/@lavrton/using-react-with-html5-canvas-871d07d8d753#.4eqd9dxn5)

> We have React that helps us to work with massive mutable DOM in a nice immutable functional style. That is awesome.But I would like to describe a way of using React that helps you to work with...

![](https://cdn-images-2.medium.com/max/2000/1*SdXg4o3AfvEbpO4nLNlo2g.png)

We have React that helps us to work with massive mutable DOM in a nice immutable functional style. That is awesome.

But I would like to describe a way of using React that helps you to work with mutable object representation of "immutable" <canvas> element. WTF? Yes, it sounds strange. But it works really well.

I am using <canvas> element a lot. I made several complex applications which use <canvas> as a main view component. Vanilla canvas without any frameworks/libraries can be really difficult for complex applications. So I started to use canvas frameworks a lot. Now I maintain [Konva 2d canvas Framework](http://konvajs.github.io/).

Konva helps a lot, but still now not so good, as I wanted. Also I started to use React in my applications and I really like to use it. So I was thinking how can I use React for drawing graphics on canvas.

It is very easy to access <canvas> context in React component and draw any graphics:

```
class CanvasComponent extends React.Component {
 componentDidMount() {
 this.updateCanvas();
 }
 updateCanvas() {
 const ctx = this.refs.canvas.getContext('2d');
 ctx.fillRect(0,0, 100, 100);
 }
 render() {
 return (
 <canvas ref="canvas" width={300} height={300}/>
 );
 }
}

ReactDOM.render(<CanvasComponent/>, document.getElementById('container'));
```

Demo: <http://jsbin.com/xituko/edit?js,output>

It works nice for trivial examples. But for complex applications this approach is not so good because we can not use full power of reusable React components.

```
// “reusable component”
function rect(props) {
 const {ctx, x, y, width, height} = props;
 ctx.fillRect(x, y, width, height);
}

class CanvasComponent extends React.Component {
 componentDidMount() {
 this.updateCanvas();
 }
 componentDidUpdate() {
 this.updateCanvas();
 }
 updateCanvas() {
 const ctx = this.refs.canvas.getContext('2d');
 ctx.clearRect(0,0, 300, 300);
 // draw children “components”
 rect({ctx, x: 10, y: 10, width: 50, height: 50});
 rect({ctx, x: 110, y: 110, width: 50, height: 50});
 }
 render() {
 return (
 <canvas ref="canvas" width={300} height={300}/>
 );
 }
}

ReactDOM.render(<CanvasComponent/>, document.getElementById('container'));
```

And what about React's lifecycle methods (like _shouldComponentUpdate etc_) and "all representation inside render function"?

I really like React approach for building apps. So I made react binding to Konva objects. And now I can draw canvas stage with different graphic shapes and use it in my React application.

```
npm install react konva react-konva --save
```

Then

```
import React from 'react';
import ReactDOM from 'react-dom';
import {Layer, Rect, Stage, Group} from ‘react-konva’;

class MyRect extends React.Component {
 constructor(...args) {
 super(...args);
 this.state = {
 color: 'green'
 };
 this.handleClick = this.handleClick.bind(this);
 }
 handleClick() {
 this.setState({
 color: Konva.Util.getRandomColor()
 });
 }
 render() {
 return (
 <Rect
 x={10} y={10} width={50} height={50}
 fill={this.state.color}
 shadowBlur={10}
 onClick={this.handleClick}
 />
 );
 }
}

function App() {
 return (
 <Stage width={700} height={700}>
 <Layer>
 <MyRect/>
 </Layer>
 </Stage>
 );
}

ReactDOM.render(<App/>, document.getElementById('container'));
```

Demo: <http://jsbin.com/camene/5/edit?html,js,output>

Thanks <https://github.com/reactjs/react-art> for providing good examples of custom React components.

[react-canvas](https://github.com/Flipboard/react-canvas) is a completely different react plugin. It allows you to draw DOM-like objects (images, texts) on canvas element in very performant way. It is NOT about drawing graphics, but **react-konva** is exactly for drawing complex graphics on <canvas> element from React.

[react-art](https://github.com/reactjs/react-art) allows you to draw graphics on a page. It also supports SVG for output. But it has no support of events of shapes.

Performance is one of the main buzz word in react hype.

I made this plugin not for performance reasons. Using vanilla <canvas> should be more performant because while using react-konva you have Konva framework on top of <canvas> and React on top of Konva. But I made this plugin to fight with application complexity. Konva helps here a lot (especially when you need events for objects on canvas, like "click" on shape, it is really hard to do with vanilla canvas). But React helps here much more as it provides very good structure for your codebase and data flow.

![](https://cdn-images-2.medium.com/max/800/1*5ZIw0SFvYvzAI56VFKMHQQ.png)

react-konva logo _react-konva_ works on top of _Konva_ so it has support for all core shapes: Circle, Rect, Ellipse, Line, Sprite, Image, Text, TextPath, Star, Ring, Arc, Label, SVG Path, RegularPolygon, Arrow and you can create your own custom shapes. Also you can use build in drag&drop support, tweening, animations, filters, caching system, desktop and mobile events (mouseenter, click, dblclick, dragstart, dragmove, dragend, tap, dbltap, etc) and much more.

```
// custom shape example
function MyShape() {
 return (
 <Shape fill=”#00D2FF” draggable
 sceneFunc={function (ctx) {
 ctx.beginPath();
 ctx.moveTo(20, 50);
 ctx.lineTo(220, 80);
 ctx.quadraticCurveTo(150, 100, 260, 170);
 ctx.closePath();

 // Konva specific method
 ctx.fillStrokeShape(this);
 }}
 />
 );
}
```

Demo: <http://jsbin.com/gakadi/2/edit?html,js,output>

```
class MyCircle extends React.Component {
 constructor(…args) {
 super(…args);
 this.state = { isMouseInside: false};
 this.handleMouseEnter = this.handleMouseEnter.bind(this);
 this.handleMouseLeave = this.handleMouseLeave.bind(this);
 }
 handleMouseEnter() {
 this.setState({ isMouseInside: true});
 }
 handleMouseLeave() {
 this.setState({ isMouseInside: false});
 }
 render() {
 return (
 <Circle
 x={100} y={60} radius={50}
 fill=”yellow” stroke=”black”
 strokeWidth={this.state.isMouseInside ? 5 : 1}
 onMouseEnter={this.handleMouseEnter}
 onMouseLeave={this.handleMouseLeave}
 />
 );
 }
}
```

Demo: <http://jsbin.com/tekopu/1/edit>

Github repo: <https://github.com/lavrton/react-konva>

Konva framework: <http://konvajs.github.io/>
