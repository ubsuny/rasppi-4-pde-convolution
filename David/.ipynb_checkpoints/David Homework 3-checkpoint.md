# Homework 3

## Problem 1

Timing the function was a simple as using the time command. The program runs through three nested for loops, one for each time step and the others for convolution. Overall, this method takes roughly 7 seconds to run. This is not necessary, as there are a few methods to use conulution without taking time to iterate. Scipy's signal module has a plethora of convultion functions each with a few modes and methods. I took use of the convolve() function testing out their different methods. The direct method calculates the convolution as per the definition of the convolution operation. This method took the average of 7 seconds down to 120ms, over 58 times faster. Instead of for loops, it takes a cross correlation of the matrices (only if the volume and kernal are not already 1D), for our kernel, cross correlation and convolution are the same operation because the difference is simply "flipping" the matrix, and our kernel's elements are symmetric.

However, convulation can be performed with fourier transforms, and in this case the performance is faster. When using this method, it now only takes roughly 75ms, now over 93 times faster than the orignal method, giving the same results as expected. Here can be seen the handling of the methods in the convolve source code:

    if method == 'fft':
        out = fftconvolve(volume, kernel, mode=mode)
        result_type = np.result_type(volume, kernel)
        if result_type.kind in {'u', 'i'}:
            out = np.around(out)
        return out.astype(result_type)
    elif method == 'direct':
        # fastpath to faster numpy.convolve for 1d inputs when possible
        if _np_conv_ok(volume, kernel, mode):
            return np.convolve(volume, kernel, mode)

        return correlate(volume, _reverse_and_conj(kernel), mode, 'direct')

Here, fftconvolve() can be found at 

[https://github.com/scipy/scipy/blob/5ab7426247900db9de856e790b8bea1bd71aec49/scipy/signal/signaltools.py#L551]

## Problem 2

When first attempting to look at hardware specific optimizations, looking at GPGPU proves to be not quite useful. Quite searches reveal that the Raspberry 4 utilizes the Broadcom VideoCore VI as its GPU, and unfortunately, many of the libraries that utilized GPGPU are incompatible with the videocard. 

The other approach is utilizing the CPU and having libraries optimized for the architechure of the raspberry. In general, libraries around convolution are not often used for the raspberry pi 4, as other hardwares are more suitable for them. However, numpy and scipy are so large that their development has lead to them starting to optimize their packages to the devices installed one them. My machine uses AArch64 architecture and numpy natively uses SIMD optimizations to make the code optimal for my platform. This makes sense as to why there is such a large increase in speed as both the direct and fft solutions take the vectorization apporach to convolution.

Lastly, I attempted to download and utilitze Tensorflow to see if their convolution operations could be handled faster, and unfortunately this did not yield faster operations. The Tensorflow convolution ran about 100ms for the 50 iterations, so while it was faster than Scipy's direct method, it was slower than Scipy's fast fourier transform method. While there might be a faster method somewhere using Tensorflow, this was my first time every using this library and I was not sure how to produce any faster method.

Overall, trying to find hardware optimizations for the Raspberry Pi 4 is not as simple as looking up libraries. Tensorflow with its own Nueral Network module was not as fast as Scipy's fastest convolution method. It is also possible that your hardware is already configured to run code optimally. Luckily, this is the case for the the raspberry pi 4. However, if the problem being solved is more niche, the hardware or the libraries required might only be specfic to another hardware or have no specializations at all. However, researching these optimizations is worth the effort, as increasing an operation by almost two orders of magnitude can make a program actually viable to run on large scales.