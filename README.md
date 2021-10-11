animate.py is Python module for vapoursynth scripts that animates filters accros clip using MAP and selection.

## Basic usage:
```
import vapoursynth as vs
from vapoursynth import core
import animate

def blur(clip,*args):
    return clip.std.BoxBlur()

MAP = [   #ranges can overlap
             (60, 100),     [animate.Crossfade(None, blur)],
             (101,200),     [blur],
             (201,250),     [animate.Crossfade(blur, None)],             
       ]

clip = core.lsmas.LibavSMASHSource('source.mp4')
#if selection argument is passed: (width, height, left,top)
clip = animate.run(clip, MAP, selection=(300,200,50,90))
clip.set_output()
```
                       
## Dependencies:
vapoursynth<br>
havsfunc

## Other examples:
```
import vapoursynth as vs
from vapoursynth import core
import animate

MAP = [
             ( 0, 65),      [animate.CrossfadeFromColor((16,128,128))],
             (300, 400),    [animate.CrossfadeToColor((16,128,128))],             
       ]

clip = core.lsmas.LibavSMASHSource('source.mp4')
clip = animate.run(clip, MAP)[:400]
clip.set_output()
```
frame ranges can overlap, filters can be chained, lambda could be used without defining a function:
```
def blur(clip,*args):
    return clip.std.BoxBlur()

def darken(clip, *args):
    return clip.std.Expr(['x 50 -','',''])

def brighten(clip, *args):
    return clip.std.Expr(['x 50 +','',''])

clip = core.lsmas.LibavSMASHSource(r'D:\video.mp4')

MAP = [ 
             (300, 400),    [blur, brighten],
             (401, 600),    [darken],
             (0, 1000),     [lambda clip, n, *args: clip.text.Text(f'frame: {n}', alignment=7)]
       ]

clip = animate.run(clip, MAP)
```
crossfades to and from a solid color:
```
MAP = [
             (0,   100),   [animate.CrossfadeFromColor((16,128,128))],
             (201, 300),   [animate.CrossfadeToColor((16,128,128))]
       ]

clip = animate.run(clip, MAP)[:300]
clip.set_output()
```
