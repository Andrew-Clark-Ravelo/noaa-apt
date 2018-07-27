# noaa-apt

Work in progress decoder for NOAA APT images from a recorded WAV file.

Written in Rust, never tried to do signal processing or to use Rust before...

## Alternatives

Just bought an RTL-SDR and tried to receive NOAA APT images, I'm new to this but
as of July 2018:

- [wxtoimg], by far the most popular, lots of features but the site looks dead
  forever, you can still get some binaries uploaded by some people if you are
  lucky.

- [atp-dec/apt-dec], works really good. Keep in mind that the [1.7 release]
  looks newer than the [repo's master branch]. I tried several times to compile
  the [repo's master branch] without success, later I realized that there was a
  newer [1.7 release] and it worked.

- [zacstewart/apt-decoder], written in Python, slower than the others but really
  simple. Doesn't align the image to the sync stripes.

- [martinber/apt-decoder], bad hack made by me on top of
  [zacstewart/apt-decoder] trying to align the image to the sync stripes. Still
  slow and minor artifacts on the image if you look at the vertical stripes.

[wxtoimg]: http://wxtoimg.com/
[atp-dec/apt-dec]: https://github.com/csete/aptdec
[1.7 release]: https://github.com/csete/aptdec/releases
[repo's master branch]: https://github.com/csete/aptdec
[zacstewart/apt-decoder]: https://github.com/zacstewart/apt-decoder
[martinber/apt-decoder]: https://github.com/martinber/apt-decoder

## Algorithms

AM resampling and demodulation using FFT (maybe too slow?):

- Load samples from WAV.
- Resample and get [analytic signal].
  - Get L (interpolation factor) and M (decimation factor).
  - Insert L-1 zeros between samples,
  - Calculate FFT.
    - Remove negative half of spectrum to get analytic signal.
    - Filter spectrum images on the right to interpolate.
    - Calculate IFFT.
  - Decimate removing M-1 samples.
- Get absolute value of analytic signal to finish AM demodulation.

AM resampling and demodulation using FIR filter, following method 4 or 5 in
reference [1]:

- TODO

[analytic signal]: https://en.wikipedia.org/wiki/Analytic_signal

## References

- [Digital Envelope Detection: The Good, the Bad, and the Ugly][1]: Lists some
  AM demodulation methods.

- [Hilbert Transform Design Example][2]: How to get the analytic signal.

[1]: https://www.dsprelated.com/showarticle/938.php
[2]: https://www.dsprelated.com/freebooks/sasp/Hilbert_Transform_Design_Example.html
