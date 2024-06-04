# SpeechSRL
This repository contains the dataset and code for the ACL Findings 2024 paper "Semantic Role Labeling from Chinese Speech via End-to-End Learning", by Huiyao Chen, Xinxin Li, Meishan Zhang*, Min Zhang.
The dataset is included in the data/ folder.
Please cite our paper if it is helpful to your work.

## Requirements:
To run our code, please install all the dependency packages by using the following command:

```
pip install -r requirements.txt
```

## Data Preparation
For CPB1.0 dataset, the predicate-argument relations needs to be downloaded [here](https://catalog.ldc.upenn.edu/LDC2005T23), which is not publicly available, so we only provide the corresponding recording files. 
For AS-SRL dataset, we provide jsonl files that have been preprocessed with the data format that needs to be provided during the training process:
```
{
   "id": int,
   "audio": {
      "path": str
   },
   "sentence": str,
   "duration": float,
   "srl": [{"pred": str, "position":[int, int], "arguments": [{"value": str, "position":[int, int], "role": str}]}]
}
```

## Model Preparation
Download the corresponding [Whisper](https://huggingface.co/openai/whisper-large-v2) and [BERT](https://huggingface.co/google-bert/bert-base-chinese) models in hugging face.

## Pretrain Whisper
To pretrain Whipser, run:
```
bash pretrain_whisper.sh
python merge_lora.py --lora_model= --output_dir=
```

## Construct Alignment Tool
To construct the alignment tool, run
```
python generate_align_data.py
cd fast_align
bash fast_algn.sh
```

## Train SRL Model Or Joint Model
All models, whether they train only SRL or a combination of speech and SRL, are implemented using the `train.sh` file, except for individual parameter settings that are different. If you want to train the model, run:
```
bash train.sh
```

For SRLGParser(oracle), set parameter:
```
is_e2e=False
```

For SRLGParser(+GS AUG), in addition to being consistent with the SRLGParser(oracle) training parameters described above, the data needs to be generated by Gumbel Softmax first and then merged with the original training data:
```
python generate_aug_data.py
python merge.py
```

For Whisper◦SRLGParser(gold SRL) which is a end-to-end learning model, set parameter:
```
is_e2e=True
is_remove_augment=True
```

For Whisper◦SRLGParser, which is a end-to-end learning model and performs best in the SRL from speech, set parameter:
```
is_e2e=True
is_use_gumbel_softmax_loss=True
```
**Notes** If you want to reproduce the result of the paper, use `get_metric_old.py` when training, we have updated the mapping function to get better alignment in `get_metric.py`.

## Evaluate
```
python evaluation.py
```

## Cite
If you use the code, please cite this paper.
