# Programming Pictures with Go

## Authors
- Anthony Starks, http://github.com/ajstarks

## Description

This entry uses three separate projects that allow artists and coders to "make pictures" using the the Go programming language.  The projects are:

* SVGo: a Go library for SVG generation
* OpenVG: a high-level API to the OpenVG standard, developed on the Raspberry Pi
* Deck: a Go package for making and serving scalable presentions using a standard markup language

SVGo was created to automate the generation of SVG, while preserving the standard SVG semantics and philosophy.
SVGo is useful for rendering locally in a browser, in fact using a local server writter in Go, the artist/coder may work
with code+picture interactively.

OpenVG is a higher-level interface to the standard API, designed to leverage the GPU and allow programmers to "get to the picture" quickly.  

Deck was made to explore what makes a compelling presentation, using a standard, lasting and portable markup language. 
Deck uses minimal markup elements, with a strict percentage-based layout system.  
Deck also explores the possibilities of machine-generated presentations, rendering slides from data.
Also included is a RESTful API for upload, play, stop, removal, and generation of slide content.


## Links

* [SVGo: a Go library for SVG generation](http://github.com/ajstarks/svgo)
* [OpenVG: a high-level API to the OpenVG standard, developed on the Raspberry Pi](http://github.com/ajstarks/openvg)
* [Deck: a Go package for making and serving scalable presentions using a standard markup language](github.com/ajstarks/deck)



## Example Code

SVGo minimal program, writing to standard output
```go
package main

import (
    "github.com/ajstarks/svgo"
    "os"
)

func main() {
    width := 500
    height := 500
    canvas := svg.New(os.Stdout)
    canvas.Start(width, height)
    canvas.Circle(width/2, height/2, 100)
    canvas.Text(width/2, height/2, "Hello, SVG", "text-anchor:middle;font-size:30px;fill:white")
    canvas.End()
}

```


OpenVG hello world
```go
package main

import (
    "bufio"
    "github.com/ajstarks/openvg"
    "os"
)

func main() {
    width, height := openvg.Init() // OpenGL, etc initialization

    w2 := openvg.VGfloat(width / 2)
    h2 := openvg.VGfloat(height / 2)
    w := openvg.VGfloat(width)

    openvg.Start(width, height)                               // Start the picture
    openvg.BackgroundColor("black")                           // Black background
    openvg.FillRGB(44, 77, 232, 1)                            // Big blue marble
    openvg.Circle(w2, 0, w)                                   // The "world"
    openvg.FillColor("white")                                 // White text
    openvg.TextMid(w2, h2, "hello, world", "serif", width/10) // Greetings 
    openvg.End()                                              // End the picture
    bufio.NewReader(os.Stdin).ReadBytes('\n')                 // Pause until [RETURN]
    openvg.Finish()                                           // Graphics cleanup
}
```

Sample Deck
```xml
<deck>
    <title>Sample Deck</title>
    <canvas width="1024" height="768"/>
    <slide bg="maroon" fg="white" duration="1s">
        <image xp="20" yp="30" width="256" height="256" name="picture.png"/>
        <text xp="20" yp="80" sp="3" link="http://example.com/">Deck uses these elements</text>
        <line xp1="20" yp1="75" xp2="90" yp2="75" sp="0.3" color="rgb(127,127,127)"/>
        <list xp="20" yp="70" sp="1.5">
            <li>canvas</li>
            <li>slide</li>
            <li>text</li>
            <li>list</li>
            <li>image</li>
            <li>line</li>
            <li>rect</li>
            <li>ellipse</li>
            <li>curve</li>
            <li>arc</li>
        </list>
        <line    xp1="20" yp1="10" xp2="30" yp2="10"/>
        <rect    xp="35"  yp="10" wp="4" hp="3" color="rgb(127,0,0)"/>
        <ellipse xp="45"  yp="10" wp="4" hp="3" color="rgb(0,127,0)"/>
        <curve   xp1="60" yp1="10" xp2="75" yp2="20" xp3="70" yp3="10" />       
        <arc     xp="55"  yp="10" wp="4" hp="3" a1="0" a2="180" color="rgb(0,0,127)"/>
    </slide>
</deck>
```

and a sample deck client
```go
package main

import (
	"fmt"
	"log"

	"github.com/ajstarks/deck"
)

func dotext(x, y, size float64, text deck.Text) {
	fmt.Println("\tText:", x, y, size, text.Tdata)
}

func dolist(x, y, size float64, list deck.List) {
	fmt.Println("\tList:", x, y, size)
	for i, l := range list.Li {
		fmt.Println("\t\titem", i, l)
	}
}
func main() {
	presentation, err := deck.Read("deck.xml", 1024, 768) // open the deck
	if err != nil {
		log.Fatal(err)
	}
	for slidenumber, slide := range presentation.Slide { // for every slide...
		fmt.Println("Processing slide", slidenumber)
		for _, t := range slide.Text { // process the text elements
			x, y, size := deck.Dimen(presentation.Canvas, t.Xp, t.Yp, t.Sp)
			dotext(x, y, size, t)
		}
		for _, l := range slide.List { // process the list elements
			x, y, size := deck.Dimen(presentation.Canvas, l.Xp, l.Yp, l.Sp)
			dolist(x, y, size, l)
		}
	}
}
```

## Images & Videos

### SVGo

* [Examples of SVGo](http://www.flickr.com/photos/ajstarks/sets/72157623441699483/) and related output like
output from Layer Tennis Remixes, 
[Season 1](http://www.flickr.com/photos/ajstarks/sets/72157625428100068/), 
[Season 2](http://www.flickr.com/photos/ajstarks/sets/72157625302560593/) and 
[Season 3](http://www.flickr.com/photos/ajstarks/sets/72157624950756818/)
* [A Deck on SVGo](https://speakerdeck.com/ajstarks/programming-pictures-with-svgo), along with 
[workshop materials](https://speakerdeck.com/ajstarks/svgo-workshop)

#### SVGo Blog posts

* [AIGA Symbols](http://mindchunk.blogspot.com/2012/07/aiga-symbols.html)
* [Raspi and SVGo](http://mindchunk.blogspot.com/2012/06/raspberry-pi-and-svgo-tools.html)
* [Tumblr Grid](http://mindchunk.blogspot.com/2012/04/tumblr-grid.html)
* [Documenting Code and Pictures](http://mindchunk.blogspot.com/2011/10/documenting-code-and-pictures.html)
* [Stockproduct](http://mindchunk.blogspot.com/2011/08/stockproduct-comparisons-with-svg.html)
* [Bulletgraphs](http://mindchunk.blogspot.com/2011/07/bullet-graphs.html)
* [SVGo and Google Web Fonts](http://mindchunk.blogspot.com/2011/05/svgo-and-google-web-fonts.html)
* [Mobile Market Share 2010](http://mindchunk.blogspot.com/2010/11/mobile-market-share-2010.html)
* [Proportional maps, an alternative to pie charts](http://mindchunk.blogspot.com/2010/10/proportional-maps-alternative-to-pie.html)
* [Font specimens with SVG](http://mindchunk.blogspot.com/2010/04/font-specimens-with-svg.html)
* [Leaning flowers](http://mindchunk.blogspot.com/2010/03/leaning-flowers.html)


 a Video showing an early version of SVGo https://www.youtube.com/watch?v=ze6O2Dj5gQ4

### OpenVG

* [Samples from the OpenVG library](http://www.flickr.com/photos/ajstarks/sets/72157630913689774/)
* [Related blog post](http://mindchunk.blogspot.com/2012/09/openvg-on-raspberry-pi.html)


### Deck

* [The Deck on Deck](https://speakerdeck.com/ajstarks/deck-a-go-package-for-presentations)
* [Flickr Set for Examples of Deck](http://www.flickr.com/photos/ajstarks/sets/72157634452348637/)
* [Video describing an early deck client](http://vimeo.com/69357909)


