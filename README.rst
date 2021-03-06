pyNES
=====


The Legend
----------

There was a time when game cartridges were forged in the fire of mount doom itself. That great power was then
trapped into a regular plastic shelf. Most of the secrets were sealed by fellowship of hardcore game programmers.
Their names were concealed in end-game credits from games that were never supposed to be finished. Countless
game lives were wasted in the first level, in fruitless attempts of unveiling their evil[1] spell.

That was what my curious and inventive mind believed for years, and still do so. As a kid, I used to play those
games and always asked myself how they were done. I really wanted to experience some of the game design problems
the pioneers once faced. Back then, they had to wage their own tools, hack the specs for game effects and layout
the memory mapper circuits. I figured out that to reach mount doom as equal, foremost, I had to forge my own
hammer. Therefore, I decided to trail their footmarks by building PyNES: A Python ASM compiler for Nintendo 8 bits.

However, as I strum steps progresses, the anvil didn't sound the same. Knowledge weight has changed. Internet
made it all available and communities are helpful. Also, computer power has grown and programming languages
have evolved. I must go further in each step of their challenges. PyNES is turning into a high-level compiler
which will allow Nintendo games to be written mostly in Python. This lecture will explain the several hacks and
drawbacks of such approach. And I must say, trying to compile a such evolved language to a such limited
processor as the c6502 isn't MADNESS. It's pyNES!


The Untold Story
----------------

`pyNES <http://gutomaia.net/pyNES>` started as a regular 6502 assembler. However, writing games in ASM wasn't fun enough. Thus, using some AST hacks I tried to figure out a way of translating Python code into ASM.


Release notes
-------------

 - pyNES versions 0.1.x is released as a Proof of concept.


Installation
-----------

Just use pip. It will give you a "pynes" command.

.. code-block:: bash

    pip install pyNES


Hello, world
------------

A simple "Hello, world" example. Given the example "hello.py"

.. code-block:: python


    import pynes
    from pynes.bitbag import *

    palette = [
        0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,
        0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,
        0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15
    ]

    chr_asset = import_chr('player.chr')

    sprite = define_sprite(128, 128, 0, 3)

    def reset():
        global palette, sprite
        wait_vblank()
        clearmem()
        wait_vblank()
        load_palette(palette)
        load_sprite(sprite, 0)

Compile it with:

.. code-block:: shell

  pynes py hello.py -o hello.nes

Now you can open hello.nes


**How does it work?**


In the above example, ``palette``, ``chr_asset`` and ``sprite`` are constants.
Each one has their own properties, since they are equally evaluated.
 * ``palette`` is an int array. Int Arrays are static, and can't be changed. 
 * ``chr_asset`` Are reading structures.
 * ``sprite`` is the sprite definition.

Functions are provided by bitbag package. Bitbag deals with templating[1] and some surrounding aspects needed by the asm code.


[1] Read "That's not all" at the end


That's not all folks
--------------------

** pyNES 0.1.x **

Despite all my efforts, the pyNES version 0.1.x had several limitations as it should as a proof of concept.

Tricky limitations:
 * Sprite collision
 * Scrolling Screen
 * Sprite animation
 * Better joystick support
 * Hard to extend

Being ``Hard to extend``



** pyNES 0.2.x **

Therefore, pyNES version 0.2.x must overcome those limitations. And so far it is going great.

Project has been split into 4 projects:
 * ``lexical`` - just the lexical analyzer
 * ``nesasm_py`` - a 6502 ASM compiler based on NESASM
 * ``pyNES`` - This project, wich must restrict it's responsibility just to
 * ``pyNES_StdLib`` - Standard Library.

Mantras:
 - No more templating.
 - Less gaps between what you are writing and what the compiler is doing.
 - Easier to extend

Hi Level Functions are not templated anymore. However, th

Example of waitvblank function:

.. code-block:: python

    @asm_function
    def waitvblank():
        BIT('$2002')
        BPL(waitvblank)
        RTS()

That must be translated to:

.. code-block:: asm

    waitvblank:
    BIT $2002
    BPL waitvblank
    RTS
