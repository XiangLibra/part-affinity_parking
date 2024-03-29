# Part Affinity Field Implementation in PyTorch

Pure python and pytorch implementation of [
Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields](https://arxiv.org/abs/1611.08050). Original caffe implementation is [here](https://github.com/ZheC/Realtime_Multi-Person_Pose_Estimation) 

NOTE: The codebase is no longer maintained. 

## COCO Parking space Dataset and Dataloader Setup:
Label tool: https://xianglibra.github.io/label_tool.github.io/label_tool/label.html

Use the Label tool after you can use this let xml convert to json file

 ```python multixml2json.py```

 ## Data visualize can use this 
 ```python visualize_coco_dataloader.py```

The data loader depends on pycocoapi which can be installed using 

```pip install pycocotools```

Design choices at this stage are i) Width of part affinity field ii) Heatmap std. iii) Choosing the parts for PAF iv) PAF magnitude (smooth/rigid) v) Masking crowded/unannotated joints? Due to differences in scale of the persons across dataset, some of these choices play an important role during training. Original paper uses constant PAF with a single part width for all joints across dataset. But this can introduce a lot of noise to the data in terms of misleading ground truth pafs/heatmaps. Alternate design choices are exposed in this implementation while keeping the original choices as default. 

## NN Model:
The paper uses first 10 layers from VGG-19 as feature extractor followed by 7 heatmap/paf regression stages with intermediate supervision at each stage. The same is implemented here.

## Training and Testing:

```python main.py ```

Comprehensive list of opts can be found in ```opts/``` folder. To debug/visualize each image's outputs during training ```-vizOut``` flag is helpful. 50k iterations takes around 11.5 hours with a batch size of 8 on a GTX 1080 GPU 

Sample nose heatmap outputs and nose-eye paf ouput is below after 20 epochs of training:
![Sample Output](output/sample_heatmap.png?raw=true "Sample heatmap outpus")

## Evaluation:
Evaluation is performed at multple scales and the average heatmap and paf are used for decoding pose. The evaluation pipeline is used from [here](https://github.com/tensorboy/pytorch_Realtime_Multi-Person_Pose_Estimation/blob/master/evaluate/coco_eval.py).

```python test.py -loadModel ../exp/default/model_train.pth```

![Sample Output](output/sample_output.png?raw=true "Sample outpus")
