animate.py is a python module for vapoursynth scripts that maps filters across clip using MAP or defines crop selection for those mapped filters. Frame ranges defined in MAP could overlap, filters could be chained for a range, crossfades could be defined to and from a filter and also filter arguments could be animated separatelly if a crossfade is not appropriate

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

clip = core.lsmas.LibavSMASHSource(r'source.mp4')

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
animate filter arguments separatelly:
```
import vapoursynth as vs
from vapoursynth import core
import animate
import adjust
clip = core.lsmas.LibavSMASHSource(r'source.mp4')

TWEAK1 = dict(hue=0.0,  sat=1.3,    bright=8.0,   cont=1.1,    coring=True)
TWEAK2 = dict(hue=0.0,  sat=1.4,    bright=15.0,  cont=1.3,    coring=True)

#declare what arguments you want to animate, int of float, but None to not animate an argument:
TWEAK_TYPES = dict(hue=None, sat=float,  bright=float, cont=float,  coring=None)

def tweak1(clip,*args):
    return adjust.Tweak(clip, **TWEAK1)
    
def tweak2(clip,*args):
    return adjust.Tweak(clip, **TWEAK2)
    
MAP = [
         (0, 100),                 [tweak1],
         (101, 150),               [animate.Arguments(adjust.Tweak, TWEAK1, TWEAK2, TWEAK_TYPES)],
         (151, clip.num_frames-1), [tweak2]
      ]
clip_out = animate.run(clip, MAP)
```

some other demonstration, no source input is needed for this script:
```
import vapoursynth as vs
from vapoursynth import core
import animate

clip = core.std.BlankClip(color=(255,0,0), length=300)

def data1(clip, n, lower, upper): return clip.text.Text(f'frame: {n}   interval to print: {lower} to {upper}',alignment=7)   
def data2(clip, *args):           return clip.text.Text(f'filters can be chained', alignment=4)   
def data3(clip, *args):           return clip.text.Text('Text that is fade in and out', alignment=1)

def headline1(clip, *args):       return clip.text.Text('Our Headline')
def headline2(clip, *args):       return clip.text.Text('... and other headline')
def headline3(clip, *args):       return clip.text.Text('... third Headline')

end = clip.num_frames-1
 
MAP = [
         (0,   60),     [CrossfadeFromColor( (0,0,0) ), Crossfade(None, headline1)],
         (60, 100),     [headline1],
         (101,150),     [Crossfade(headline1, headline2)],
         (151,200),     [headline2],
         (201,250),     [Crossfade(headline2,headline3)],
         (251, end),    [headline3],
         (end-29,end),  [CrossfadeToColor( (0,0,0) )],       
         (0, end) ,     [
             #example defining functions using lambda:
             lambda clip, *args: clip.text.Text('this is always visible, because filter is positioned on the bottom in MAP', alignment=4),
             lambda clip, n, *args: clip.text.Text(f'frame: {n}', alignment=1),
                        ]
       ]

clip = run(clip, MAP)
clip.set_output()
```
