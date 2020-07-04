# Project Write-Up

You can use this document as a template for providing your project write-up. However, if you
have a different format you prefer, feel free to use it as long as you answer all required
questions.

## Explaining Custom Layers

Custom layers are layers that are not included in the list of known layers used in a neural network. If one's topology contains any layers that are not in the list of known layers, the Model Optimizer classifies them as custom.

The process behind converting custom layers involves both custom layer extensions for the Model Optimizer, as well as custom layer extensions for the Inference Engine. Regarding the former, the extensions needed for the IR are the Custom Layer Extractor (that is responsible for identifying the custom layer operation and extracting the parameters for each instance of the custom layer) and the Custom Layer Operation (that is responsible for specifying the attributes that are supported by the custom layer and computing the output shape for each instance of the custom layer from its parameters). Regarding the latter, the extensions neede for the IE are Custom Layer CPU/GPU Extensions (where each plugin includes a library of optimized implementations to execute known layer operations which must be extended to execute a custom layer).

## Comparing Model Performance

My method(s) to compare models before and after conversion to Intermediate Representations were to use some performance measure to gauge performance with the stats of the output of the converted model.

The difference between model accuracy pre- and post-conversion was more or less the same after the conversion, even though the performance pre-conversion was a lot more accurate.

The size of the model pre- and post-conversion was within 10 orders of magnitude apart. The Size of Model Before was Vast which while it can be reduced using Open Vino Considerably.

The inference time of the model pre- and post-conversion has been significantly reduced by at least an order of magnitude difference.

## Assess Model Use Cases

Some of the potential use cases of the people counter app are:


To track the movement of individual activities in a shop, in order to monitor for any illegal activities especially shoplifting.




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

