---
layout: post
title: "Building LLMs from Scratch"
date: 2026-03-30
---

**DeepSeekV3:** You can find the full code here: [https://github.com/pandey-ps/papers/tree/main/deepseekv3](https://github.com/pandey-ps/papers/tree/main/deepseekv3)

8x2 MoE DeepSeekV3 model from scratch in pytorch; this is a decoder only transformer with optional multi token prediction (had to disable it during training as it created memory overhead).

It is trained on the tinystories dataset using the GPT2 tokenizer (had to tokenize separately and kept on disk as it caused memory overflow even though it was streamed and chunked!), the jupyter notebook in the link has the whole walkthrough on training the end to end model. All the architecture details are present in the readme too. I did not implement RoPE like it is in the main architecture instead i used Sinusoidal Positional Encodings for simplicity.

The model speaks coherent english and generated good enough stories as I trained it only for 10000 steps with loss falling to about 2.4 on train data and 2.7 on the validation data split. Training was done on a kaggle set of 2 parallel T4 GPUs, so this whole architecture took about 4 hours to train.

Next plan is to build LLaMA from scratch. (will update this same blog post when i am done with).
