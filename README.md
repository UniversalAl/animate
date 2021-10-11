animate.py is Python module for vapoursynth scripts that animates filters accros clip using MAP and selection.

<h3>Basic usage:</h3>
<pre><code>import vapoursynth as vs
from vapoursynth import core
import animate

def blur(clip,*args):
    return clip.std.BoxBlur()

MAP = [ #ranges can overlap   #filters can be chained: [filter1,filter2,filter3 ..]
             (60, 100),     [animate.Crossfade(None, blur)],
             (101,200),     [blur],
             (201,250),     [animate.Crossfade(blur, None)],             
       ]


clip = core.lsmas.LibavSMASHSource(source.mp4)
#selection if argument passed is (width, height, left,top)
clip = animate.run(clip, MAP, selection=(300,200,50,90))
clip.set_output()</code></pre>
                       
<h3>Dependencies:</h3>
vapoursynth<br>
havsfunc<br>

<h3>Other examples:</h3>
<pre><code>import vapoursynth as vs
from vapoursynth import core
import animate

MAP = [ #ranges can overlap   #filters can be chained: [filter1,filter2,filter3 ..]
             ( 0, 65),      [animate.CrossfadeFromColor((16,128,128))],
             (300, 400),    [animate.CrossfadeToColor((16,128,128))],             
       ]

clip = core.lsmas.LibavSMASHSource(video.mp4)

clip = animate.run(clip, MAP)[:400]
clip.set_output()</code></pre>

