# Image-processing-filtering-and-color-segmentation
## 1.1. Enhancing brightness of the image
For brightening the image, we simply multiply the value for each pixel in all the channels by 2. The image is loaded as a 32-bit int instead of uint8 so that the values can flow over 255. We then check if the value has exceeded 255 and then set it back to 255.

## 1.2. Vignette Effect
The vignette filter is supposed to create an image that is brighter near the center of the image and as we move away from the center, the image has to become darker. A Gaussian filter fits this specification where the values slowly decrease when we move away from the center. Thus, a Gaussian filter that has the same size as the input image can be multiplied element-wise to get the desired result. We observed that it takes a lot of time to create a gaussian filter which is the same size as the image. So we created two Gaussian filters, both of them being one dimensional, one which has the same size as the number of rows of the image and the other being the same size as that of the number of columns of the image. Since a product of two gaussian filters give another Gaussian filter, and a product of two vectors of the size, r x 1 and 1 x c gives a filter of size r x c, these two Gaussian filters can be multiplied to get a Gaussian filter with the same size as the image. An element-wise multiplication of this kernel with all the channels of the image gives the desired output. The implementation is not entirely real time now. But this can be done if the 2 gaussians vectors can be convolved one after the another instead of creating a huge gaussian kernel which is the same size of the image and then convolving this kernel over the input image. One can control the intensity of this effect by altering the value of sigma when creating the Gaussian filters.

## 2.1 Gaussian Blur
In order to achieve the gaussian blur, we start by creating a gaussian kernel. The gaussian kernel follows the symmetric gaussian distribution, i.e., $$G(x,y)=\frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}$$
The size chosen for the kernel, was 15x15 with $\sigma = 0.8$. Creating the kernel is is a one-time process and the same kernel can be applied to different images. The kernel is then convoluted with the image to apply the gaussian blur filter. 
<br><br><br>
A larger $\sigma$ value makes the image more blurrier. $\sigma$ represents the standard deviation from the center for each value in the gaussian filter, therefore more weight is given to neighboring pixels.

## 2.2 Bilateral Filter
A bilateral filter computes the squared distance between the intensity values between the selected pixel value with all other pixels within the given kernel and applies it to the gaussian formula. It wasn’t possible to implement a kernel that would move across the image and compute the squared distances between the intensity values. So we implemented an algorithm that would iterate through all the pixels in the image and for each pixel, the algorithm would iterate through all the pixels within the kernel for that pixel and compute the value using the formula provided in the problem. We can see that a beta value of 15 has a perfect balance between how bright the image is and how much blur is applied to the image.

## 2.3 Image Graadients
The magnitude map for the image can be created using the Sobel operators. We create the two Sobel operators in the x and y direction and then do two seperate convolutions with the source image twice. Using the two convolutions we can create the magnitude map by taking the magnitude for each pixel in the x and y convolutions.

## 2.4. Emphasizing Boundaries
Using the above magnitude map, the edges in the output image were highlighted. We set all the values in the magnitude map to be either 255 or 0 based on their intensity to create a binary mask. We then apply this to the original image, where the pixels that are set to 255 in the binary mask are changed to 0 (black).

## 3. Exact Histogram Equalization
The histogram of the input has to be switched to be entirely uniform as per the problem statement. For this purpose, 5 averaging kernels were created by convolving the kernels over the image which would find 5 averages over the input image and store it in a matrix after flattening them. The matrix is then sorted lexicographically. The new histogram to which the current histogram should be matched to is a uniform signal with the amplitude equal to the product of number of rows and columns divided by the number of bins it has to be assigned to. After sorting through the matrix lexicographically, each bin of the image is assigned with the corresponding pixels with the same size specified in the histogram (uniform signal). The last row of this matrix has the original pixel locations and when ‘argsort’ of this is performed, the new image can be obtained. 

Reference: https://github.com/StefanoD/ExactHistogramSpecification/blob/master/histogram_matching.py

The code here was referred to find how the matrix can be sorted lexicographically. The remaining parts of the code were programmed on our own.

## 4. Color Segmentation
The color segmentation of the image has to be performed without having any illumination variance and has to perform even if the surface has some deforms. The image was blurred using a very huge gaussian kernel which has a high sigma value. This was done so that none of the image’s deformity can affect the color segmentation. It is also done so that the neighboring pixels resemble the current pixel so that we can remove some of the illumination variances in the image. Once the image is blurred, the yellow component of the image is found out from the input RGB image. Various ranges were tried out and the particular range for the yellow channel is able to segment the image properly.
