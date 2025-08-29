# FYP_NIGHTSHADE

This repo contains the research code for Nightshade by the University of Chicago, forked for my Final Year Project.

## OVERVIEW

The repo contains code to generate Nightshade data for a source concept (e.g. "dog"). Specifically,

1) `data_extraction.py` first identifies an optimal set of clean image/text pairs from a pool of clean data from the source concept. This step is designed to find a good set of text prompts to use for the poison.
2) `gen_poison.py` optimizes a perturbation on each of the selected candidate images.

### HOW TO

#### Step 1: Candidate Data Selection

We first extract a desired set of clean image/text pairs as the starting point of poison generation. Given a source concept (e.g. "dog"), you need to collect a large set (> 500) image/text pairs that contain the source concept. If you do not have text prompts, you can use BLIP or similar techniques to generate the prompts or simply use "a photo of X" for the prompts.

**Data format:** To better store longer prompts, we use pickle files for image/text pairs. Each pickle file contains a numpy image (key "img") and its corresponding text prompt (key "text"). 

Next, run `data_extraction.py` to select a set of 100 poison candidates. `python data_extraction.py --directory data/ --concept dog --num 100 --outdir selected_data/`

#### Step 2: Poison Generation

Next, we add perturbation to the images given a target concept (e.g. "cat"). `python gen_poison.py --directory selected_data/ --target_name cat --outdir poisoned_data/`. The code will output the perturbed images to the output folder.

#### Step 3: Data Processing

The tool I used to finetune a Stable Diffusion model was [EveryDream2trainer](https://github.com/victorchall/EveryDream2trainer). The paper said not to use LORA or dreambooth so this was the perfect tool to use, since its a general case fine tuner.

Their accepted data format is a jpg and txt file for the caption, so I had to write some code to process the pickle files to separate them into their corresponding image and txt files.

#### Step 4: Training the Model

I transferred the dataset into the repository and ran `python train.py --config train.json`, your parameters may vary, but I used 80 epochs for 200-300 images.

#### Step 5: Generating from the Poisoned Model

I used [stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) to generate the images. After setting up, I downloaded the .safetensors file from EveryDream2trainer and dropped it into the `models/Stable-diffusion` folder. It also came with a clean SD 1.5 model for me to run control tests.

### Citation

```
@inproceedings{shan2024nightshade,
  title={Nightshade: Prompt-Specific Poisoning Attacks on Text-to-Image Generative Models},
  author={Shan, Shawn and Ding, Wenxin and Passananti, Josephine and Wu, Stanley and Zheng, Haitao and Zhao, Ben Y.},
  booktitle={IEEE Symposium on Security and Privacy},
  year={2024},
}
```

For any questions with the code, please email shawnshan@cs.uchicago.edu. 
