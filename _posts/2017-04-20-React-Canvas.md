---
layout: post
title: React Canvas Component
date: 2017-04-20
---

This is just a quick post to show some typescript code that implements a React canvas component. It's a bit opionated in that I am using an Rx stream for the image data being displayed.  It's a stateless/dumb component that displays low level image data.  Hit me  up in the comments if you have any questions.

### Typescript React Canvas Component 
{% highlight typescript linenos %}
import * as React from 'react';
import * as Rx from 'rx';
import * as ReactDOM from 'react-dom';

export type PixelRepresentation = 'GrayScale8bpp'; //| GrayScale16bpp | add others representations here

export class Image {
    constructor(
        public width: number, //assuming stride to be equal width
        public height: number,
        public pixelRep: PixelRepresentation, //defines what each byte in pixels means
        public pixels: number[]) { } //pixels is an array of bytes
}

export interface IReactiveCanvasProps {
    imageStream: Rx.Observable<Image>;
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
            let c = this.canvas.getContext('2d');
            //this is super wonky. Wonder if there is a better way
            //to deal with null possibility in the union return.
            if (c == null) {
                throw new ReferenceError('Cannot get canvas context');
            } else {
                this.ctx = c;
            }
            
            this.disp =
                this.props.imageStream
                    .subscribe(i => {
                        this.clear();
                        this.updateCanvas(i);
                    });
        } catch (error) {
            console.log(error);
        }
    }

    componentWillUnmount() {
        this.disp.dispose();
    }

    render() {
        return (
            <div>
                <canvas ref='canvas'></canvas>
            </div>
        );
    }

    clear() {
        this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    }

    // updateCanvas is called for each frame to be rendered to the canvas.
    updateCanvas(img: Image) {
        try {
            let startTime = Date.now();

            this.canvas.width = img.width;
            this.canvas.height = img.height;

            let imgData = this.ctx.createImageData(img.width, img.height);

            for (let idx = 0; idx < img.pixels.length; idx++) {
                let x = idx % img.width;
                let y = Math.floor(idx / img.width);
                let pixIdx = ((x + y * img.width) * 4);
                this.setPixel(imgData, pixIdx, img.pixels[idx], img.pixelRep);
            }
            this.ctx.putImageData(imgData, 0, 0);

            let endTime = Date.now() - startTime;
            console.log('Frame Render: ' + endTime + 'ms');

        } catch (error) {
            console.log(error);
        }
    }

    // setPixel sets the individual pixel value on the passed in ImageData obect.
    // ImageData is what gets rendered to the canvas after the pixels have been set.
    // How each pixel is set depends on the PixelRepresentation
    setPixel(img: ImageData, pixIdx: number, color: number, pixelRep: PixelRepresentation) {

        switch (pixelRep) {
            case 'GrayScale8bpp':
                this.setPixelGrayScale8bpp(img.data, pixIdx, color);
                break;
            //add other PixelRepresentation here:
            default:
                throw new Error('Invalid PixelRepresentation');
        }
    }

    setPixelGrayScale8bpp(pixel: Uint8ClampedArray, startIndex: number, color: number) {
        pixel[startIndex] = color;      //R
        pixel[startIndex + 1] = color;  //G
        pixel[startIndex + 2] = color;  //B
        pixel[startIndex + 3] = 255;    //A
    }
}

{% endhighlight %}

You will need to run these npm installs on your project to add the dependencies. If you don't have typescript installed you will need that too.

```
npm install react react-dom rx --save
npm install @types/react @types/react-dom @types/rx --save-dev
```