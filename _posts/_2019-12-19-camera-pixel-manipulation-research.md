My research on reading and writing pixel info from camera<!--more-->

### Books:
- Learning AV Foundation

### Frameworks:
- A simple app that grabs raw camera data, converts to textures and renders on screen using Metal.
https://github.com/navoshta/MetalRenderCamera
https://navoshta.com/metal-camera-part-1-camera-session/

### Gotchas:
- shared instance of CIContext to reuse for each image

### Resources:
- Can get pixel from climate: but messy code
https://stackoverflow.com/questions/54354138/how-can-you-make-a-cvpixelbuffer-directly-from-a-ciimage-instead-of-a-uiimage-in

- Accelerate and pixels:
https://stackoverflow.com/questions/55093326/averaging-the-color-of-pixels-with-accelerate¨

- Initing pixelbuffer with vImage framework (might be faster)
https://stackoverflow.com/questions/20401763/how-do-i-use-the-accelerate-framework-with-core-graphics

- Applies filter to video pixel data (vImage):
https://developer.apple.com/documentation/accelerate/reading_from_and_writing_to_core_video_pixel_buffers

- Fast channel splitting: (Quanitization)
https://developer.apple.com/documentation/accelerate/real-time_video_effects_with_vimage

- Avfoundation to vImage
https://developer.apple.com/documentation/accelerate/applying_vimage_operations_to_video_sample_buffers

- Metal accelerated video RGBA processing:
https://github.com/BradLarson/GPUImage3 ⭐

- BytePixel Pixel RGB utils:
https://github.com/skyfe79/SwiftImageProcessing

- Very outdated apple capture, process and write example:
https://developer.apple.com/library/archive/samplecode/RosyWriter/Introduction/Intro.html#//apple_ref/doc/uid/DTS40011110-Intro-DontLinkElementID_2

- Get pixel data from .mp4 files (perfet for CI/UnitTests)
https://www.objc.io/issues/23-video/core-image-video/

- Converting of 32 bits RGB to 32 bits RGBA
https://stackoverflow.com/a/51380146/5389500

- Cropping CVPixelBuffer
https://github.com/hollance/CoreMLHelpers/blob/master/CoreMLHelpers/CVPixelBuffer%2BHelpers.swift

- lots of buffer methods:
https://github.com/hollance/CoreMLHelpers/tree/master/CoreMLHelpers

- exotic buffer format: for core ml
https://stackoverflow.com/a/58647596/5389500

```swift
dispatch_apply(height, dispatch_get_global_queue(0, 0), ^(size_t y) {
    for (size_t x = 0; x < width; x += 2) {
        // Do something with x and y here
    }
});

// add the loop above to the one bellow 🏀

DispatchQueue.concurrentPerform(iterations: self.width) { x in

}
```
- https://stackoverflow.com/questions/18049219/is-dispatch-apply-synchronous-or-asynchronous
the work performed during each iteration is distinct from the work performed during all other iterations, and the order in which each successive loop finished is unimportant


### Ideas:
another approach is spliting your image into seperate components and sending them to different threads and recombining the resulting arrays. Youll want to use gcd * workitems async to do this.

### Payed resources:
https://www.raywenderlich.com/5428948-core-image-from-ciimage-to-metal-and-beyond/ ⭐
https://leanpub.com/coreml-survival-guide/ ⭐
https://store.raywenderlich.com/products/machine-learning-by-tutorials
https://store.raywenderlich.com/products/metal-by-tutorials
https://www.raywenderlich.com/5428948-core-image-from-ciimage-to-metal-and-beyond ⭐⭐⭐
