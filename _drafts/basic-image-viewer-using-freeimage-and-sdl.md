---
layout: post
title: How to build a basic image viewer using FreeImage and SDL2
comments: true
tags:
  - C
  - image analysis
---

In this blog post we will use FreeImage and SDL2 to create a basic image viewer
in C. [FreeImage](http://freeimage.sourceforge.net/) is an open source library
for working with image files. It supports over 30 file formats, gives
access to meta-data and provides basic image manipulation routines.
[SDL](https://www.libsdl.org/) (Simple DirectMedia Layer) is a cross-platform
library which provides low level access to things like the keyboard and mouse
as well as graphics hardware using OpenGL and Direct3D. SDL provides official
supports for Windows, Mac, Linux, iOS and Android.

By the end of this post we will have created a C program that can be used to
view RGB and grayscale images from the command line.


## Argument parsing

Let us start by adding some basic argument parsing. Add the code below to a
file named ``see.c``.

```c
#include <stdlib.h>
#include <stdio.h>

char *parse_args_get_filename(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Usage: %s FILENAME\n", argv[0]);
        exit(2);
    }
    char *filename = argv[1];
    return filename;
}
```


There are a few things going on in the above. We include the ``<stdlib.h>`` and
``<stdio.h>`` header files. The former provides the ``exit()`` function and the
latter the ``fprintf()`` function.

We also define the function ``parse_args_get_filename()`` that returns a
pointer to a ``char`` array, the ``char`` array that will hold our input file
name. Note that the ``argc`` variable is an integer that holds the number of
arguments supplied from the command line and ``argc`` is a list of pointers to
``char`` arrays holding the values of the strings supplied from the command
line. In our program ``argc`` will contain two such pointers, one to the name
of the program and one to the filename of the image we want to view.


## Reading in the image using FreeImage

We will now use FreeImage to read in an image. Let us start by including the FreeImage header.

```c
#include <stdlib.h>
#include <stdio.h>

#include <FreeImage.h>
```

Now let us add a function for creating a FreeImage bitmap from an image file.
The function will return a pointer to the bitmap.

```c
/** Initialise a FreeImage bitmap and return a pointer to it. */
FIBITMAP *get_freeimage_bitmap(char *filename) {
    FREE_IMAGE_FORMAT filetype = FreeImage_GetFileType(filename, 0);
    FIBITMAP *freeimage_bitmap = FreeImage_Load(filetype, filename, 0);
    return freeimage_bitmap;
}
```

Here we use the ``FreeImage_GetFileType()`` function to determine the image
file type by analysing the bitmap signature. According to the
[FreeImage documentation](http://freeimage.sourceforge.net/documentation.html)
the second parameter (``size``) is currently not in use and can
be set to ``0``.

We then use the ``FreeImage_Load()`` function to initialise the bitmap from the
input file name. The third parameter (``flags``) can be used to change the
loading behaviour for certain file types. As we are not interested in using
this functionality we can set it to ``0``.

Note that the FreeImage bitmap is flipped vertically with respect to the
coordinate system used by SDL. We will deal with this in the next section.

## Creating a SDL surface from a FreeImage bitmap

It is time to start using SDL. Let us therefore include the SDL header.

```c
#include <stdlib.h>
#include <stdio.h>

#include <FreeImage.h>

#include <SDL2/SDL.h>
```

Now we will create a function that takes our FreeImage bitmap and returns a
[``SDL_Surface``](https://wiki.libsdl.org/SDL_Surface) (a structure containing
pixel information).  To achieve this we will make use of the
[``SDL_CreateRGBSurfaceFrom()``](https://wiki.libsdl.org/SDL_CreateRGBSurfaceFrom)
function.

```c
/** Initialise a SDL surface and return a pointer to it.
 *
 *  This function flips the FreeImage bitmap vertically to make it compatible
 *  with SDL's coordinate system.
 *
 *  If the input image is in grayscale a custom palette is created for the
 *  surface.
 */
SDL_Surface *get_sdl_surface(FIBITMAP *freeimage_bitmap, int is_grayscale) {

    // Loaded image is upside down, so flip it.
    FreeImage_FlipVertical(freeimage_bitmap);

    SDL_Surface *sdl_surface = SDL_CreateRGBSurfaceFrom(
        FreeImage_GetBits(freeimage_bitmap),
        FreeImage_GetWidth(freeimage_bitmap),
        FreeImage_GetHeight(freeimage_bitmap),
        FreeImage_GetBPP(freeimage_bitmap),
        FreeImage_GetPitch(freeimage_bitmap),
        FreeImage_GetRedMask(freeimage_bitmap),
        FreeImage_GetGreenMask(freeimage_bitmap),
        FreeImage_GetBlueMask(freeimage_bitmap),
        0 
    );

    if (sdl_surface == NULL) {
        fprintf(stderr, "Failed to create surface: %s\n", SDL_GetError());
        exit(1);
    }

    if (is_grayscale) {
        // To display a grayscale image we need to create a custom palette.
        SDL_Color colors[256];
        int i;
        for(i = 0; i < 256; i++) {
            colors[i].r = colors[i].g = colors[i].b = i;
        }
        SDL_SetPaletteColors(sdl_surface->format->palette, colors, 0, 256);
    }

    return sdl_surface;
}
```

We start off by flipping the bitmap vertically. Since this is done in memory it
is a side-effect of the function.

We then create the SDL surface using the
[``SDL_CreateRGBSurfaceFrom()``](https://wiki.libsdl.org/SDL_CreateRGBSurfaceFrom)
function, which (amongst others) takes as input the red, green and blue masks
of the FreeImage bitmap. The functions for accessing these masks
(``FreeImage_GetRedMask()``, etc) work even if the FreeImage bitmap comes from a
single channel input image (gray scale). If the input image is in gray scale we
therefore need to create a custom palette for it and associate this with the
SDL surface that we have created.

## Creating a SDL window

Our image viewer will display the image in a SDL window. For sake of
minimalism (and simplicity) this will be a border-less window displayed in the
centre of the screen.

```c
/** Initialise a SDL window and return a pointer to it. */
SDL_Window *get_sdl_window(int width, int height) {
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        fprintf(stderr, "SDL couldn't initialise: %s.\n", SDL_GetError());
        exit(1);
    }

    SDL_Window *sdl_window;
    sdl_window = SDL_CreateWindow( "Image",
        SDL_WINDOWPOS_CENTERED,
        SDL_WINDOWPOS_CENTERED,
        width,
        height,
        SDL_WINDOW_BORDERLESS);

    return sdl_window;
}
```

## Rendering the surface as a texture in the window (a.k.a. displaying the image)

We now need some code to render the surface as a texture in the window. In the
code we will do this back to front, by using the window to generate a renderer
and using the renderer to generate a texture. Finally, the renderer is cleared
before adding the texture and presenting it.

It is worth noting that a
[``SDL_Texture``](https://wiki.libsdl.org/SDL_Texture) is <q>a structure that
contains an efficient, driver-specific representation of pixel data</q>.
Which means that, unlike a
[``SDL_Surface``](https://wiki.libsdl.org/SDL_Surface),
it can be processed by the GPU.


```c
/** Display the image by rendering the surface as a texture in the window. */
void render_image(SDL_Window *window, SDL_Surface *surface) {
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    if ( renderer == NULL ) {
        fprintf(stderr, "Failed to create renderer: %s\n", SDL_GetError());
        exit(1);
    }

    SDL_Texture* texture = SDL_CreateTextureFromSurface(renderer, surface);
    if ( texture == NULL ) {
        fprintf(stderr, "Failed to load image as texture\n");
        exit(1);
    }

    SDL_RenderClear(renderer);
    SDL_RenderCopy(renderer, texture, NULL, NULL);
    SDL_RenderPresent(renderer);
}
```

Note that the third parameter of the
[``SDL_RenderCopy()``](https://wiki.libsdl.org/SDL_CreateTextureFromSurface)
function (``srcrect``) is a pointer to the source rectangle and can be used to
implement zooming. However, here we set it to ``NULL`` to display the entire
texture.

## Giving the user the chance to view the image

At this point we need some sort of event loop to make sure that the image does
not vanish instantaneously after having been rendered in the window. Below is
is a simple event loop that ends when the user presses a key on the keyboard.

```c
/** Loop until a key is pressed. */
void event_loop() {
    int done = 0;
    SDL_Event e;
    while (!done) {
        while (SDL_PollEvent(&e)) {
            if (e.type == SDL_KEYDOWN) {
                done = 1;
            }
        }
    }
}
```

## Putting it all together

Finally we add the main logic of the code. This includes some functionality for
checking if the input image is gray scale. We achieve this by checking if the
FreeImage colour type is ``FIC_MINISBLACK``. Other colour types include
``FIC_RGB`` and ``FIC_CMYK``.

As gray scale images can be more than 8-bits (quite common when dealing with
microscopy images) we make sure that we compress the data using the
``FreeImage_ConvertToGreyscale()`` function.

```c
int main(int argc, char *argv[]) {
    char *filename = parse_args_get_filename(argc, argv);
    FIBITMAP *freeimage_bitmap = get_freeimage_bitmap(filename);

    int is_grayscale = 0;
    if (FreeImage_GetColorType(freeimage_bitmap) == FIC_MINISBLACK) {
        // Single channel so ensure image is compressed to 8-bit.
        is_grayscale = 1;
        FIBITMAP *tmp_bitmap = FreeImage_ConvertToGreyscale(freeimage_bitmap);
        FreeImage_Unload(freeimage_bitmap);
        freeimage_bitmap = tmp_bitmap;
    }

    int width = FreeImage_GetWidth(freeimage_bitmap);
    int height = FreeImage_GetHeight(freeimage_bitmap);
    SDL_Window *sdl_window = get_sdl_window(width, height);
    SDL_Surface *sdl_surface = get_sdl_surface(freeimage_bitmap, is_grayscale);

    render_image(sdl_window, sdl_surface);
    event_loop();

    FreeImage_Unload(freeimage_bitmap);
    SDL_FreeSurface(sdl_surface);
    return 0;
}
```

Note that we free up the dynamically allocated bitmap and surface memory using
``FreeImage_Unload()`` and
[``SDL_FreeSurface()``](https://wiki.libsdl.org/SDL_FreeSurface)
before we exit the program.


## Compiling and linking

Now we can compile the code.

```
$ gcc -c see.c
```

This creates the object file ``see.o`` which contains machine code as well as
information that allows a linker to find out which symbols (global objects,
functions, etc) it requires in order to work.

Let us link our object file.

```
$ gcc -o see see.o -lfreeimage -lsdl2
```

This produces the executable file ``see``, which we can test using the command
below.

```
$  ./see image.png
```

## Conclusion

FreeImage and SDL are useful C libraries for working with images and graphical
user interfaces, respectively. In this post we have used the two in combination
to create a basic image viewer that can parse over 30 image file formats and
display both RGB and gray scale images correctly.

## Acknowledgements

This blog post was inspired by and based on some of the code in the
[github.com/JIC-CSB/eye](https://github.com/JIC-CSB/eye) project.
