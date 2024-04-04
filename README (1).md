# Image-Colorization
 Image Colorization with U-Net and GAN typically addresses how to automatically add color to black and white photos using deep neural networks. In this context, U-Net and GAN (Generative Adversarial Networks) are two popular network architectures utilized.
## Introduction to colorization problem
 The colorization problem aims to add color to black and white images, historically a manual task now advanced through deep learning. Techniques like U-Net and GANs enable models to predict realistic colors by learning from colored image datasets. This automation transforms monochrome visuals into colorized versions, enhancing their perceptual and historical value.
### RGB vs Lab
 RGB (Red, Green, Blue):
Purpose: Primarily used for digital displays, such as computer monitors, TV screens, and cameras. It's designed for electronic devices that emit light.
Composition: Consists of three color channels (Red, Green, and Blue). Combining these colors in various ways produces a wide range of colors. The intensity of each color is usually represented on a scale from 0 to 255.
Characteristics: RGB is an additive color model where colors are created by combining red, green, and blue light. Adding all three colors together in full intensity produces white, while the absence of all colors results in black.

![](https://github.com/hahwng/Image-Colorization-with-U-Net-and-GAN/blob/main/READMEFILE/plot-neon-rgb-camera-data_15_0.png)

 Lab (CIELAB):

Purpose: Designed to be more perceptually uniform, meaning equal changes in color values correspond to roughly equal changes in perceived color. It's used in industries where color matching is crucial, like printing and product design.
Composition: Consists of three axes - L* for lightness, a* for the color spectrum from green to red, and b* for the color spectrum from blue to yellow. L* ranges from 0 (black) to 100 (white), while a* and b* can have positive or negative values, representing the color direction from the neutral gray at 0.
Characteristics: Lab is device-independent, meaning it aims to model human color vision rather than how colors are produced by a device. It can represent all perceivable colors and is particularly useful for color difference calculations and color conversions that require accuracy across different devices.
In summary, RGB is optimized for electronic displays and digital imaging where colors are generated through light. In contrast, Lab is designed for precise color communication and matching, focusing on how humans perceive color changes, making it invaluable for applications requiring high color fidelity across different mediums.

![](https://github.com/hahwng/Image-Colorization-with-U-Net-and-GAN/blob/main/READMEFILE/Lab-color-space.jpg)

## How to solve the problem
 During the last few years, many different solutions have been proposed to colorize images by using deep learning. [_**Colorful Image Colorization**_](https://arxiv.org/abs/1603.08511) paper approached the problem as a classification task and they also considered the uncertainty of this problem
## Use strategy
 [_**Image-to-Image Translation with Conditional Adversarial Networks**_](https://arxiv.org/abs/1611.07004) paper, which you may know by the name pix2pix, proposed a general solution to many image-to-image tasks in deep learning which one of those was colorization.).
## GAN(Generative adversarial networks)
 As mentioned earlier, we are going to build a GAN (a conditional GAN to be specific) and use an extra loss function, L1 loss. Let's start with the GAN.

As you might know, in a GAN we have a generator and a discriminator model which learn to solve a problem together. In our setting, the generator model takes a grayscale image (1-channel image) and produces a 2-channel image, a channel for \*a and another for \*b. The discriminator, takes these two produced channels and concatenates them with the input grayscale image and decides whether this new 3-channel image is fake or real. Of course the discriminator also needs to see some real images (3-channel images again in Lab color space) that are not produced by the generator and should learn that they are real. 

So what about the "condition" we mentioned? Well, that grayscale image which both the generator and discriminator see is the condition that we provide to both models in our GAN and expect that the they take this condition into consideration.
![](https://github.com/hahwng/Image-Colorization-with-U-Net-and-GAN/blob/main/READMEFILE/Screenshot%202024-03-31%20190341.png)
## Loss function
 The earlier loss function helps to produce good-looking colorful images that seem real, but to further help the models and introduce some supervision in our task, we combine this loss function with L1 Loss (you might know L1 loss as mean absolute error) of the predicted colors compared with the actual colors:
![](https://github.com/hahwng/Image-Colorization-with-U-Net-and-GAN/blob/main/READMEFILE/Screenshot%202024-03-31%20190718.png)
If we use L1 loss alone, the model still learns to colorize the images but it will be conservative and most of the time uses colors like "gray" or "brown" because when it doubts which color is the best, it takes the average and uses these colors to reduce the L1 loss as much as possible (it is similar to the blurring effect of L1 or L2 loss in super resolution task). Also, the L1 Loss is preferred over L2 loss (or mean squared error) because it reduces that effect of producing gray-ish images. So, our combined loss function will be:
![](https://github.com/hahwng/Image-Colorization-with-U-Net-and-GAN/blob/main/READMEFILE/Screenshot%202024-03-31%20190745.png)
where _**λ**_ is a coefficient to balance the contribution of the two losses to the final loss (of course the discriminator loss does not involve the L1 loss).

## Data
The following will download about 20,000 images from COCO dataset. Notice that I am going to use only 8000 of them for training. Also you can use any other dataset like ImageNet as long as it contains various scenes and locations.

 
