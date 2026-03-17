# fyp_nightshade

## Overview
This repo contains my implementation of the Nightshade research code by the University of Chicago, the original repository can be found [here](https://github.com/Shawn-Shan/nightshade-release). This repository was created as part of my final year project on investigating data poisoning techniques on text-to-image models.

## Organisation of Repository

```
fyp_nightshade/
├── 300_poisoned_dataset/       # Nightshaded dataset
├── original_data/              # The original clean dataset, in .p format
├── original_extracted_data     # The original clean dataset in image-caption format
├── data_extraction.py          # Candidate data selection script
├── gen_poison.py               # Poison generation script
├── opt.py                      # Auxillary code to aid in gen_poison.py
├── README.md                   # This file
└── target.png                  # The anchor image of the attacker concept
```

## Usage

### Step 1: Candidate Data Selection

First extract a desired set of clean image/text pairs as the starting point of poison generation. Given a source concept (e.g. "dog"), you need to collect a large set (> 500) image/text pairs that contain the source concept. If you do not have text prompts, you can use BLIP or similar techniques to generate the prompts or simply use "a photo of X" for the prompts.

**Data format:** To better store longer prompts, pickle files are used for image/text pairs. Each pickle file contains a numpy image (key "img") and its corresponding text prompt (key "text"). 

Next, run `data_extraction.py` to select a set of poison candidates, for example 100 of them. `python data_extraction.py --directory data/ --concept dog --num 100 --outdir selected_data/`

### Step 2: Poison Generation

Next, we add perturbation to the images given a target concept (e.g. "cat"). `python gen_poison.py --directory selected_data/ --target_name cat --outdir poisoned_data/`. The code will output the perturbed images to the output folder.

### Step 3: Data Processing

The tool I used to finetune a Stable Diffusion model was [EveryDream2trainer](https://github.com/victorchall/EveryDream2trainer). The researchers advised not to use LoRA or Dreambooth. EveryDream2Trainer is a general case fine tuner, hence it is appropriate.

### Step 4: Training the Model

I transferred the dataset into the EveryDream2trainer repository and ran `python train.py --config train.json`, your parameters may vary, but I used 100 epochs for 300 images.

### Step 5: Generating from the Poisoned Model

I used [stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) to generate the images. After setting up, I downloaded the .safetensors file from EveryDream2trainer and dropped it into the `models/Stable-diffusion` folder. It also came with a clean SD 1.5 model for me to run control tests.

## Citation

```
@inproceedings{shan2024nightshade,
  title={Nightshade: Prompt-Specific Poisoning Attacks on Text-to-Image Generative Models},
  author={Shan, Shawn and Ding, Wenxin and Passananti, Josephine and Wu, Stanley and Zheng, Haitao and Zhao, Ben Y.},
  booktitle={IEEE Symposium on Security and Privacy},
  year={2024},
}
```

For any questions with the code, please email shawnshan@cs.uchicago.edu. 
