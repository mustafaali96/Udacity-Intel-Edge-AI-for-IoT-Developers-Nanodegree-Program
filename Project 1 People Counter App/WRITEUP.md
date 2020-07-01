# Project Write-Up

## Explaining Custom Layers

- Custom layers are the layers that are not in the list of known layers.
- First model optimizer compare each layer of model with known layers list. If model contains any layer that are not in the list of known layers that ar **Custom laters**.
- List of known layers is different for each supported framework.
- To see the supported layers vist:- https://docs.openvinotoolkit.org/latest/_docs_MO_DG_prepare_model_Supported_Frameworks_Layers.html
---

The process behind converting custom layers involves...

- When implementing a custom layer for the model, we need to add extensions to both the Model Optimizer and the Inference Engine.
- Different supported framework has different step for registering custom layers.
- For register custom layers your self follow steps given in this link:- https://github.com/david-drew/OpenVINO-Custom-Layers/blob/master/2019.r2.0/ReadMe.Linux.2019.r2.md
- When you load IR in Infernece Engine then there may be possibility to found unsuported layers and for that cpu extension can be used. cpu extension in linux:- /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_xxxx.so 
---

Some of the potential reasons for handling custom layers are...

- Custom layers needs to handle because without handling it model optimizer can not convert specific model to Intermediate Representation.
- Model Optimizer does not know about the custom layers so it needs to taken care of and also need to handle for handle unsupported layers at the time of inference.

## Comparing Model Performance

My method(s) to compare models before and after conversion to Intermediate Representations
were...

The difference between model accuracy pre- and post-conversion was...
- Accuracy of the pre-conversion model = moderate (less than post-conversion) and post-conversion model = Good

The size of the model pre- and post-conversion was...
- size of the fozen inference graph(.pb file) = 54.5Mb and size of the pos-conversion model xml+bin file = 50.9Mb

The inference time of the model pre- and post-conversion was...
- Inference time of the pre-conversion model:- Avg inference time:- 123.40 ms, min inference time:- 65.50 ms, max inference time:- 5123.22 ms
- Inference time of the post-conversion model:- Avg inference time:- 2.32 ms, min inference time:- 0.24 ms, max inference time:- 59.41 ms

The CPU Overhead of the model pre- and post-conversion was...
- cpu overhead of the pre conversion model:- Around 55% per core
- cpu overhead of the post conversion model:- Around 31% per core

compare the differences in network needs and costs of using cloud services as opposed to deploying at the edge...
- Edge model needs only local network connection or edge model can used with very low speed compared to cloud.
- cost of the renting server at cloud is so high. where edge model can run on minimal cpu with local network connection.

## Assess Model Use Cases

Some of the potential use cases of the people counter app are...
1. people counter app can be use for count how many people visited for specific time
    - In this app we can determine this by using total count and duration.
2. people counter app can also used for intrusion detection for specific time.
    - We can add a switch or define a specific time and when app detect any person during a specific time or when switch is on then app can give notification or sound an intrusion alert.
3. people counter app can use for monitoring number of people can allow to present in the monitoring area.
    - We can add a threshold of number people if when the count of detected people go above threshold then app can send a notification or sound an alert
Each of these use cases would be useful because...
- Using the people counter app we can easily monitor the specific area.
- It can be use for intrusion detection and also for allowing limited people.

## Assess Effects on End User Needs

Lighting, model accuracy, and camera focal length/image size have different effects on a
deployed edge model. The potential effects of each of these are as follows...
- Lighting:- Lighting is most assential factor which affects to result of model. We need input image with lighting because model can't predict so accurately if input image is dark. So monitored place must have lights.
- Model accuracy:- Deployed edge model must have high accuracy because deployed edge model works in real time if we have deployed low accuracy model then it would give faulty results which is no good for end users.
- Camera focal length:- High focal length gives you focus on specific object and narrow angle image while Low focal length gives you the wider angle. Now It's totally depend upon end user's reuqirements that which type of camera is required. If end users want to monitor wider place than high focal length camera is better but model can extract less information about object's in picture so it can lower the accuracy. In compare if end users want to monitor very narrow place then they can use low focal length camera.
- Image size:- Image size totally depend upon resolution of image. If image resolution is better then size will be larger. Model can gives better output or result if image resolution is better but for higher resolution image model can take more time to gives output than less resolution image and also take more memory. If end users have more memory and also can manage with some delay for accurate result then higher resoltuion means larger image can be use.

## Submission Details

### Generating IR files

#### Step 1
Download the pre-trained model from here:- 
http://download.tensorflow.org/models/object_detection/faster_rcnn_inception_v2_coco_2018_01_28.tar.gz

#### Step 2
Extract the files:-
```
tar -xvf faster_rcnn_inception_v2_coco_2018_01_28.tar.gz
```

#### Step 3
Go to the faster_rcnn_inception_v2_coco_2018_01_28 directory and run the following command line:-
```
python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model faster_rcnn_inception_v2_coco_2018_01_28/frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config faster_rcnn_inception_v2_coco_2018_01_28/pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/faster_rcnn_support.json
```

#### Step 4
Create model directory in the app directory and move the generated .xml and .bin file into created model directory.

#### Running the app
**To install the ui follow the steps written in readme file of github profile**

Now for the running the demo on th video run the following commands:-
```
source /opt/intel/openvino/bin/setupvars.sh -pyver 3.5
```

**For running on the CPU**
```
python main.py -i resources/Pedestrian_Detect_2_1_1.mp4 -m frozen_inference_graph.xml -l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so -d CPU -pt 0.4 | ffmpeg -v warning -f rawvideo -pixel_format bgr24 -video_size 768x432 -framerate 24 -i - http://0.0.0.0:3004/fac.ffm
```
To see the output on a web based interface, open the link [http://localhost:8080](http://localhost:8080/) in a browser.


## Demo video of Running the App with UI
There is some lag in video because of poor network connection.

[![demo video with ui](https://youtu.be/P33PzFqHfRo)](https://youtu.be/P33PzFqHfRo)


