---
layout: post
title: React Canvas Component
date: 2017-04-20
---

This is just a quick post to show some typescript code that implements a React canvas component. It's a bit opionated in that I am using an Rx stream for the image data being displayed.  It's a stateless/dumb component that displays low level image data.  Hit me  up in the comments if you have any questions.

### Typescript React Canvas Component 
* Updated on 10 May 2017 to a more efficient implementation *
{% highlight typescript linenos %}
import * as React from 'react';
import * as Rx from 'rx';
import * as ReactDOM from 'react-dom';

export class ReactiveCanvasImage {
    constructor(
        public width: number, //assuming stride to be equal width
        public height: number,
        public pixels: number[]) { } //pixels is an array of bytes
}

// Defines the properties of the ReactiveCanvas
// It is important to have width and height pre-defined for a
// high frame rate. If width and height are reset per image in
// the imageStream then frame rate suffers.
// height: in pixels
// width: in pixels
// imageMIMEType: e.g. 'image/jpeg'
// imageStream: and observable of ReactiveCanvasImage to display
export interface IReactiveCanvasProps {
    height: number;
    width: number;
    imageMIMEType: string;
    imageStream: Rx.Observable<ReactiveCanvasImage>;
}

export class ReactiveCanvas extends React.Component<IReactiveCanvasProps, undefined> {
    private disp: Rx.IDisposable;
    private ctx: CanvasRenderingContext2D;
    private canvas: HTMLCanvasElement;

    refs: {
        canvas: HTMLCanvasElement;
    };

    componentDidMount() {
        try {
            this.canvas = (ReactDOM.findDOMNode(this.refs.canvas) as HTMLCanvasElement);
            this.ctx = this.canvas.getContext('2d')!; //trailing bang removes null or undefined from the expression
            this.canvas.width = this.props.height;
            this.canvas.height = this.props.width;

            this.disp =
                this.props.imageStream
                    .throttle(20) //this is necessary since node/js is single threaded. Without it there is no time for anything else to process.
                    .subscribe(i => {
                        this.updateCanvas(i);
                    });
        } catch (error) {
            console.log(error);
        }
    }

    componentWillUnmount() {
        this.disp.dispose();
    }

    shouldComponentUpdate() {
        return false;
    }

    render() {
        return (
            <div className='center-children'>
                <canvas ref='canvas' className='render-canvas'></canvas>
            </div>
        );
    }

    clear() {
        this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    }

    // updateCanvas is called for each frame to be rendered to the canvas.
    updateCanvas(reactiveImage: ReactiveCanvasImage) {
        try {
            let img = new Image();
            let blob = new Blob([reactiveImage.pixels], { type: this.props.imageMIMEType });
            let url = URL.createObjectURL(blob);

            img.onload = () => {
                let startTime = Date.now();
                this.clear();
                this.ctx.drawImage(img, 0, 0);
                URL.revokeObjectURL(url);
                console.log('Time to draw/load img: ' + (Date.now() - startTime));
            };

            img.src = url;
        } catch (error) {
            console.log(error);
        }
    }
}

{% endhighlight %}

You will need to run these npm installs on your project to add the dependencies. If you don't have typescript installed you will need that too.

```
npm install react react-dom rx --save
npm install @types/react @types/react-dom @types/rx --save-dev
```