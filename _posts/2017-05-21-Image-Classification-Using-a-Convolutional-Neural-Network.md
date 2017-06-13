---
layout: post
title:  "Image Classification using Convolutional Neural Networks"
date:   2017-05-21
categories: how-to
---

To wrap up the course, each student creates a capstone project; something to demonstrate the knowledge gained over the past twelve weeks. We don't have many guidelines and are encouraged to be creative with our problem statement. 

Recently, I adopted a puppy and was told its mother was a Boston Terrier, but that's all they know. I have since been to two separate vets who guess that my puppy, Boomer, might also be parts Boxer and/or Beagle. I have google image searched both Boston-Boxer and Boston-Beagle mixes and can see traces of him in both. However, many people seem pretty convinced of either Beagle or Boxer. My vet told me that for a cheap $100, I could find out the DNA, but since it's a dog and spending $100 on a DNA test won't provide much use other than being able to provide a concrete answer to the oft questioned 'What kind of dog is he?,' I figured I could end all suspicion through the creation of a neural network.

I initially had great ambitions to classify nearly 120 breeds, using an Image-net database of over 100,000 images. I quickly found that a GPU can't handle that, and had to narrow down my data to just a few breeds. It seemed pretty obvious to me that I should just take Boston Bull terriers, Beagles, and Boxers, each with a little over 1,200 images.

As this was my first endeavor into a neural network, I did a great deal of research and spent a lot of time processing my images. Image-net provides bounding boxes for some of their synsets of images, and luckily each of my three chosen breeds had them. I hadn't originially bothered with them, because when I was using all breeds, I found that only some had bounding boxes, and not all. So after a lot of struggling, I realized that I should double check for my three breeds.

Image-Net is a commonly used image database, so I was hoping that there would be tons of tools shared to easily download images and bounding boxes, but I didn't find them until after I figured it out on my own. I ended up trying the utilties from this [repo:](https://github.com/tzutalin/ImageNet_Utils). It's an excellent resource, and will even crop the images according to their bounding boxes.

Unfortunately, not all of the photos per sysnset have bounding boxes, which I didn't realize beforehand. The process from ImageNet_Utils had a separate folder of original images and one with the cropped images, and due to the difference in naming conventions, it would've taken more time that I had available to determine which images didn't have bounding boxes and should just be included anyways. Because of this, the number of images I had for this projects was greatly reduced.

Luckily, there are plenty of resources online detailing how to deal with small sets of images--perform image augmentation. Essentially, more images are created from the existing ones by flipping them, rotation, shifting, etc. so that the model can have more of a variety of images to train on.

As I have never built a nueral network, the modeling was a bit tough for me. I found several examples of different layers to add or pretrained models, but I settled on my final model because the accuracy scores from training were nearly identical to what the test score evaluated to, ~67%.  My other models were quite overfit and although training scores were high 90%s, they dropped nearly 30% with the test data.

To conclude my project, I ran a set of images of Boomer through my model to assess the percentages of each breed that were predicted. Turns out, it's pretty inconclusive. I guess if I really want to know, I should just fork over the $100. 


Check out the whole capstone piece [here](https://doyleax.github.io/Portfolio/breed-classification.html).



