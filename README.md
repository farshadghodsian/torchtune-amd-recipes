# torchtune-amd-recipes
Torchtune recipes for fine-tuning on AMD Radeon GPUs


# Torchtune Lora Fine-tuning Llama3.2-Vision-11B Model

## Prerequisites
- wandb.ai account and API Key

### Wandb Account:
In order to export training metrics to Weights and Biases (wandb.ai) you will need to have already created an account on http://wandb.ai and saved your api-key to an environment variable

```
YOUR_API_KEY=ABCxxxxxxxxxxxxxxxxx123
```

</br>
If you do not want to sync metrics to Weights and Biases or want to skip this you can run in offline mode. Note that this is not recommended and you should instead choose another logging method in your torchtune config file such as `torchtune.training.metric_logging.DiskLogger`.

```
WANDB_MODE=offline
```
</br>

## 1. Clone Torchtune repo
```
git clone https://github.com/pytorch/torchtune.git
```
</br>

## 2. CD into directory
```
cd torchtune
```
</br>

## 3. Creation Python Virtual Environment (optional, but recommended)

Create Torchtune virtual environment and activate venv
```
python3 -m venv .venv/torchtune
source .venv/torchtune/bin/activate
```

## 4. Install Torchtune
```
pip install -e .
```
</br>

## 5. Install PyTorch + ROCm
```
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm6.2
```

## 6. Install package dependancies
```
pip install torchao wandb
```
</br>

## 7. Set WandB Parameters
```
WANDB_API_KEY=$YOUR_API_KEY
WANDB_NAME="llama3.2-vision-11b-instruct Lora Fine-tuning Demo"
WANDB_NOTES="Demo of Lora Fine-tuning Llama3.2-vision-11b-intruct model on multi-modal image + text dataset on AMD GPUs"
```
</br>

Note: If the above WandB Environment variables aren't working you can log into you WandB account with your API key manually by doing:
```
wandb login
```
</br>


## 8. Download llama3.2-vision-11b-instruct model
```
tune download meta-llama/Llama-3.2-11B-Vision-Instruct --output-dir /tmp/Llama-3.2-11B-Vision-Instruct --ignore-patterns "original/consolidated*"
```
</br>

## 9. Download Recipie Files
```
wget https://raw.githubusercontent.com/farshadghodsian/torchtune-amd-recipes/refs/heads/main/recipes/configs/llama3_2_vision/llama3.2-vision-11b-lora-finetune.yaml

wget https://raw.githubusercontent.com/farshadghodsian/torchtune-amd-recipes/refs/heads/main/recipes/configs/llama3_2_vision/llama3.2-vision-11b-lora-finetune-single-device.yaml
```
</br>

## 10. Start Fine-tuning Run 
### (Option 1) Run Lora Multi-GPU Fine-tuning
```
tune run --nproc_per_node 8 lora_finetune_distributed --config ./llama3.2-vision-11b-lora-finetune.yaml
```
</br>

### (Option 2) Run Lora Sigle-GPU Fine-tuning
```
tune run lora_finetune_single_device --config ./llama3.2-vision-11b-lora-finetune-single-device.yaml
```
</br>

## Running On Radeon and Radeon Pro GPUs (RDNA3)
Radeon GPUs now have experimental support for Flash Attention through OpenAI's Triton (AOTrition). To enable this support you will need to set the following environment variable:
```
TORCH_ROCM_AOTRITON_ENABLE_EXPERIMENTAL=1 
```

In addition to Experimental Flash Attention support PyTorch will defaul to tusing HIPBLASLT for AMD GPUs, however Radeon GPUs do not support the HIPBLASLT library so you will need to default to using HIPBLAS instead. The latest version of PyTorch does this by default, but will throw warnings about it. To disable the warnings set the following environment variable:
```
TORCH_BLAS_PREFER_HIPBLASLT=0 
```

You can include these two env vars in the training run command by adding them before your `tune run` command as follows:

```
TORCH_BLAS_PREFER_HIPBLASLT=0 TORCH_ROCM_AOTRITON_ENABLE_EXPERIMENTAL=1 tune run lora_finetune_single_device --config ./llama3.2-vision-11b-lora-finetune-single-device.yaml 
```