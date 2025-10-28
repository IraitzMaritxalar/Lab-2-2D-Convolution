# 🧪 Lab 2 – 2D Convolution & FIR Filters

**Course:** Mathematical Algorithms (DSP) — Image Processing Labs  
**Objective:** Explore 2D convolution, FIR filters, smoothing, sharpening, and edge detection in images.

---

## 🔧 **0) Load image**
We use **peppers.png** (or **cameraman.tif** as fallback).  
Convert the image to grayscale and normalize to **double [0,1]**.

**Expected results:**
- Grayscale image  
- Pixel values between 0 and 1

---

## 🎯 **1) Delta image & impulse response**

A delta image is created to visualize the response of an averaging kernel:

delta = zeros(101,101);
delta(51,51) = 1;
H_vis = conv2(delta, h_avg, 'same');

markdown
Copiar código

**Effect:**
- Shows the **3x3 average kernel response** to an impulse.  
- Useful to understand linear filter behavior.

---

## 🟢 **2) Low-pass: box vs Gaussian, separability**

We compare smoothing filters:
- **Box 3x3** and **Box 7x7**  
- **Gaussian 7x7** (separable)

g1d = fspecial('gaussian',[1 7], sigma);
h_gauss = g1d'*g1d; % separable Gaussian kernel
I_box3 = imfilter(I, h_box3, 'replicate');
I_box7 = imfilter(I, h_box7, 'replicate');
I_gauss = imfilter(I, h_gauss, 'replicate');

yaml
Copiar código

**Observations:**
- Large box filters smooth but produce harsher edges.  
- Gaussian provides **smoother and more natural results**.  
- **Separability** allows two 1D convolutions, reducing computational cost.

---

## 🔺 **3) Unsharp masking (Sharpen)**

Enhancement using high-frequency mask:

I_blur = imfilter(I, h_gauss, 'replicate');
mask = I - I_blur;
gain = 1.0;
I_sharp = max(min(I + gain*mask,1),0);

yaml
Copiar código

**Effect:**
- Enhances edges and fine details.  
- Blur first, then add mask to locally increase contrast for sharpening.

---

## 🖤 **4) Edge detection: Sobel & Laplacian**

Classic operators are applied:

Gx = imfilter(I, h_sobel_x, 'replicate');
Gy = imfilter(I, h_sobel_y, 'replicate');
Gmag = hypot(Gx, Gy);
I_lap = imfilter(I, h_lap, 'replicate');

yaml
Copiar código

**Effects:**
- **Sobel Gx / Gy:** horizontal and vertical derivatives  
- **Gradient magnitude:** combines Gx and Gy  
- **Laplacian:** highlights edges in all directions

---

## 🔄 **5) Correlation vs convolution**

C1 = conv2(I, h_box3, 'same');
C2 = imfilter(I, h_box3, 'conv', 'same');
diff_val = max(abs(C1(:)-C2(:)));

yaml
Copiar código

**Observations:**
- `conv2` and `imfilter` with 'conv' produce almost identical results.  
- `imfilter` internally flips the kernel.  
- Maximum difference is very small (ideally zero).

---

## 🛡️ **6) Boundary handling**

Compare filter edge modes:

I_rep = imfilter(I, h_box7, 'replicate');
I_sym = imfilter(I, h_box7, 'symmetric');
I_cir = imfilter(I, h_box7, 'circular');

markdown
Copiar código

**Effects:**
- **replicate:** extends border pixels, avoids abrupt edges  
- **symmetric:** reflects image, smooth corners  
- **circular:** wraps edges, useful for periodic textures  
