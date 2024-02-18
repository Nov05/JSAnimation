
# **👉 Changed by Nov05**  

**Installation from this repo**  
```
!git clone https://github.com/Nov05/JSAnimation.git
!pip install git+file://./JSAnimation
!rm -r ./JSAnimation
```

**Change logs**   
2024-02-17 fixed compatibility issues with `matplotlib>2.1.0`  
* Issue solved: [`anim.save(filename, fps=20, clear_temp=False) TypeError: save() got an unexpected keyword argument 'clear_temp'.`](https://github.com/qutip/qutip/issues/798)  
  Solution: change file `./JSAnimation/html_writer.py`  
```
        super(HTMLWriter, self).setup(fig, outfile, dpi,
                                      # frame_prefix, clear_temp=False) ## changed by nov05 on 2024-02-17
                                      frame_prefix)
```

* Issue solved: [AttributeError: 'HTMLWriter' object has no attribute '_temp_names'](https://colab.research.google.com/corgiredirector?site=https%3A%2F%2Fteratail.com%2Fquestions%2F280493)  
  Solution: change file `./JSAnimation/html_writer.py`  
```
    def _run(self):
        # make a ducktyped subprocess standin
        # this is called by the MovieWriter base class, but not used here.
        class ProcessStandin(object):
            returncode = 0
            def communicate(self):
                # return ('', '') ## changed by nov05 on 2024-02-17
                return (''.encode('utf-8'), ''.encode('utf-8'))
```
```
        with open(self.outfile, 'w') as of:
            of.write(JS_INCLUDE)
            of.write(DISPLAY_TEMPLATE.format(id=self.new_id(),
                                             # Nframes=len(self._temp_names), ## changed by nov05 on 2024-02-17
                                             Nframes=len(self._temp_paths),
```


JSAnimation
===========

**NOTE: this package is part of matplotlib as of version 2.1. The code here is maintained only for historical purposes.**

*Copyright (c) 2013, Jake Vanderplas*

*License: [BSD](http://opensource.org/licenses/BSD-2-Clause)*

This package implements an HTML/Javascript writer for Matplotlib animations.
The result can be embedded in a web page, or an IPython notebook.

Example
-------
See a [rendered example](http://nbviewer.ipython.org/urls/raw.github.com/jakevdp/JSAnimation/master/animation_example.ipynb): please note the browser
requirements listedbelow.

Testing it Out
--------------
There are several scripts which demonstrate this.  ``make_animation.py``
creates a basic animation, with the frame data embedded within the HTML
document.  Run this using

    python make_animation.py

and then open the resulting file, ``animation.html`` using your web browser.
This file is created using the option ``embed = True``, which means that the
image data is embedded directly in the html file in base64-representation.
This means that the animation is entirely self-contained in the file.

A more sophisticated animation can be created using

    python make_lorenz_animation.py

(adapted from [this blog post](http://jakevdp.github.io/blog/2013/02/16/animating-the-lorentz-system-in-3d/)
The resulting file, ``lorenz_animation.html``,
can be opened in your web browser.
This animation is created using the option ``embed = False``, which means that
the frames are individually stored in the directory ``lorenz_animation_frames``.
This prevents the html file from being too large.

If you have IPython notebook installed, you can open ``animation_example.ipynb``
to see the automatic animation representation within the notebook.  Simply
import the ``IPython_display`` submodule, create the animation, and the
javascript widget will appear embedded in your notebook window.

Browser Compatibility
---------------------
Because the animation widget uses an HTML5 slider element, it does not work
in some browsers.  For a comprehensive list of supported browsers, see
[this list](http://caniuse.com/input-range).
