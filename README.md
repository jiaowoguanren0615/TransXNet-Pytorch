# TransXNet-Pytorch

## TransXNet: Learning Both Global and Local Dynamics with a Dual Dynamic Token Mixer for Visual Recognition (https://arxiv.org/pdf/2310.19380v1.pdf)

## Project Structure
```
  ├── datasets: Load datasets
  	  ├── my_dataset.py: Customize reading data sets and define transforms data enhancement methods
  	  ├── split_data.py: Define the function to read the image dataset and divide the training-set and test-set
  	  ├── threeaugment.py: Additional data augmentation methods
  ├── models: TransXNet Model
  	  ├── poolformer.py: Construct "poolformer" model
  	  ├── transxnet.py: Construct "transxnet" model
  ├── util: 
  	  ├── engine.py: Function code for a training/validation process
      ├── losses.py: Knowledge distillation loss, combined with teacher model (if any)
  	  ├── optimizer.py: Define Sophia optimizer
  	  ├── samplers.py: Define the parameter of "sampler" in DataLoader
      ├── utils.py: Record various indicator information and output and distributed environment
  ├── estimate_model.py: Visualized evaluation indicators ROC curve, confusion matrix, classification report, etc.
  └── train_gpu.py: Training model startup file
```

## Precautions
The code is mainly derived from the official source code (https://github.com/LMMMEng/TransXNet/), and has been simplified and modified on this basis. It can now be used to train your own classification image datasets. Before you use the code to train your own data set, please first enter the ___train_gpu.py___ file and modify the ___data_root___, ___batch_size___ and ___nb_classes___ parameters. If you want to draw the confusion matrix and ROC curve, you only need to remove the comments of ___Plot_ROC___ and ___Predictor___ at the end of the code. For the third parameter, you should change it to the path of your own model weights file(.pth).

## Use Sophia Optimizer (in util/optimizer.py)
You can use anther optimizer sophia, just need to change the optimizer in ___train_gpu.py___, for this training sample, can achieve better results
```
# optimizer = create_optimizer(args, model_without_ddp)
optimizer = SophiaG(model.parameters(), lr=2e-4, betas=(0.965, 0.99), rho=0.01, weight_decay=args.weight_decay)
```

## Train this model
### train model with single-machine single-card：
```
python train_gpu.py
```

### train model with single-machine multi-card：
```
python -m torch.distributed.launch --nproc_per_node=8 train_gpu.py
```

### train model with single-machine multi-card: 
(using a specified part of the cards: for example, I want to use the second and fourth cards)
```
CUDA_VISIBLE_DEVICES=1,3 python -m torch.distributed.launch --nproc_per_node=2 train_gpu.py
```

### train model with multi-machine multi-card:
(For the specific number of GPUs on each machine, modify the value of --nproc_per_node. If you want to specify a certain card, just add CUDA_VISIBLE_DEVICES= to specify the index number of the card before each command. The principle is the same as single-machine multi-card training)
```
On the first machine: python -m torch.distributed.launch --nproc_per_node=1 --nnodes=2 --node_rank=0 --master_addr=<Master node IP address> --master_port=<Master node port number> train_gpu.py

On the second machine: python -m torch.distributed.launch --nproc_per_node=1 --nnodes=2 --node_rank=1 --master_addr=<Master node IP address> --master_port=<Master node port number> train_gpu.py
```

## Citation
```
@article{lou2023transxnet,
  title={TransXNet: Learning Both Global and Local Dynamics with a Dual Dynamic Token Mixer for Visual Recognition},
  author={Lou, Meng and Zhou, Hong-Yu and Yang, Sibei and Yu, Yizhou},
  journal={arXiv preprint arXiv:2310.19380},
  year={2023}
}
```

