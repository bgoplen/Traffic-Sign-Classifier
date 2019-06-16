
Traffic Sign Recognition
---


Overview
---

Traffic sign recognition was done in this projected using neural networks with the German Traffic Sign Dataset.  The LeNet-5 architecture was modified for this project and both deep and convolutional neural network elelment were used


Submission Files
---

Files included in this project:
Traffic_Sign_Classifier.ipynb
Traffic_Sign_Classifier.html
sign_pics_from_internet/road_sign_*.jpg
Write_up.md


Dataset Summary
---
The data consists of training, validation, and test datasets.  For each of these datasets, there are features consisting of 32x32 pixel images with 3 color channels for RGB, and labels for 43 different sign classes.  The training dataset has 34799 images of signs with corresponding sign labels.  The validation dataset has 4410 examples, and the test dataset has 12630 examples.


Exploratory Visualization
---
The exploratory visualization is shown in the html and has plotted 10 random images from each of the 43 sign class for the training, validation, and test datasets.  For the training dataset, sign images within each sign class look very similar.  For each sign class it looked like it is the exact same sign taking at slightly different angles.  The background and lighting seem similar within the each sign class also.  Similarly, the validation dataset seems to exhibit the same characteristics in having the same sign instance for each sign class with only slight differences in background and lighting.  The test dataset, on the other hand, has great variability in the signs for each sign class.  In contrast, the sign images in the test dataset have various backgrounds, lighting, sizes, and orientations.  

I also plotted the histograms for each dataset showing the number of example per sign class.  There is great variability in the number of image per class for all three.  The distributions seem similarly shaped between the datasets, and the class with the most images has approximately 10 times more images than the class with the fewest number of images.


Preprocessing
---

The first thing I tried was converting the images to grayscale and normalizing the data between -0.9 and 0.9.  I noticed that if I shifted the data so that its average is close to zero, I got better results so I applied a normalization function that shifts the training data's average to zero and applied the training data's transform to the validation and test datasets also.  There isn't much variability in the training data so I added random gamma corrections, contrast, rotation, zooming, and translation changes to the training images.  I also tried circular masks and adaptive thresholded gaussian filters to remove background noise, but these two methods didn't improve results and were not used in the final solution.  In order to create even more variability in the training dataset, I augmented the training data by duplicating it 5 times and adding randomized gamma correction, contrast, rotation, sizing, and translation changes to the duplicated data also.  Initially, I implemented the random contrast function to clip values between -0.9 to 0.9, but I found this degrades the result probably because information is lost from the clipping so I change the contrast function to be unbounded and not clip off pixel values to be between -0.9 and 0.9.

Having to control so many randomized perturbations to the images created a lot of hyperparameter I had to tune by hand.  I tried max gamma corrections of 1.25, 2.5, 4, 5, 6, 7.5, & 10.  Max contrasts of 1, 2, 2.5, 3, & 5. Max zooms of 1.1, 1.25, 1.3, 1.35, 1.4, & 1.5.  Max shifts of 1.25%, 2%, 2.5%, 3%, 5%, & 10%.  Max rotations of 8, 11, 14, 15, 16, 18, 20, & 25.

10 random sign images from each sign class of this preprocess data with the randomized characteristics is shown in html.  The histogram is also shown with the number of images per class.


Model Architecture
---

I basically used the LeNet-5 implemenation presenting at the end of the CNN lesson in the class materials, but I changed the final fully connected layer to have an output size of 43 instead to represent the number of sign types possible.  The same input size was used i.e. 32x32x1.  I also had to change the softmax_cross_entropy_with_logits function to softmax_cross_entropy_with_logits_v2 due to some errors I was getting.  A number of different preprossesing and data augmentions steps were done as discussed above to improve the accurracy to exceed 93% without having to change the architecture any further.

Please note, I reran the ipynb on the Udacity GPU rather than my own GPU prior to project submission so I had to change softmax_cross_entropy_with_logits_v2 to softmax_cross_entropy_with_logits.

Model Training
---

I got the GPU to work with tensorflow on my computer and determined that a batch size of 300 was about optimal by trial and error which makes sense because it has 384 cuda cores.  With batches of 300, the GPU was being utilized at 80% to 85%.  I had to adjust both the number of epochs and learning rates.  Lower learning rates needed more epochs, but produced better results with longer run times.  Higher learning rates needed few epochs, but resulted in poorer results and had faster run times.  I chose a learning rate of 0.001 with 20 epochs, and also plotted the training, validation, and test accuracy as the run progressed as shown in the html

Please note, I reran the ipynb on the Udacity GPU rather than my own GPU prior to project submission.

