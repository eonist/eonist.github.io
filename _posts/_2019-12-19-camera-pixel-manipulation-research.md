My research on reading and writing pixel info from camera<!--more-->

### Books:
- Learning AV Foundation

### Frameworks:
- A simple app that grabs raw camera data, converts to textures and renders on screen using Metal.
https://github.com/navoshta/MetalRenderCamera
https://navoshta.com/metal-camera-part-1-camera-session/

### Resources:
- lots of buffer methods (reading writing from buffer): 👌
https://github.com/hollance/CoreMLHelpers/tree/master/CoreMLHelpers ⭐⭐

- Cropping CVPixelBuffer 👌
https://github.com/hollance/CoreMLHelpers/blob/master/CoreMLHelpers/CVPixelBuffer%2BHelpers.swift ⭐

- Metal accelerated video RGBA processing: 👌
https://github.com/BradLarson/GPUImage3 ⭐

- Can get pixel from CIImage: but messy code 🚫
https://stackoverflow.com/questions/54354138/how-can-you-make-a-cvpixelbuffer-directly-from-a-ciimage-instead-of-a-uiimage-in

- Accelerate and pixels: 🤔
https://stackoverflow.com/questions/55093326/averaging-the-color-of-pixels-with-accelerate

- Initing pixelbuffer with vImage framework (might be faster) 🤔
https://stackoverflow.com/questions/20401763/how-do-i-use-the-accelerate-framework-with-core-graphics

- Applies filter to video pixel data (vImage): 🤔
https://developer.apple.com/documentation/accelerate/reading_from_and_writing_to_core_video_pixel_buffers

- Fast channel splitting: (Quantisation) 🤔
https://developer.apple.com/documentation/accelerate/real-time_video_effects_with_vimage

- Avfoundation to vImage 🤔
https://developer.apple.com/documentation/accelerate/applying_vimage_operations_to_video_sample_buffers

- BytePixel Pixel RGB utils: 👌
https://github.com/skyfe79/SwiftImageProcessing

- Very outdated apple capture, process and write example:
https://developer.apple.com/library/archive/samplecode/RosyWriter/Introduction/Intro.html#//apple_ref/doc/uid/DTS40011110-Intro-DontLinkElementID_2

- Get pixel data from .mp4 files (perfet for CI/UnitTests)
https://www.objc.io/issues/23-video/core-image-video/

- Converting of 32 bits RGB to 32 bits RGBA
https://stackoverflow.com/a/51380146/5389500

- exotic buffer format: for core ml (planes etc)
https://stackoverflow.com/a/58647596/5389500

### Payed resources:
https://www.raywenderlich.com/5428948-core-image-from-ciimage-to-metal-and-beyond/ ⭐
https://leanpub.com/coreml-survival-guide/ ⭐
https://store.raywenderlich.com/products/machine-learning-by-tutorials
https://store.raywenderlich.com/products/metal-by-tutorials
https://www.raywenderlich.com/5428948-core-image-from-ciimage-to-metal-and-beyond ⭐⭐⭐
Metal Programming Guide: Tutorial and Reference via Swift chapter 15: Parallel Programming

### Gotchas:
- shared instance of CIContext to reuse for each image
- Dealloc CGImage creation with autorelease

### Ideas:
another approach is spliting your image into seperate components and sending them to different threads and recombining the resulting arrays. Youll maybe want to use gcd * workitems async to do this.

### PixelFormat:
kCVPixelFormatType_24RGB, kCVPixelFormatType_24BGR, kCVPixelFormatType_32ARGB, kCVPixelFormatType_32BGRA, kCVPixelFormatType_32ABGR, kCVPixelFormatType_32RGBA all of these would be the best options and is usually the most COMMON options (IE: 24-bit bitmap, 24-bit PNG, 32-bit bitmap, 32-bit PNG, etc).

Basically, 24-bit only contains R, G, B, pixel components and the alpha channel is completely missing. 32-bit contains an alpha channel so: R, G, B, A, components would be used. Usually 24-bit works really well on Tesseract and 32-bit works really well when the alpha channel is transparent (0x0 or 0xFF for all bytes). This is equivalent to using BMP or PNG format.
