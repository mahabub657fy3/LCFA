---
# Learning to Attack: Frequency-Aware Attack via CLIP-Guided Learnable Conditioning

<img width="1051" height="290" alt="Overall_pipeline" src="https://github.com/user-attachments/assets/9f30c09a-b04f-4068-a075-73adf7ce88cf" />


## **Abstract**

Deep neural networks (DNNs) are highly susceptible to adversarial examples, raising substantial concerns in safety-critical applications. Conditional generative attacks have emerged as an attractive research direction due to their strong transferability and efficient single-pass inference. However, existing multi-target methods condition the generator on fixed text templates that are never optimized for the adversarial objective and inject perturbations directly into clean source images, introducing a semantic conflict that suppresses target-class signals. To address these limitations, we propose Learnable Conditioning for Frequency-Aware Attacks (LCFA), a data-efficient generative attack framework that couples CLIP-guided learnable prompt conditioning with low-frequency perturbation injection. LCFA optimizes continuous per-class context vectors in the CLIP embedding space. This allows the conditioning signal to adapt the attack objective and explore target-aligned semantic directions beyond static prompts. Rather than perturbing the clean image directly, LCFA embeds the perturbation in the low-pass filtered image. This decouples the adversarial signal from the source-class semantics by exploiting the role of low-frequency content in targeted classification decisions. Together, these two principles substantially enhance cross-model semantic generalization while requiring only a limited training subset. Comprehensive experiments on ImageNet and CIFAR-10 show that LCFA achieves state-of-the-art targeted transferability. Notably, LCFA achieves a 22.03% higher success rate than the best baseline when transferring from ResNet-50 to DenseNet121 on 100k ImageNet samples.

---
Average targeted success rate (TSR) as a function of training dataset size (10k–100k). Shaded regions indicate the absolute TSR improvement of our method over CGNC.
<img width="2664" height="1616" alt="ASR_comparison_revamped" src="https://github.com/user-attachments/assets/eacaaba4-36f8-4561-8569-f6a89dd3d047" />


## **Features**

* **One-click Evaluation:** Generate adversarial examples for specified classes using a pre-trained or trained model.
* **One-click Inference:** Easily evaluate the performance of the adversarial attack on CIFAR-10 or ImageNet datasets.
* **One-click Training:** Train the model from scratch or resume from a checkpoint with minimal setup.
* **Pretrained Models:** Download pretrained models from Google Drive or upload your own model checkpoint (`.pth` file).

---

## **Pipeline Overview**

The overall pipeline for the LP-LFGA framework is as follows:

1. **Data Input:** Input images are processed through data augmentation and normalization.
2. **Frequency Decomposition:** The low-frequency components of the image are extracted using a Gaussian filter.
3. **CLIP-Guided Conditioning:** The model is conditioned using text embeddings from CLIP, either precomputed or learned through a prompt module.
4. **Adversarial Generation:** The conditional generator produces adversarial examples that perturb the low-frequency components.
5. **Model Evaluation:** The generated adversarial examples are evaluated for transferability and effectiveness against the victim model.

---

## **Installation**

### **1. Setup Environment**

First, clone the repository and set up the required environment using the provided `environment.yml` file:

```bash
git clone https://github.com/mahabub657fy3/LCFA
cd LCFA
conda env create -f environment.yml
conda activate lp-lfga
```

### **2. Download Pretrained Models**

You can download the pretrained models directly from Google Drive or upload your own `.pth` files.

* **[Download Pretrained Models](https://drive.google.com/drive/folders/1HhYsKmOBHC17ypRZojs4MiTIV8Z3h4af?usp=sharing) **

Or, upload your own `.pth` files to the `models/` directory.

---

## **Usage**

### **One-click Evaluation**

Generate Adversarial Examples on CIFAR-10 or ImageNet, use the following commands:

#### **CIFAR-10 Evaluation:**
Below we provide running commands for generating targeted adversarial examples on [Cifar-10](https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz)  under our multi-class setting:

```bash
python eval_cifar10.py \
    --dataset cifar10 \
    --data_dir path/to/cifar10/test \
    --batch_size 5 \
    --eps 16 \
    --model_type cifar10_resnet56 \
    --load_path checkpoints/cifar10/model-9.pth \
    --label_flag C5 \
    --nz 16 \
    --save_dir results_cifar10 \
    --clip_backbone ViT-B/16 \
    --ctx_dim 512 \
    --prompt_ckpt checkpoints/cifar10/prompt-9.pth \
    --k 2
```

#### **ImageNet Evaluation:**
Below we provide running commands for generating targeted adversarial examples on [ImageNet NeurIPS validation set](https://www.kaggle.com/c/nips-2017-non-targeted-adversarial-attack) (1k images) under our multi-class setting:

```bash
python eval_imagenet.py \
    --dataset imagenet \
    --data_dir path/to/imagenet/val \
    --is_nips \
    --batch_size 5 \
    --eps 16 \
    --model_type res50 \
    --load_path checkpoints/imagenet/model-9.pth \
    --label_flag N8 \
    --nz 16 \
    --save_dir results_imagenet \
    --clip_backbone ViT-B/16 \
    --ctx_dim 512 \
    --prompt_ckpt checkpoints/imagenet/prompt-9.pth \
    --k 4
```

This will load the pretrained model, Generate adversarial examples using the trained generator, and store the generated samples in the `results/` directory.

---

### **One-click Inference**

 Here are the commands to run inference to Evaluate Target Success Rate:

#### **CIFAR-10 Inference:**

```bash
python inference.py --dataset cifar10 --data_dir ./cifar10/test --batch_size 5 --eps 16 --model_type cifar
```

#### **ImageNet Inference:**

```bash
python inference.py --dataset imagenet --data_dir ./imagenet/test --batch_size 5 --eps 16 --model_t normal --save_dir ./output_imagenet
```

This will Evaluate Attack Success Rate.

---

### **One-click Training**

To train the model from scratch or resume from a checkpoint, use the following commands:

#### **CIFAR-10 Training:**

```bash
python train_cifar10.py \
    --dataset cifar10 \
    --train_dir path/to/cifar10/train \
    --batch_size 128 \
    --epochs 10 \
    --lr 2e-4 \
    --eps 16 \
    --model_type cifar10_resnet56 \
    --label_flag C5 \
    --nz 16 \
    --save_dir checkpoints_cifar10 \
    --clip_backbone ViT-B/16 \
    --ctx_dim 512 \
    --k 2
```

#### **ImageNet Training:**

```bash
python train_imagenet.py \
    --dataset imagenet \
    --train_dir path/to/imagenet/train \
    --batch_size 8 \
    --epochs 10 \
    --lr 2e-4 \
    --eps 16 \
    --model_type res50 \
    --label_flag N8 \
    --nz 16 \
    --save_dir checkpoints_imagenet \
    --clip_backbone ViT-B/16 \
    --ctx_dim 512 \
    --k 4
```

The model will be trained for the specified number of epochs and saved periodically in the `checkpoints/` directory.

---
