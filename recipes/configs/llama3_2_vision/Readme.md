# Torchtune Lora Fine-tuning Llama3.2-Vision-11B Model

## Prerequisites
In order to export training metrics to Weights and Biases (wandb.ai) you will need to have already created an account on http://wandb.ai and saved your api-key to an environment variable

```
$YOUR_API_KEY=ABCxxxxxxxxxxxxxxxxx123
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

## 3. Install Torchtune
```
pip install -e .
```
</br>

## 4. Install wandb
```
pip install wandb
```
</br>

## 5. Set WandB Parameters
```
WANDB_API_KEY=$YOUR_API_KEY
WANDB_NAME="llama3.2-vision-11b-instruct Lora Fine-tuning Demo"
WANDB_NOTES="Demo of Lora Fine-tuning Llama3.2-vision-11b-intruct model on multi-modal image + text dataset on AMD GPUs"
```
</br>

## 6. Download llama3.2-vision-11b-instruct model
```
tune download meta-llama/Llama-3.2-11B-Vision-Instruct --output-dir /tmp/Llama-3.2-11B-Vision-Instruct --ignore-patterns "original/consolidated*"
```
</br>

## 7. Download Recipie Files
```
wget https://raw.githubusercontent.com/farshadghodsian/torchtune-amd-recipes/refs/heads/main/recipes/configs/llama3_2_vision/llama3.2-vision-11b-lora-finetune.yaml

wget https://raw.githubusercontent.com/farshadghodsian/torchtune-amd-recipes/refs/heads/main/recipes/configs/llama3_2_vision/llama3.2-vision-11b-lora-finetune-single-device.yaml
```
</br>

## 8. Start Fine-tuning Run 
### (Option 1) Run Lora Multi-GPU Fine-tuning
```
tune run --nproc_per_node 8 lora_finetune_distributed --config ./llama3.2-vision-11b-lora-finetune.yaml
```
</br>

### (Option 2) Run Lora Sigle-GPU Fine-tuning
```
tune run lora_finetune_single_device --config ./llama3.2-vision-11b-lora-finetune-single-device.yaml
```