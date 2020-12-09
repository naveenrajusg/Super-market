-->Pretrained weights:
Model was trained on pretrained weights(imagenet) of VGG-16.

-->Dataset preperation:
-Downloaded images from https://github.com/gulvarol/grocerydataset
-Replaced the Shelf images of the the above dataset with train and test split provided by you.
-few images in dataset was rotated so i corrected it to match its annotations.
-Iterated over images in ProductImagesFromShelves folder,each image name had its filename,shelf_id,	planogram_id,category,xmin,ymin,w,h. Stored the imagename and its respective x,y,w,h cordinates in csv file.
-Iterated over csv file genearted and converted it into XML file format which has annotations in xmin,ymin,xmax,ymax format, made sure mutiple annotations of single image is written in same respective xml file of that image.
-Program files for generation of csv from images("Data Preparation.ipynb") and convertion of csv to xml("product_annotations.py") is kept in the follwing path "ssd_keras-master/data_preperation_code".   

-->Architecture:
-Modified the original SSD 300 architecture by removing few layers from last, I have kept the original and modified architecture images in the folowwing path "ssd_keras-master/model_architecture_image".
Reasons to remove small feature maps:
-I removed few last layers of original architecture because it had very small feature map for which it will be having larger receptive field, since our target is to detect products in shelf which are of same similar small sizes(according to training and testing set).
-so if i use smaller feature maps of original architecture for which generation of large anchors is required with larger stride, which is useless in our cases as it will not match any of our groundtruth boxes.
-if i generate small anchors with small strides for small features maps(having large receptive field) there will be mixed gradient flow(large lows and high values) to the feature map cells, which will hamper the training time and the model may not converge very well. 

-->Image dimensions:
Both images and ground truth annotations were resized to 300*300 dimensions on run during training process. 

-->Anchor generation:
-Changed the anchor width and height calculation formula in the original anchor generation code,
-earlier it was                 
box_height = scale * size / np.sqrt(ar)
box_width  = scale * size * np.sqrt(ar)

-modified to
box_width = scale * size * np.sqrt(ar)
box_height= box_width*1.75

-I read the width and heights of ground truth annotations from xml of several images and i noticed that height/width ratio of majority annotations was approximately clustered around 1.8 to 2, therefore since the problem statement criteria was to use only one anchor per feature map cell i choose only one aspect ratio i.e 1.75.

-After in depth analysis of width and heights of ground truth annotations and took averages of the width and height of several annotations of several images, i decided on the below scales so that it would generate boxes approximately to the size of groundtruths.
-Modified the scales of the original code to 0.08,0.15,0.33.

-->Folder paths:
Training code: "ssd_keras-master/ssd300_training.ipynb"
Inference code: "ssd_keras-master/ssd300_inference.ipynb"
Evaluation code: "ssd_keras-master/cutom_metric_evaluation.py"
weights:"ssd_keras-master/ssd300_pascal_07+12_epoch-03_loss-4.1870_val_loss-3.6023"

-->Below path contains program to generate a csv file which will have total ground truth counts and total predictions counts along with the image names respectively.
"ssd_keras-master/GT_Prediction_count.py"


-->training details:
-Was trained for 80 epocs(1000steps each with batch size of 4) with learning rate of 0.0001 and 0.00008 with SGD as an optimiser.
-scaling factor(alpha)of 1 for localisation loss was used.


-->Metrics json file:
json file that contain metrics is in the following path "/ssd_keras-master/metrics.json"

-->Requirements:
Requirement text file has been kept in the following path"/ssd_keras-master/Requirement"

