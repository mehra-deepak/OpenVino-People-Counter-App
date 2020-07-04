# Project Write-Up

You can use this document as a template for providing your project write-up. However, if you
have a different format you prefer, feel free to use it as long as you answer all required
questions.

For my project i have used [ssd_mobilenet_v2_coco](http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz) which can be downloaded by 

```wget http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v2_coco_2018_03_29.tar.gz```

In order to extract the files from the tar version of the model use :

```
tar -xvf ssd_mobilenet_v2_coco_2018_03_29.tar.gz
```

Rename the folder ```ssd_mobilenet_v2_coco_2018_03_29``` to  ```ssd```.

Navigate through the directory 

```
cd ssd
```

The model can't be the existing models provided by Intel. So, converting the TensorFlow model to Intermediate Representation (IR) or OpenVINO IR format. The command used is given below:

```
python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json
```



## Explaining Custom Layers

Custom layers are layers that are not included in the list of known layers used in a neural network. If one's topology contains any layers that are not in the list of known layers, the Model Optimizer classifies them as custom.

The process behind converting custom layers involves both custom layer extensions for the Model Optimizer, as well as custom layer extensions for the Inference Engine. Regarding the former, the extensions needed for the IR are the Custom Layer Extractor (that is responsible for identifying the custom layer operation and extracting the parameters for each instance of the custom layer) and the Custom Layer Operation (that is responsible for specifying the attributes that are supported by the custom layer and computing the output shape for each instance of the custom layer from its parameters). Regarding the latter, the extensions neede for the IE are Custom Layer CPU/GPU Extensions (where each plugin includes a library of optimized implementations to execute known layer operations which must be extended to execute a custom layer).

The process behind converting custom layers depends on frameworks we use either it is tensorflow, caffee or kaldi. For this project Tensorflow was used. Details
In both TensorFlow and Caffe : First,register the custom layers as extensions to the Model Optimizer.

For Caffe :
Second, register the layers as Custom, then use Caffe to calculate the output shape of the layer. You’ll need Caffe on your system to do this option.

For TensorFlow :
Second,replace the unsupported subgraph with a different subgraph. The final TensorFlow option is to actually offload the computation of the subgraph back to TensorFlow during inference.

Some of the potential reasons for handling custom layers are :

If any Layer which is not present in list of Supported Layer of any particular model Framework,The Model Optimizer automatically classifies it as Custom Layer.
Custom Layers are mainly used for handling UnSupported Layers.
Some of the potential reasons for handling custom layers is to optimize our pre-trained models and convert them to a intermediate representation without a lot of loss of accuracy and of course shrink and speed up the Performance.

OpenVino toolkit use model optimizer to reduce the size of the model. Model optimizer searches for the list of known layers for each layer in the model. The inference engine loads the layers from the model IR into the specified device plugin, which will search a list of known layer implementations for the device. If your model architecure contains layer or layers that are not in the list of known layers for the device, the Inference Engine considers the layer to be unsupported and reports an error. To use the model having the unsupported layer we need to use the custom layer feature of OpenVino Toolkit.



## Comparing Model Performance

My method(s) to compare models before and after conversion to Intermediate Representations were to use some performance measure to gauge performance with the stats of the output of the converted model.

The difference between model accuracy pre- and post-conversion was more or less the same after the conversion, even though the performance pre-conversion was a lot more accurate.

The size of the model pre- and post-conversion was within 10 orders of magnitude apart. The Size of Model Before was Vast which while it can be reduced using Open Vino Considerably.

The inference time of the model pre- and post-conversion has been significantly reduced by at least an order of magnitude difference.

The Table shows a brief comparison in terms of time and space complexity.

| Model/Framework                   | Latency (microseconds) |Memory(Mb)    |
| ----------------------------------| -----------------------| -------------|
| ssd_inception_v2_coco (plain TF)  | 229                    |538           |
|ssd_inception_v2_coco (OpenVINO)   | 150                    |329           |


## Assess Model Use Cases

Some of the potential use cases of the people counter app are:

These Type of models can be useful in situation like Pandemic or where Social Distancing is required , like in COVID-19 , it can leverage social distancing on broader level.

To track the movement of individual activities in a shop, in order to monitor for any illegal activities especially shoplifting.

Customer Traffic inside warehouses could help in mitigating risk factors. Also it can help to provide visitor analytics.


## Assess Effects on End User Needs

Lighting, model accuracy, and camera focal length/image size have different effects on a
deployed edge model. The potential effects of each of these are as follows...

1) Lightining: Lighter model with lower precesion provides less accuracy.

2) Model Accuracy : Dependent on Model Precesion.

3) Image Size : Doesnt really matter as image needs to be resized according to model.

## Model Research

[This heading is only required if a suitable model was not found after trying out at least three
different models. However, you may also use this heading to detail how you converted 
a successful model.]

In investigating potential people counter models, I tried each of the following three models:

- Model 1: Faster R-CNN
  - [Pytorch]
  - I converted the model to an Intermediate Representation with the following arguments ```python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/faster_rcnn_support.json```
  
  - The model was insufficient for the app because i was not able to handle the custom layers.
  
- Model 2: ssd_mobilenet_v2 
  - [Tensorflow Model]
  - I converted the model to an Intermediate Representation with the following arguments 
  ```python /opt/intel/openvino/deployment_tools/model_optimizer/mo_tf.py --input_model frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json --reverse_input_channel```

  - This is the model that i have used for my own purpose.
  - Although this model has large room for improvement but it works just fine.

