# face.evoLVe: High-Performance Face Recognition Library based on PyTorch
* Evolve to be more comprehensive, effective and efficient for face related analytics \& applications!
* About the name:
  * "face" means this repo is dedicated for face related analytics \& applications.
  * "evolve" means unleash your greatness to be better and better. "LV" are capitalized to acknowledge the nurturing of Learning and Vision ([LV](http://www.lv-nus.org)) group, Nation University of Singapore (NUS).
* This work was done during Jian Zhao served as a short-term "Texpert" Research Scientist at Tencent FiT DeepSea AI Lab, Shenzhen, China.

|Author|Jian Zhao|
|:---:|:---:|
|Homepage|https://zhaoj9014.github.io|

****
## License

The code of [face.evoLVe](#Introduction) is released under the MIT License.

****
## News

:triangular_flag_on_post: **`22 Jan 2019`**: We are developing a highly-effective and efficient distributed training schema with multi-GPUs under PyTorch, supporting both the backbone and the head with fully-connected layers, to facilitate very large-scale training (huge number of identities, *e.g.*, >100k). We will add such support into our repo soon.

:triangular_flag_on_post: **`22 Jan 2019`**: We are fine-tuning our released [IR-50](https://arxiv.org/pdf/1602.07261.pdf) model on our private Aisa face data, which will be released soon to facilitate high-performance Asia face recognition.

:triangular_flag_on_post: **`21 Jan 2019`**: We are training a better-performing [IR-50](https://arxiv.org/pdf/1602.07261.pdf) model on [MS-Celeb-1M_Align_112x112](https://arxiv.org/pdf/1607.08221.pdf), and will replace the current model soon.
  
****
## Contents
* [Introduction](#Introduction)
* [Pre-Requisites](#Pre-Requisites)
* [Usage](#Usage)
* [Face Alignment](#Face-Alignment)
* [Data Processing](#Data-Processing)
* [Training and Validation](#Training-and-Validation)
* [Data Zoo](#Data-Zoo)
* [Model Zoo](#Model-Zoo)
* [Achievement](#Achievement)
* [Acknowledgement](#Acknowledgement)
* [Citation](#Citation)

****
## face.evoLVe for High-Performance Face Recognition

### Introduction 
:information_desk_person:

<img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig1.png" width="500px"/>

* This repo provides a comprehensive face recognition library for face related analytics \& applications, including face alignment (detection, landmark localization, affine transformation, *etc.*), data processing (*e.g.*, augmentation, data balancing, normalization, *etc.*), various backbones (*e.g.*, [ResNet](https://arxiv.org/pdf/1512.03385.pdf), [IR](https://arxiv.org/pdf/1602.07261.pdf), [IR-SE](https://arxiv.org/pdf/1709.01507.pdf), ResNeXt, SE-ResNeXt, DenseNet, [LightCNN](https://arxiv.org/pdf/1511.02683.pdf), MobileNet, ShuffleNet, DPN, *etc.*), various losses (*e.g.*, Softmax, [Focal](https://arxiv.org/pdf/1708.02002.pdf), Center, [SphereFace](https://arxiv.org/pdf/1704.08063.pdf), [CosFace](https://arxiv.org/pdf/1801.09414.pdf), [AmSoftmax](https://arxiv.org/pdf/1704.06369.pdf), [ArcFace](https://arxiv.org/pdf/1801.07698.pdf), Triplet, *etc.*) and bags of tricks for improving performance (*e.g.*, training refinements, model tweaks, knowledge distillation, *etc.*).
* All data before \& after alignment, source codes and trained models are provided.
* This repo can help researchers/engineers develop high-performance deep face recognition models and algorithms quickly for practical use and deployment.

****
### Pre-Requisites 
:cake:

* Linux or macOS
* [Python 3.7](https://repo.continuum.io/archive/Anaconda3-2018.12-Linux-x86_64.sh) (for training \& validation) and [Python 2.7](https://repo.continuum.io/archive/Anaconda2-2018.12-Linux-x86_64.sh) (for visualization w/ tensorboardX)
* PyTorch 1.0 (for traininig \& validation, install w/ `pip install torch torchvision`)
* MXNet 1.3.1 (optinal, for data processing, install w/ `pip install mxnet-cu90`)
* TensorFlow 1.12 (optinal, for visualization, install w/ `pip install tensorflow-gpu`)
* tensorboardX 1.6 (optinal, for visualization, install w/ `pip install tensorboardX`)
* OpenCV 3.4.5 (install w/ `pip install opencv-python`)
* bcolz 1.2.0 (install w/ `pip install bcolz`)

While not required, for optimal performance it is **highly** recommended to run the code using a CUDA enabled GPU. We used 4-8 NVIDIA Tesla P40 in parallel.

****
### Usage 
:orange_book:

* Clone the repo: `git clone https://github.com/ZhaoJ9014/face.evoLVe.PyTorch.git`.
* `mkdir data checkpoint log` at appropriate directory to store your train/val/test data, checkpoints and training logs.
* Prepare your train/val/test data (refer to Sec. [Data Zoo](#Data-Zoo) for publicly available face related databases), and ensure each database folder has the following structure:
  ```
  ./data/db_name/
          -> id1/
              -> 1.jpg
              -> ...
          -> id2/
              -> 1.jpg
              -> ...
          -> ...
              -> ...
              -> ...
  ```
* Refer to the codes of corresponding sections for specific purposes.

****
### Face Alignment 
:triangular_ruler:

<img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig2.png" width="900px"/>
<img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig3.png" width="500px"/>

* This section is based on the work of [MTCNN](https://arxiv.org/pdf/1604.02878.pdf).
* Folder: ```./align```
* Face detection, landmark localization APIs and visualization toy example with ipython notebook:
  ```python 
  from PIL import Image
  from detector import detect_faces
  from visualization_utils import show_results

  img = Image.open('some_img.jpg') # modify the image path to yours
  bounding_boxes, landmarks = detect_faces(img) # detect bboxes and landmarks for all faces in the image
  show_results(img, bounding_boxes, landmarks) # visualize the results
  ``` 
* Face alignment API (perform face detection, landmark localization and alignment with affine transformations on a whole database folder ```source_root``` with the directory structure as demonstrated in Sec. [Usage](#Usage), and store the aligned results to a new folder ```dest_root``` with the same directory structure): 
  ```
  python face_align.py -source_root [source_root] -dest_root [dest_root] -crop_size [crop_size]

  # python face_align.py -source_root './data/test' -dest_root './data/test_Aligned' -crop_size 112
  ```
* For macOS users, there is no need to worry about ```*.DS_Store``` files which may ruin your data, since they will be automatically removed when you run the scripts.
* Keynotes for customed use: 1) specify the arguments of ```source_root```, ```dest_root``` and ```crop_size``` to your own values when you run ```face_align.py```; 2) pass your customed ```min_face_size```, ```thresholds``` and ```nms_thresholds``` values to the ```detect_faces``` function of ```detector.py``` to match your practical requirements.

****
### Data Processing 
:bar_chart:

* Folder: ```./balance```
* Remove low-shot data API (remove the low-shot classes with less than ```min_num``` samples in the training set ```root``` with the directory structure as demonstrated in Sec. [Usage](#Usage) for data balance and effective model training):
  ```
  python remove_lowshot.py -root [root] -min_num [min_num]

  # python remove_lowshot.py -root './data/train' -min_num 10
  ```
* Keynotes for customed use: specify the arguments of ```root``` and ```min_num``` to your own values when you run ```remove_lowshot.py```.
* We prefer to include other data processing tricks, *e.g.*, augmentation (flip horizontally, scale hue/satuation/brightness with coefficients uniformly drawn from \[0.6,1.4\], add PCA noise with a coefficient sampled from a normal distribution N(0,0.1), *etc.*), weighted random sampling, normalization, *etc.* to the main training script in Sec. [Training and Validation](#Training-and-Validation) to be self-contained.

****
### Training and Validation 
:coffee:

* Folder: ```./```
* Configuration API (configurate your overall settings for training \& validation) ```config.py```:
  ```python
  import torch

  configurations = {
      1: dict(
          SEED = 1337, # random seed for reproduce results

          DATA_ROOT = '/media/pc/6T/jasonjzhao/data/faces_emore', # the parent root where your train/val/test data are stored
          MODEL_ROOT = '/media/pc/6T/jasonjzhao/buffer/model', # the root to buffer your checkpoints
          LOG_ROOT = '/media/pc/6T/jasonjzhao/buffer/log', # the root to log your train/val status
          BACKBONE_RESUME_ROOT = './', # the root to resume training from a saved checkpoint
          HEAD_RESUME_ROOT = './', # the root to resume training from a saved checkpoint

          BACKBONE_NAME = 'IR_SE_50', # support: ['ResNet_50', 'ResNet_101', 'ResNet_152', 'IR_50', 'IR_101', 'IR_152', 'IR_SE_50', 'IR_SE_101', 'IR_SE_152']
          HEAD_NAME = 'ArcFace', # support:  ['Softmax', 'ArcFace', 'CosFace', 'SphereFace', 'Am_softmax']
          LOSS_NAME = 'Focal', # support: ['Focal', 'Softmax']

          INPUT_SIZE = [112, 112], # support: [112, 112] and [224, 224]
          RGB_MEAN = [0.5, 0.5, 0.5], # for normalize inputs to [-1, 1]
          RGB_STD = [0.5, 0.5, 0.5],
          EMBEDDING_SIZE = 512, # feature dimension
          BATCH_SIZE = 512,
          DROP_LAST = True, # whether drop the last batch to ensure consistent batch_norm statistics
          LR = 0.1, # initial LR
          NUM_EPOCH = 125, # total epoch number (use the firt 1/25 epochs to warm up)
          WEIGHT_DECAY = 5e-4, # do not apply to batch_norm parameters
          MOMENTUM = 0.9,
          STAGES = [30, 60, 90], # epoch stages to decay learning rate

          DEVICE = torch.device("cuda:0" if torch.cuda.is_available() else "cpu"),
          MULTI_GPU = True, # flag to use multiple GPUs
          GPU_ID = [0, 1, 2, 3], # specify your GPU ids
          PIN_MEMORY = True,
          NUM_WORKERS = 0,
  ),
  }
  ```
* Train \& validation API (all folks about training \& validation, *i.e.*, import package, hyperparameters \& data loaders, model & loss & optimizer, train & validation & save checkpoint) ```train.py```. Since [MS-Celeb-1M](https://arxiv.org/pdf/1607.08221.pdf) serves as an [ImageNet](https://www.researchgate.net/profile/Li_Jia_Li/publication/221361415_ImageNet_a_Large-Scale_Hierarchical_Image_Database/links/00b495388120dbc339000000/ImageNet-a-Large-Scale-Hierarchical-Image-Database.pdf) in the filed of face recognition, we pre-train the [face.evoLVe](#Introduction) models on [MS-Celeb-1M](https://arxiv.org/pdf/1607.08221.pdf) and perform validation on [LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf), [CFP_FF](http://www.cfpw.io/paper.pdf), [CFP_FP](http://www.cfpw.io/paper.pdf), [AgeDB](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w33/papers/Moschoglou_AgeDB_The_First_CVPR_2017_paper.pdf), [CALFW](https://arxiv.org/pdf/1708.08197.pdf), [CPLFW](http://www.whdeng.cn/CPLFW/Cross-Pose-LFW.pdf) and [Vggface2_FP](https://arxiv.org/pdf/1710.08092.pdf). Let's dive into details together step by step.
  * Import necessary packages:
    ```python
    import torch
    import torch.nn as nn
    import torch.optim as optim
    import torchvision.transforms as transforms
    import torchvision.datasets as datasets

    from config import configurations
    from backbone.model_resnet import ResNet_50, ResNet_101, ResNet_152
    from backbone.model_irse import IR_50, IR_101, IR_152, IR_SE_50, IR_SE_101, IR_SE_152
    from head.metrics import ArcFace, CosFace, SphereFace, Am_softmax
    from loss.focal import FocalLoss
    from util.utils import make_weights_for_balanced_classes, get_val_data, separate_irse_bn_paras, separate_resnet_bn_paras, warm_up_lr, schedule_lr, perform_val, get_time, buffer_val, AverageMeter, accuracy

    from tensorboardX import SummaryWriter
    from tqdm import tqdm
    import os
    ```
  * Initialize hyperparameters:
    ```python
    cfg = configurations[1]

    SEED = cfg['SEED'] # random seed for reproduce results
    torch.manual_seed(SEED)

    DATA_ROOT = cfg['DATA_ROOT'] # the parent root where your train/val/test data are stored
    MODEL_ROOT = cfg['MODEL_ROOT'] # the root to buffer your checkpoints
    LOG_ROOT = cfg['LOG_ROOT'] # the root to log your train/val status
    BACKBONE_RESUME_ROOT = cfg['BACKBONE_RESUME_ROOT'] # the root to resume training from a saved checkpoint
    HEAD_RESUME_ROOT = cfg['HEAD_RESUME_ROOT']  # the root to resume training from a saved checkpoint

    BACKBONE_NAME = cfg['BACKBONE_NAME'] # support: ['ResNet_50', 'ResNet_101', 'ResNet_152', 'IR_50', 'IR_101', 'IR_152', 'IR_SE_50', 'IR_SE_101', 'IR_SE_152']
    HEAD_NAME = cfg['HEAD_NAME'] # support:  ['Softmax', 'ArcFace', 'CosFace', 'SphereFace', 'Am_softmax']
    LOSS_NAME = cfg['LOSS_NAME'] # support: ['Focal', 'Softmax']

    INPUT_SIZE = cfg['INPUT_SIZE']
    RGB_MEAN = cfg['RGB_MEAN'] # for normalize inputs
    RGB_STD = cfg['RGB_STD']
    EMBEDDING_SIZE = cfg['EMBEDDING_SIZE'] # feature dimension
    BATCH_SIZE = cfg['BATCH_SIZE']
    DROP_LAST = cfg['DROP_LAST'] # whether drop the last batch to ensure consistent batch_norm statistics
    LR = cfg['LR'] # initial LR
    NUM_EPOCH = cfg['NUM_EPOCH'] # total epoch number (use the firt 1/25 epochs to warm up)
    WEIGHT_DECAY = cfg['WEIGHT_DECAY'] # do not apply to batch_norm parameters
    MOMENTUM = cfg['MOMENTUM']
    STAGES = cfg['STAGES'] # epoch stages to decay learning rate

    DEVICE = cfg['DEVICE']
    MULTI_GPU = cfg['MULTI_GPU'] # flag to use multiple GPUs
    GPU_ID = cfg['GPU_ID'] # specify your GPU ids
    PIN_MEMORY = cfg['PIN_MEMORY']
    NUM_WORKERS = cfg['NUM_WORKERS']
    print("=" * 60)
    print("Overall Configurations:")
    print(cfg)
    print("=" * 60)

    writer = SummaryWriter(LOG_ROOT) # writer for buffering intermedium results
    ```
  * Train \& validation data loaders:
    ```python
    train_transform = transforms.Compose([ # refer to https://pytorch.org/docs/stable/torchvision/transforms.html for more build-in online data augmentation
        transforms.Resize([int(128 * INPUT_SIZE[0] / 112), int(128 * INPUT_SIZE[0] / 112)]), # smaller side resized
        transforms.RandomCrop([INPUT_SIZE[0], INPUT_SIZE[1]]),
        transforms.RandomHorizontalFlip(),
        transforms.ToTensor(),
        transforms.Normalize(mean = RGB_MEAN,
                             std = RGB_STD),
    ])

    dataset_train = datasets.ImageFolder(os.path.join(DATA_ROOT, 'imgs'), train_transform)

    # create a weighted random sampler to process imbalanced data
    weights = make_weights_for_balanced_classes(dataset_train.imgs, len(dataset_train.classes))
    weights = torch.DoubleTensor(weights)
    sampler = torch.utils.data.sampler.WeightedRandomSampler(weights, len(weights))

    train_loader = torch.utils.data.DataLoader(
        dataset_train, batch_size = BATCH_SIZE, sampler = sampler, pin_memory = PIN_MEMORY,
        num_workers = NUM_WORKERS, drop_last = DROP_LAST
    )

    NUM_CLASS = len(train_loader.dataset.classes)
    print("Number of Training Classes: {}".format(NUM_CLASS))
    
    # validate on LFW, CFP_FF, CFP_FP, AgeDB, CALFW, CPLFW and VGGFace2_FP
    lfw, cfp_ff, cfp_fp, agedb, calfw, cplfw, vgg2_fp, lfw_issame, cfp_ff_issame, cfp_fp_issame, agedb_issame, calfw_issame, cplfw_issame, vgg2_fp_issame = get_val_data(DATA_ROOT)
    ```
  * Define and initialize model (backbone \& head):
    ```python
    BACKBONE_DICT = {'ResNet_50': ResNet_50(INPUT_SIZE), 'ResNet_101': ResNet_101(INPUT_SIZE), 'ResNet_152': ResNet_152(INPUT_SIZE),
                     'IR_50': IR_50(INPUT_SIZE), 'IR_101': IR_101(INPUT_SIZE), 'IR_152': IR_152(INPUT_SIZE),
                     'IR_SE_50': IR_SE_50(INPUT_SIZE), 'IR_SE_101': IR_SE_101(INPUT_SIZE), 'IR_SE_152': IR_SE_152(INPUT_SIZE)}
    BACKBONE = BACKBONE_DICT[BACKBONE_NAME]
    print("=" * 60)
    print(BACKBONE)
    print("{} Backbone Generated".format(BACKBONE_NAME))
    print("=" * 60)

    HEAD_DICT = {'ArcFace': ArcFace(in_features = EMBEDDING_SIZE, out_features = NUM_CLASS),
                 'CosFace': CosFace(in_features = EMBEDDING_SIZE, out_features = NUM_CLASS),
                'SphereFace': SphereFace(in_features = EMBEDDING_SIZE, out_features = NUM_CLASS),
                'Am_softmax': Am_softmax(in_features = EMBEDDING_SIZE, out_features = NUM_CLASS)}
    HEAD = HEAD_DICT[HEAD_NAME]
    print("=" * 60)
    print(HEAD)
    print("{} Head Generated".format(HEAD_NAME))
    print("=" * 60)
    ```
  * Define and initialize loss function:
    ```python
    LOSS_DICT = {'Focal': FocalLoss(), 'Softmax': nn.CrossEntropyLoss()}
    LOSS = LOSS_DICT[LOSS_NAME]
    print("=" * 60)
    print(LOSS)
    print("{} Loss Generated".format(LOSS_NAME))
    print("=" * 60)
    ```
  * Define and initialize optimizer:
    ```python
    if BACKBONE_NAME.find("IR") >= 0:
        backbone_paras_only_bn, backbone_paras_wo_bn = separate_irse_bn_paras(BACKBONE) # separate batch_norm parameters from others; do not do weight decay for batch_norm parameters to improve the generalizability
        _, head_paras_wo_bn = separate_irse_bn_paras(HEAD)
    else:
        backbone_paras_only_bn, backbone_paras_wo_bn = separate_resnet_bn_paras(BACKBONE) # separate batch_norm parameters from others; do not do weight decay for batch_norm parameters to improve the generalizability
        _, head_paras_wo_bn = separate_resnet_bn_paras(HEAD)
    OPTIMIZER = optim.SGD([{'params': backbone_paras_wo_bn + head_paras_wo_bn,
                            'weight_decay': WEIGHT_DECAY}, {'params': backbone_paras_only_bn}], lr = LR, momentum = MOMENTUM)
    print("=" * 60)
    print(OPTIMIZER)
    print("Optimizer Generated")
    print("=" * 60)
    ```
  * Whether resume from a checkpoint or not:
    ```python
    if BACKBONE_RESUME_ROOT and HEAD_RESUME_ROOT:
        print("=" * 60)
        if os.path.isfile(BACKBONE_RESUME_ROOT) and os.path.isfile(HEAD_RESUME_ROOT):
            print("Loading Backbone Checkpoint '{}'".format(BACKBONE_RESUME_ROOT))
            BACKBONE.load_state_dict(torch.load(BACKBONE_RESUME_ROOT))
            print("Loading Head Checkpoint '{}'".format(HEAD_RESUME_ROOT))
            HEAD.load_state_dict(torch.load(HEAD_RESUME_ROOT))
        else:
            print("No Checkpoint Found at '{}' and '{}'. Please Have a Check or Continue to Train from Scratch".format(BACKBONE_RESUME_ROOT, HEAD_RESUME_ROOT))
        print("=" * 60)
    ```
  * Whether use multi-GPU or not:
    ```python
    if MULTI_GPU:
        # multi-GPU setting
        BACKBONE = nn.DataParallel(BACKBONE, device_ids = GPU_ID)
        BACKBONE = BACKBONE.to(DEVICE)
        HEAD = nn.DataParallel(HEAD, device_ids = GPU_ID)
        HEAD = HEAD.to(DEVICE)
    else:
        # single-GPU setting
        BACKBONE = BACKBONE.to(DEVICE)
        HEAD = HEAD.to(DEVICE)
    ```
  * Minor settings prior to training:
    ```python
    DISP_FREQ = len(train_loader) // 100 # frequency to display training loss & acc

    NUM_EPOCH_WARM_UP = NUM_EPOCH // 25 # use the first 1/25 epochs to warm up
    NUM_BATCH_WARM_UP = len(train_loader) * NUM_EPOCH_WARM_UP # use the first 1/25 epochs to warm up
    batch = 0 # batch index
    ```
  * Training \& validation \& save checkpoint (use the first 1/25 epochs to warm up -- gradually increase LR to the initial value to ensure stable convergence):
    ```python
    for epoch in range(NUM_EPOCH): # start training process

        if epoch == STAGES[0]: # adjust LR for each training stage after warm up, you can also choose to adjust LR manually (with slight modification) once plaueau observed
            schedule_lr(OPTIMIZER)
        if epoch == STAGES[1]:
            schedule_lr(OPTIMIZER)
        if epoch == STAGES[2]:
            schedule_lr(OPTIMIZER)

        BACKBONE.train() # set to training mode
        HEAD.train()
        
        losses = AverageMeter()
        top1 = AverageMeter()
        top5 = AverageMeter()

        for inputs, labels in tqdm(iter(train_loader)):

            if (epoch + 1 <= NUM_EPOCH_WARM_UP) and (batch + 1 <= NUM_BATCH_WARM_UP): # adjust LR for each training batch during warm up
                warm_up_lr(batch + 1, NUM_BATCH_WARM_UP, LR, OPTIMIZER)

            # compute output
            inputs = inputs.to(DEVICE)
            labels = labels.to(DEVICE).long()
            features = BACKBONE(inputs)
            outputs = HEAD(features, labels)
            loss = LOSS(outputs, labels)

            # measure accuracy and record loss
            prec1, prec5 = accuracy(outputs.data, labels, topk = (1, 5))
            losses.update(loss.data.item(), inputs.size(0))
            top1.update(prec1.data.item(), inputs.size(0))
            top5.update(prec5.data.item(), inputs.size(0))

            # compute gradient and do SGD step
            OPTIMIZER.zero_grad()
            loss.backward()
            OPTIMIZER.step()

            # dispaly training loss & acc every DISP_FREQ
            if ((batch + 1) % DISP_FREQ == 0) and batch != 0:
                print("=" * 60)
                print('Epoch {}/{} Batch {}/{}\t'
                      'Training Loss {loss.val:.4f} ({loss.avg:.4f})\t'
                      'Training Prec@1 {top1.val:.3f} ({top1.avg:.3f})\t'
                      'Training Prec@5 {top5.val:.3f} ({top5.avg:.3f})'.format(
                    epoch + 1, NUM_EPOCH, batch + 1, len(train_loader) * NUM_EPOCH, loss = losses, top1 = top1, top5 = top5))
                print("=" * 60)

            batch += 1 # batch index

        # training statistics per epoch (buffer for visualization)
        epoch_loss = losses.avg
        epoch_acc = top1.avg
        writer.add_scalar("Training_Loss", epoch_loss, epoch + 1)
        writer.add_scalar("Training_Accuracy", epoch_acc, epoch + 1)
        print("=" * 60)
        print('Epoch: {}/{}\t'
              'Training Loss {loss.val:.4f} ({loss.avg:.4f})\t'
              'Training Prec@1 {top1.val:.3f} ({top1.avg:.3f})\t'
              'Training Prec@5 {top5.val:.3f} ({top5.avg:.3f})'.format(
            epoch + 1, NUM_EPOCH, loss = losses, top1 = top1, top5 = top5))
        print("=" * 60)

        # perform validation & save checkpoints per epoch
        # validation statistics per epoch (buffer for visualization)
        print("=" * 60)
        print("Perform Evaluation on LFW, CFP_FF, CFP_FP, AgeDB, CALFW, CPLFW and VGG2_FP, and Save Checkpoints...")
        accuracy_lfw, best_threshold_lfw, roc_curve_lfw = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, lfw, lfw_issame)
        buffer_val(writer, "LFW", accuracy_lfw, best_threshold_lfw, roc_curve_lfw, epoch + 1)
        accuracy_cfp_ff, best_threshold_cfp_ff, roc_curve_cfp_ff = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, cfp_ff, cfp_ff_issame)
        buffer_val(writer, "CFP_FF", accuracy_cfp_ff, best_threshold_cfp_ff, roc_curve_cfp_ff, epoch + 1)
        accuracy_cfp_fp, best_threshold_cfp_fp, roc_curve_cfp_fp = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, cfp_fp, cfp_fp_issame)
        buffer_val(writer, "CFP_FP", accuracy_cfp_fp, best_threshold_cfp_fp, roc_curve_cfp_fp, epoch + 1)
        accuracy_agedb, best_threshold_agedb, roc_curve_agedb = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, agedb, agedb_issame)
        buffer_val(writer, "AgeDB", accuracy_agedb, best_threshold_agedb, roc_curve_agedb, epoch + 1)
        accuracy_calfw, best_threshold_calfw, roc_curve_calfw = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, calfw, calfw_issame)
        buffer_val(writer, "CALFW", accuracy_calfw, best_threshold_calfw, roc_curve_calfw, epoch + 1)
        accuracy_cplfw, best_threshold_cplfw, roc_curve_cplfw = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, cplfw, cplfw_issame)
        buffer_val(writer, "CPLFW", accuracy_cplfw, best_threshold_cplfw, roc_curve_cplfw, epoch + 1)
        accuracy_vgg2_fp, best_threshold_vgg2_fp, roc_curve_vgg2_fp = perform_val(MULTI_GPU, DEVICE, EMBEDDING_SIZE, BATCH_SIZE, BACKBONE, vgg2_fp, vgg2_fp_issame)
        buffer_val(writer, "VGGFace2_FP", accuracy_vgg2_fp, best_threshold_vgg2_fp, roc_curve_vgg2_fp, epoch + 1)
        print("Epoch {}/{}, Evaluation: LFW Acc: {}, CFP_FF Acc: {}, CFP_FP Acc: {}, AgeDB Acc: {}, CALFW Acc: {}, CPLFW Acc: {}, VGG2_FP Acc: {}".format(epoch + 1, NUM_EPOCH, accuracy_lfw, accuracy_cfp_ff, accuracy_cfp_fp, accuracy_agedb, accuracy_calfw, accuracy_cplfw, accuracy_vgg2_fp))
        print("=" * 60)

        # save checkpoints per epoch
        if MULTI_GPU:
            torch.save(BACKBONE.module.state_dict(), os.path.join(MODEL_ROOT, "Backbone_{}_Epoch_{}_Batch_{}_Time_{}_checkpoint.pth".format(BACKBONE_NAME, epoch + 1, batch, get_time())))
            torch.save(HEAD.module.state_dict(), os.path.join(MODEL_ROOT, "Head_{}_Epoch_{}_Batch_{}_Time_{}_checkpoint.pth".format(HEAD_NAME, epoch + 1, batch, get_time())))
        else:
             torch.save(BACKBONE.state_dict(), os.path.join(MODEL_ROOT, "Backbone_{}_Epoch_{}_Batch_{}_Time_{}_checkpoint.pth".format(BACKBONE_NAME, epoch + 1, batch, get_time())))
             torch.save(HEAD.state_dict(), os.path.join(MODEL_ROOT, "Head_{}_Epoch_{}_Batch_{}_Time_{}_checkpoint.pth".format(HEAD_NAME, epoch + 1, batch, get_time())))
    ```
* Now, you can start to play with [face.evoLVe](#Introduction) and run ```train.py```. User friendly information will popped out on your terminal:
  * About overall configuration:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig4.png" width="900px"/>
  
  * About number of training classes:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig5.png" width="400px"/>
  
  * About backbone details:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig6.png" width="900px"/>
  
  * About head details:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig7.png" width="400px"/>
  
  * About loss details:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig8.png" width="400px"/>
  
  * About optimizer details:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig9.png" width="400px"/>
    
  * About resume training:
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig10.png" width="400px"/>
  
  * About training status \& statistics (when batch index reachs ```DISP_FREQ``` or at the end of each epoch):
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig11.png" width="900px"/>
  
  * About validation statistics \& save checkpoints (at the end of each epoch):
  
    <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig12.png" width="900px"/>
    
* Please refer to Sec. [Model Zoo](#Model-Zoo) for specific model weights and corresponding performance.
* Feature extraction API (extract features from pre-trained models) ```./util/extract_feature_v1.py``` (implemented with PyTorch build-in functions) and ```./util/extract_feature_v2.py``` (implemented with OpenCV).
* Visualize training \& validation statistics with tensorboardX (see Sec. [Model Zoo](#Model-Zoo)):
  ```
  tensorboard --logdir /media/pc/6T/jasonjzhao/buffer/log
  ```
  
****
### Data Zoo 
:tiger:

|Database|Version|\#Identity|\#Image|\#Frame|\#Video|Download Link|
|:---:|:----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|[LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf)|Raw|5,749|13,233|-|-|[Google Drive](https://drive.google.com/file/d/1JIgAXYqXrH-RbUvcsB3B6LXctLU9ijBA/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1VzSI_xqiBw-uHKyRbi6zzw)|
|[LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf)|Align_250x250|5,749|13,233|-|-|[Google Drive](https://drive.google.com/file/d/11h-QIrhuszY3PzT17Q5eXw8yrewgqX7m/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1Ir8kAcQjBJA6A_pWPL9ozQ)|
|[LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf)|Align_112x112|5,749|13,233|-|-|[Google Drive](https://drive.google.com/file/d/1o5BugNFAfs-X9amdOquPRI5EwmceM2sE/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1j0aC8Ln6pNZ1BG8X0i3S2w)|
|[CALFW](https://arxiv.org/pdf/1708.08197.pdf)|Raw|4,025|12,174|-|-|[Google Drive](https://drive.google.com/file/d/1LcIDIfeZ027tbyUJDbaDt12ZoMVJuoMp/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/17IzL_nGzedup1gcPuob0NQ)|
|[CALFW](https://arxiv.org/pdf/1708.08197.pdf)|Align_112x112|4,025|12,174|-|-|[Google Drive](https://drive.google.com/file/d/1kpmcDeDmPqUcI5uX0MCBzpP_8oQVojzW/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1IxqyLFfHNQaj3ibjc7Vcvg)|
|[CPLFW](http://www.whdeng.cn/CPLFW/Cross-Pose-LFW.pdf)|Raw|3,884|11,652|-|-|[Google Drive](https://drive.google.com/file/d/1WipxZ1QXs_Fi6Y5qEFDayEgos3rHDRnS/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1gJuZZcm-2crTrqKI0sa5sA)|
|[CPLFW](http://www.whdeng.cn/CPLFW/Cross-Pose-LFW.pdf)|Align_112x112|3,884|11,652|-|-|[Google Drive](https://drive.google.com/file/d/1DJ8VYzc56GVXsWJQIj722yxlmaKV63_8/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1HvzLtuFGCC0rRO5h3HvF-g)|
|[CASIA-WebFace](https://arxiv.org/pdf/1411.7923.pdf)|Raw_v1|10,575|494,414|-|-|[Baidu Drive](https://pan.baidu.com/s/1xh073sKX3IYp9xPm9S6F5Q)|
|[CASIA-WebFace](https://arxiv.org/pdf/1411.7923.pdf)|Raw_v2|10,575|494,414|-|-|[Google Drive](https://drive.google.com/file/d/19R6Svdj5HbUA0y6aJv3P1WkIR5wXeCnO/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1cZqsRxln-JmrA4xevLfjYQ)|
|[CASIA-WebFace](https://arxiv.org/pdf/1411.7923.pdf)|Clean|10,575|455,594|-|-|[Google Drive](https://drive.google.com/file/d/1wJC2aPA4AC0rI-tAL2BFs2M8vfcpX-w6/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1x_VJlG9WV1OdrrJ7ARUZQw)|
|[MS-Celeb-1M](https://arxiv.org/pdf/1607.08221.pdf)|Clean|100,000|5,084,127|-|-|[Google Drive](https://drive.google.com/file/d/18FxgfXgKwuYzY3DmWJXNJuY51TPmC9yH/view?usp=sharing)|
|[MS-Celeb-1M](https://arxiv.org/pdf/1607.08221.pdf)|Align_224x224|100,000|5,084,127|-|-| |
|[MS-Celeb-1M](https://arxiv.org/pdf/1607.08221.pdf)|Align_112x112|85,742|5,822,653|-|-|[Google Drive](https://drive.google.com/file/d/1tFMAuCfu6mZl7xHaqbEtCAKF0Bi4RoN2/view?usp=sharing)|
|[Vggface2](https://arxiv.org/pdf/1710.08092.pdf)|Clean|8,631|3,086,894|-|-|[Google Drive](https://drive.google.com/file/d/1jdZw6ZmB7JRK6RS6QP3YEr2sufJ5ibtO/view?usp=sharing)|
|[Vggface2_FP](https://arxiv.org/pdf/1710.08092.pdf)|Align_112x112|-|-|-|-|[Google Drive](https://drive.google.com/file/d/1N7QEEQZPJ2s5Hs34urjseFwIoPVSmn4r/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1STSgORPyRT-eyk5seUTcRA)|
|[AgeDB](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w33/papers/Moschoglou_AgeDB_The_First_CVPR_2017_paper.pdf)|Raw|570|16,488|-|-|[Google Drive](https://drive.google.com/file/d/1FoZDyzTrs8r_oFM3Xqmi3iAHsnoirTRA/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1-E_hkW-bXsXNYRiAhRPM7A)|
|[AgeDB](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w33/papers/Moschoglou_AgeDB_The_First_CVPR_2017_paper.pdf)|Align_112x112|570|16,488|-|-|[Google Drive](https://drive.google.com/file/d/1AoZrZfym5ZhdTyKSxD0qxa7Xrp2Q1ftp/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1ehwmQ4M7WpLylV83uUBxiA)|
|[IJB-A](https://www.cv-foundation.org/openaccess/content_cvpr_2015/papers/Klare_Pushing_the_Frontiers_2015_CVPR_paper.pdf)|Clean|500|5,396|20,369|2,085|[Google Drive](https://drive.google.com/file/d/1WdQ62XJuvw0_K4MUP5nXOhv2RsEBVB1f/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1iN68cdiPO0bTTN_hwmbe9w)|
|[IJB-B](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w6/papers/Whitelam_IARPA_Janus_Benchmark-B_CVPR_2017_paper.pdf)|Raw|1,845|21,798|55,026|7,011|[Google Drive](https://drive.google.com/file/d/15oibCHL3NX-q-QV8q_UAmbIr9e_M0n1R/view?usp=sharing)|
|[CFP](http://www.cfpw.io/paper.pdf)|Raw|500|7,000|-|-|[Google Drive](https://drive.google.com/file/d/1tGNtqzWeUx3BYAxRHBbH1Wy7AmyFtZkU/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/10Qq64LO_RWKD2cr_D32_6A)|
|[CFP](http://www.cfpw.io/paper.pdf)|Align_112x112|500|7,000|-|-|[Google Drive](https://drive.google.com/file/d/1MWp7NGqzpkYeXl-WiKIINHc3F4o-aT6D/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1kvGaVP5dluJ4GgUFsR3QVA)|
|[Umdfaces](https://arxiv.org/pdf/1611.01484.pdf)|Align_112x112|8,277|367,888|-|-|[Google Drive](https://drive.google.com/file/d/13IDdIMqPCd8h1vWOYBkW6T5bjAxwmxm5/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1UzrBMguV5YLh8aawIodKeQ)|
|[CelebA](https://arxiv.org/pdf/1411.7766.pdf)|Raw|10,177|202,599|-|-|[Google Drive](https://drive.google.com/file/d/1FO_p759JtKOf3qOnxOGpmoxCcnKiPdBI/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1DfvDKKEB11MrZcf7hPjJfw)|
|[CACD-VS](http://cmlab.csie.ntu.edu.tw/~sirius42/papers/chen14eccv.pdf)|Raw|2,000|163,446|-|-|[Google Drive](https://drive.google.com/file/d/1syrMyJGeXYxbjbmWKLxo1ASzpj2DRrk3/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/13XI67Zn_D_Kncp_9hlTbQQ)|
|[YTF](http://www.cs.tau.ac.il/~wolf/ytfaces/WolfHassnerMaoz_CVPR11.pdf)|Align_344x344|1,595|-|3,425|621,127|[Google Drive](https://drive.google.com/file/d/1o_5b7rYcSEFvTmwmEh0eCPsU5kFmKN_Y/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1M43AcijgGrurb0dfFVlDKQ)|
|[DeepGlint](http://trillionpairs.deepglint.com)|Align_112x112|180,855|6,753,545|-|-|[Google Drive](https://drive.google.com/file/d/1Lqvh24913uquWxa3YS_APluEmbNKQ4Us/view?usp=sharing)|
* Remark: unzip [CASIA-WebFace](https://arxiv.org/pdf/1411.7923.pdf) clean version with 
  ```
  unzip casia-maxpy-clean.zip    
  cd casia-maxpy-clean    
  zip -F CASIA-maxpy-clean.zip --out CASIA-maxpy-clean_fix.zip    
  unzip CASIA-maxpy-clean_fix.zip
  ```
* Remark: after unzip, get image data \& pair ground truths from [AgeDB](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w33/papers/Moschoglou_AgeDB_The_First_CVPR_2017_paper.pdf), [CFP](http://www.cfpw.io/paper.pdf), [LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf) and [VGGFace2_FP](https://arxiv.org/pdf/1710.08092.pdf) align_112x112 versions with 
  ```python
  import numpy as np
  import bcolz
  import os

  def get_pair(root, name):
      carray = bcolz.carray(rootdir = os.path.join(root, name), mode='r')
      issame = np.load('{}/{}_list.npy'.format(root, name))
      return carray, issame

  def get_data(data_root):
      agedb_30, agedb_30_issame = get_pair(data_root, 'agedb_30')
      cfp_fp, cfp_fp_issame = get_pair(data_root, 'cfp_fp')
      lfw, lfw_issame = get_pair(data_root, 'lfw')
      vgg2_fp, vgg2_fp_issame = get_pair(data_root, 'vgg2_fp')
      return agedb_30, cfp_fp, lfw, vgg2_fp, agedb_30_issame, cfp_fp_issame, lfw_issame, vgg2_fp_issame

  agedb_30, cfp_fp, lfw, vgg2_fp, agedb_30_issame, cfp_fp_issame, lfw_issame, vgg2_fp_issame = get_data(DATA_ROOT)
  ```
* Due to release license issue, for other face related databases, please make contact with us in person for more details.

****
### Model Zoo 
:monkey:

* Model

  |Backbone|Head|Loss|Training Data|Download Link|
  |:---:|:---:|:---:|:---:|:---:|
  |[IR-50](https://arxiv.org/pdf/1602.07261.pdf)|[ArcFace](https://arxiv.org/pdf/1801.07698.pdf)|[Focal](https://arxiv.org/pdf/1708.02002.pdf)|[MS-Celeb-1M_Align_112x112](https://arxiv.org/pdf/1607.08221.pdf)|[Google Drive](https://drive.google.com/file/d/1SKhC-iwZ5yX8kDv9mpLiC94ixLIwGd99/view?usp=sharing), [Baidu Drive](https://pan.baidu.com/s/1RKvDsU9CJNtNJiNk8PjU1Q)|

  * Setting
    ```
    INPUT_SIZE: [112, 112]; RGB_MEAN: [0.5, 0.5, 0.5]; RGB_STD: [0.5, 0.5, 0.5]; BATCH_SIZE: 1024 (drop the last batch to ensure consistent batch_norm statistics); Initial LR: 0.1; WEIGHT_DECAY: 5e-4 (do not apply to batch_norm parameters); MOMENTUM: 0.9; Augmentation: Random Crop + Horizontal Flip; Imbalanced Data Processing: Weighted Random Sampling; Solver: SGD; GPUs: 8 NVIDIA Tesla P40 in Parallel
    ```
  * Training \& validation statistics
    * Stage1 (```LR: 0.1; EPOCH: 1-9; TIME: ~1.95s/iter```)

      <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig13.png" width="1000px"/>
      
    * Stage2 (```LR: 0.01; EPOCH: 10-24; TIME: ~1.95s/iter```)

      <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig15.png" width="1000px"/>
      
    * Stage3 (```LR: 0.001; EPOCH: 25-30; TIME: ~1.95s/iter```)

      <img src="https://github.com/ZhaoJ9014/face.evoLVe.PyTorch/blob/master/disp/Fig16.png" width="1000px"/>

  * Performance (havn't converge well yet; can be further improved by training up to 30 epochs per stage; will release a better model soon)

    |[LFW](https://hal.inria.fr/file/index/docid/321923/filename/Huang_long_eccv2008-lfw.pdf)|[CFP_FF](http://www.cfpw.io/paper.pdf)|[CFP_FP](http://www.cfpw.io/paper.pdf)|[AgeDB](http://openaccess.thecvf.com/content_cvpr_2017_workshops/w33/papers/Moschoglou_AgeDB_The_First_CVPR_2017_paper.pdf)|[CALFW](https://arxiv.org/pdf/1708.08197.pdf)|[CPLFW](http://www.whdeng.cn/CPLFW/Cross-Pose-LFW.pdf)|[Vggface2_FP](https://arxiv.org/pdf/1710.08092.pdf)|
    |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
    |99.73|99.71|97.15|97.64|95.81|90.00|94.88|

* Model

  |Backbone|Head|Loss|Training Data|Download Link|
  |:---:|:---:|:---:|:---:|:---:|
  |[IR-50](https://arxiv.org/pdf/1602.07261.pdf)|[ArcFace](https://arxiv.org/pdf/1801.07698.pdf)|[Focal](https://arxiv.org/pdf/1708.02002.pdf)|Private Asia Face Data| |

  * Setting
    ```
    INPUT_SIZE: [112, 112]; RGB_MEAN: [0.5, 0.5, 0.5]; RGB_STD: [0.5, 0.5, 0.5]; BATCH_SIZE: 1024 (drop the last batch to ensure consistent batch_norm statistics); Initial LR: 0.01 (initialize weights from the above model pre-trained on MS-Celeb-1M_Align_112x112); NUM_EPOCH: 80; WEIGHT_DECAY: 5e-4 (do not apply to batch_norm parameters); MOMENTUM: 0.9; STAGES: [20, 40, 60]; Augmentation: Random Crop + Horizontal Flip; Imbalanced Data Processing: Weighted Random Sampling; Solver: SGD; GPUs: 8 NVIDIA Tesla P40 in Parallel
    ```
  * Training \& validation statistics

  * Performance (please perform evaluation on your own Asia face benchmark dataset)

****
### Achievement 
:confetti_ball:

* 2017 No.1 on ICCV 2017 MS-Celeb-1M Large-Scale Face Recognition [Hard Set](https://www.msceleb.org/leaderboard/iccvworkshop-c1)/[Random Set](https://www.msceleb.org/leaderboard/iccvworkshop-c1)/[Low-Shot Learning](https://www.msceleb.org/leaderboard/c2) Challenges. [WeChat News](http://mp.weixin.qq.com/s/-G94Mj-8972i2HtEcIZDpA), [NUS ECE News](http://ece.nus.edu.sg/drupal/?q=node/215), [NUS ECE Poster](https://zhaoj9014.github.io/pub/ECE_Poster.jpeg), [Award Certificate for Track-1](https://zhaoj9014.github.io/pub/MS-Track1.jpeg), [Award Certificate for Track-2](https://zhaoj9014.github.io/pub/MS-Track2.jpeg), [Award Ceremony](https://zhaoj9014.github.io/pub/MS-Awards.jpeg).
* 2017 No.1 on National Institute of Standards and Technology (NIST) IARPA Janus Benchmark A (IJB-A) Unconstrained Face [Verification](https://zhaoj9014.github.io/pub/IJBA_11_report.pdf) challenge and [Identification](https://zhaoj9014.github.io/pub/IJBA_1N_report.pdf) challenge. [WeChat News](https://mp.weixin.qq.com/s/s9H_OXX-CCakrTAQUFDm8g).

* State-of-the-art performance on 

    * MS-Celeb-1M (Challenge1 Hard Set Coverage@P=0.95: 79.10%; Challenge1 Random Set Coverage@P=0.95: 87.50%; Challenge2 Development Set Coverage@P=0.99: 100.00%; Challenge2 Base Set Top 1 Accuracy: 99.74%; Challenge2 Novel Set Coverage@P=0.99: 99.01%).
    * IJB-A (1:1 Veification TAR@FAR=0.1: 99.6%±0.1%; 1:1 Veification TAR@FAR=0.01: 99.1%±0.2%; 1:1 Veification TAR@FAR=0.001: 97.9%±0.4%; 1:N Identification FNIR@FPIR=0.1: 1.3%±0.3%; 1:N Identification FNIR@FPIR=0.01: 5.4%±4.7%; 1:N Identification Rank1 Accuracy: 99.2%±0.1%; 1:N Identification Rank5 Accuracy: 99.7%±0.1%; 1:N Identification Rank10 Accuracy: 99.8%±0.1%).
    * IJB-C (1:1 Veification TAR@FAR=1e-5: 82.6%).
    * Labeled Faces in the Wild (LFW) (Accuracy: 99.85%±0.217%).
    * Celebrities in Frontal-Profile (CFP) (Frontal-Profile Accuracy: 96.01%±0.84%; Frontal-Profile EER: 4.43%±1.04%; Frontal-Profile AUC: 99.00%±0.35%; Frontal-Frontal Accuracy: 99.64%±0.25%; Frontal-Frontal EER: 0.54%±0.37%; Frontal-Frontal AUC: 99.98%±0.03%).
    * CMU Multi-PIE (Rank1 Accuracy Setting-1 under ±90°: 76.12%; Rank1 Accuracy Setting-2 under ±90°: 86.73%).
    * MORPH Album2 (Rank1 Accuracy Setting-1: 99.65%; Rank1 Accuracy Setting-2: 99.26%).
    * CACD-VS (Accuracy: 99.76%).
    * FG-NET (Rank1 Accuracy: 93.20%).

****
### Acknowledgement 
:two_men_holding_hands:

* This repo is inspired by [InsightFace.MXNet](https://github.com/deepinsight/insightface), [InsightFace.PyTorch](https://github.com/TreB1eN/InsightFace_Pytorch), [ArcFace.PyTorch](https://github.com/ronghuaiyang/arcface-pytorch), [MTCNN.MXNet](https://github.com/pangyupo/mxnet_mtcnn_face_detection) and [PretrainedModels.PyTorch](https://github.com/Cadene/pretrained-models.pytorch).
* The work of Jian Zhao was partially supported by China Scholarship Council (CSC) grant 201503170248.
* We would like to thank [Prof. Jiashi Feng](https://sites.google.com/site/jshfeng/), [Dr. Jianshu Li](https://sites.google.com/view/li-js), Mr. Yu Cheng (Learning and Vision group, National University of Singapore), Mr. Yuan Xin, Mr. Di Wu, Mr. Zhenyuan Shen, Mr. Jianwei Liu (Tencent FiT DeepSea AI Lab, China), [Prof. Ran He](http://www.nlpr.ia.ac.cn/english/irds/People/rhe.html), [Prof. Junliang Xing](http://people.ucas.ac.cn/~0051452?language=en), [Mr. Xiang Wu](http://alfredxiangwu.github.io/) (Institute of Automation, Chinese Academy of Sciences), [Prof. Guosheng Hu](https://www.linkedin.com/in/guosheng-hu-6801b333/) (AnyVision Inc., U.K.), [Dr. Lin Xiong](https://bruinxiong.github.io/xionglin.github.io/) (JD Digits AI Lab, U.S.), Miss Yi Cheng (Panasonic R\&D Center, Singapore) for helpful discussions.

****
### Citation 
:bookmark_tabs:

- Please consult and consider citing the following papers:


      @article{zhao2018look,
      title={Look Across Elapse: Disentangled Representation Learning and Photorealistic Cross-Age Face Synthesis for Age-Invariant Face Recognition},
      author={Zhao, Jian and Cheng, Yu and Cheng, Yi and Yang, Yang and Lan, Haochong and Zhao, Fang and Xiong, Lin and Xu, Yan and Li, Jianshu and Pranata, Sugiri and others},
      journal={AAAI},
      year={2019}
      }
      
      
      @article{zhao20183d,
      title={3D-Aided Dual-Agent GANs for Unconstrained Face Recognition},
      author={Zhao, Jian and Xiong, Lin and Li, Jianshu and Xing, Junliang and Yan, Shuicheng and Feng, Jiashi},
      journal={T-PAMI},
      year={2018}
      }
      
      
      @inproceedings{zhao2018towards,
      title={Towards Pose Invariant Face Recognition in the Wild},
      author={Zhao, Jian and Cheng, Yu and Xu, Yan and Xiong, Lin and Li, Jianshu and Zhao, Fang and Jayashree, Karlekar and Pranata,         Sugiri and Shen, Shengmei and Xing, Junliang and others},
      booktitle={CVPR},
      pages={2207--2216},
      year={2018}
      }
      
      
      @inproceedings{zhao2017dual,
      title={Dual-agent gans for photorealistic and identity preserving profile face synthesis},
      author={Zhao, Jian and Xiong, Lin and Jayashree, Panasonic Karlekar and Li, Jianshu and Zhao, Fang and Wang, Zhecan and Pranata,           Panasonic Sugiri and Shen, Panasonic Shengmei and Yan, Shuicheng and Feng, Jiashi},
      booktitle={NIPS},
      pages={66--76},
      year={2017}
      }
      
      
      @inproceedings{zhao3d,
      title={3D-Aided Deep Pose-Invariant Face Recognition},
      author={Zhao, Jian and Xiong, Lin and Cheng, Yu and Cheng, Yi and Li, Jianshu and Zhou, Li and Xu, Yan and Karlekar, Jayashree and       Pranata, Sugiri and Shen, Shengmei and others},
      booktitle={IJCAI},
      pages={1184--1190},
      year={2018}
      }


      @inproceedings{cheng2017know,
      title={Know you at one glance: A compact vector representation for low-shot learning},
      author={Cheng, Yu and Zhao, Jian and Wang, Zhecan and Xu, Yan and Jayashree, Karlekar and Shen, Shengmei and Feng, Jiashi},
      booktitle={ICCVW},
      pages={1924--1932},
      year={2017}
      }
