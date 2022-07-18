in this trial I will try to find solution to the problem documented in [this paper](https://link.springer.com/article/10.1007/s11042-021-10607-7) so to break down this paper I will work on steps 

### Low illumination color image enhancement based on Gabor filtering and Retinex theory
#### What is gabor filter and How can we use it? 
  - it an edge detection algorithm, texture analysis, feature extraction
  - These filters have been shown to possess **optimal localization properties in both spatial and frequency domain** and thus are well suited for **texture segmentation problems**. 
  - Gabor filters are special classes of **band pass filters**, i.e., they allow a certain ‘band’ of frequencies and reject the others.
  - A Gabor filter can be viewed as a **sinusoidal signal** of particular frequency and orientation, modulated by a Gaussian wave.
    - **sine wave, sinusoidal wave**: just sinusoid is a mathematical curve defined in terms of the sine trigonometric function
    - can be define by **frequency and orientation**
    <img src="../images/sinusoidal_wave.png" />
  ##### Example 
    Consider an example of elephant which has pattern or stripes on its skin at different orientation. Now to highlight or extract out all those patterns we are going to use a bank of 16 Gabor filters at an orientation of 11.250 (i.e. if the first filter is at 00, then the second will be at 11.250, the third will be at 22.50, and so on.).The figure 2 shows all the filter bank of 16 filters
    - A 2D Gabor filter can be viewed as a sinusoidal signal of particular frequency and orientation, modulated by a Gaussian wave.
    - 







##### resources 
  - [Through The Eyes of Gabor Filter](https://medium.com/@anuj_shah/through-the-eyes-of-gabor-filter-17d1fdb3ac97)