Solution Approach
---
Preprocessing was done as described in an earlier section with grayscale conversion, normalization, and recentering.  This alone was not enough to get 93% validation accuracy.  The training data had to be augmented by duplication and randomizing various image aspects like gamma correction, contrast, rotation, zoom, and translation.  These randomized adjustments had to be tuned by hand to maximized the accuracy.  In the end, I got training, validation, and test accurracies of 97.6%, 96.6%, and 94.5% respectively.


Acquiring New Images
---

I googled "German road signs" and picked some images from the following websites:

http://www.wrx900.com/german%20road%20signs%202.jpg
http://www.wrx900.com/german%20road%20signs.jpg
http://www.wrx900.com/Anfang.jpg
https://www.colourbox.com/image/building-site-with-traffic-signs-image-3230065
https://en.wikipedia.org/wiki/Schützenfest#/media/File:Sign_-_Attention_%22Sch%C3%BCtzenfest%22_.jpg
https://jalopnik.com/germanys-autobahn-is-overrated-1816209905
http://angelikasgerman.co.uk/9-signs-to-know-when-driving-in-germany/

From these images, I used Paint to extract out 11 images of signs and resized to 32x32.  I separated the sign images into two sets.  The first set has 6 images, and the signs in these images are present in the given German Traffic Sign Dataset.  I put the class id number and the sign name within the file names for this set:

sign_pics_from_internet\road_sign_13_Yield.jpg
sign_pics_from_internet\road_sign_17_No_entry.jpg
sign_pics_from_internet\road_sign_23_Slippery_road.jpg
sign_pics_from_internet\road_sign_25_Road_work.jpg
sign_pics_from_internet\road_sign_32_End_of_all_speed_and_passing_limits.jpg
sign_pics_from_internet\road_sign_40_Roundabout_mandatory.jpg

The second set of 5 sign images are not represented by any sign type in the German Traffic Sign Dataset.  These German road signs tend to not be applicable to self driving cars so it is not surprising that they are not in the given sign type dataset.  The file names for these sign images contain "road_sign_x" and the name of the sign.

sign_pics_from_internet\road_sign_x_Attention_Schützenfest.jpg
sign_pics_from_internet\road_sign_x_No_motorcycles.jpg
sign_pics_from_internet\road_sign_x_No_pedestrians.jpg
sign_pics_from_internet\road_sign_x_Restricted_parking.jpg
sign_pics_from_internet\road_sign_x_Vehicles_over_30_metric_tons_prohibited.jpg

Performance on New Images
---

The same preprocessing was applied to the 11 new images as was applied to the validation and test datasets.  I used the saver restore function to load the trained model and ran the 11 test images through it.  Each of the sign images along with the neural net predictions are displayed in the html.  For the first set of 6 sign images that were known within the given sign type database, it predicted the signs with 83.3% accurracy (5/6).  It erroneously predicted that the slippery road sign was a bicycle crossing.  In the training data example images for slippery road signs, they are very dark and the contrast is really low so it is difficult for even a human eye to see the sign.  It probably didn't learn the slippery road sign well as a result.  For the second set of 5 sign images, it could not predict any of the signs because those sign types were not in the sign type database. 



Model Certainty - Softmax Probabilities
---

For each of the 11 test images, I created bar charts showing the top 5 softmax probabilities.  For the first set of 6 images, the top probablities were nearly 100% for the correct sign type except for the slippery road sign.  The slippery road sign had about a 30% prediction for being a slippery road sign.  For the second set of 5 image, it had very high probablities for each of their top choices but tended to be less than 100%.  For the Attention_Schutzenfest sign, the top prediction was a Slippery road sign, and I guess it sort of looks like that at low resolution.  It predicted that the Restricted_parking sign was the No Vehicles sign, but there really isn't any sign in the database that is similar to the Restricted_parking sign so that is understandable.  For the No_pedestrian sign, it predicted that it is a "Go straight or left" sign.  I thought that it would be predicted as pedestrian crossing sign, but that one has a triangle shape.  I guess the circular sign similarity between the No_pedestrian sign and the "Go straight or left" sign matters more than the walking person silhouette in the sign.

Discussion
---
One thing I kept in mind when working on this project is that machine learning methods are only as good as the input data used to training it.  Frankly, the training data was of poor quality with little variability for the same sign types.  Often the images looked like the exact same sign instance taken at only slightly different angles.  If this was a real commercial project, I would insist on better training data with pictures of signs taking under greater variety of circumstances and certainly of different instances of signs rather than the exact same sign.  For some sign classes, all the images were really dark, and it's not even possible to see what the sign is with the naked eye.  The test dataset had much greater variability than the training dataset.  I suspect that if the test and training datasets were swapped, much better results could have been obtained.  However, I was able to working around this lack of variability by injecting some variability into it myself.  Random gama corrections, contrasts, rotations, zooming, and image shifting were applied to the training images to give them greater variability.  These randomized image changes did add a lot more hyperparameter and were difficult to tume.  One possible improvement would be to use Bayesian optimization to tune all the hyperparameters.





















