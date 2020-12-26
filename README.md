# PhoNLP: A joint multi-task learning model for Vietnamese part-of-speech tagging, named entity recognition and dependency parsing

PhoNLP is a multi-task learning model for joint part-of-speech (POS) tagging, named entity recognition (NER) and dependency parsing. Experiments on Vietnamese benchmark datasets show that PhoNLP produces state-of-the-art results, outperforming a single-task learning approach that  fine-tunes the pre-trained Vietnamese language model [PhoBERT](https://github.com/VinAIResearch/PhoBERT) for each task independently.

## Installation

- Python version >= 3.6; PyTorch version >= 1.4.0
- PhoNLP can be installed using `pip` as follows: `pip3 install phonlp`
- Or PhoNLP can also be installed from source with the following commands: 
	```
	git clone https://github.com/VinAIResearch/PhoNLP
	cd PhoNLP
	pip3 install -e .
	```

## Usage example: Command lines

To play with the examples using command lines, please install `phonlp` from the source:	
```
git clone https://github.com/VinAIResearch/PhoNLP
cd PhoNLP
pip3 install -e . 
```

Although we specify PhoNLP for Vietnamese, the following command scripts in fact can directly work for other languages that have gold annotated corpora available for the three tasks of POS tagging, NER and dependency parsing, and a pre-trained BERT-based language model available from [transformers](https://huggingface.co/models).

### Training

```
cd phonlp/models
python3 run_phonlp.py --mode train --save_dir <model_folder_path> \
	--pretrained_lm <transformers_pretrained_model> \
	--lr <float_value> --batch_size <int_value> --num_epoch <int_value>\
	--lambda_pos <float_value> --lambda_ner <float_value> --lambda_dep <float_value> \
	--train_file_pos <path_to_training_file_pos> --eval_file_pos <path_to_validation_file_pos> \
	--train_file_ner <path_to_training_file_ner> --eval_file_ner <path_to_validation_file_ner> \
	--train_file_dep <path_to_training_file_dep> --eval_file_dep <path_to_validation_file_dep>
```

`--lambda_pos`, `--lambda_ner` and  `--lambda_dep` represent mixture weights associated with POS tagging, NER and dependency parsing losses, respectively, and `lambda_pos + lambda_ner + lambda_dep = 1`.

Example:

```
python3 run_phonlp.py --mode train --save_dir ./phonlp_tmp \
	--pretrained_lm "vinai/phobert-base"
	--lr 1e-5 --batch_size 32 --num_epoch 40 \
	--lambda_pos 0.4 --lambda_ner 0.2 --lambda_dep 0.4 \
	--train_file_pos ../sample_data/pos_train.txt --eval_file_pos ../sample_data/pos_valid.txt \
	--train_file_ner ../sample_data/ner_train.txt --eval_file_ner ../sample_data/ner_valid.txt \
	--train_file_dep ../sample_data/dep_train.txt --eval_file_dep ../sample_data/dep_valid.txt
```

### Evaluation

```
cd phonlp/models
python3 run_phonlp.py --mode eval --save_dir <model_folder_path> \
	--eval_file_pos <path_to_test_file_pos> \
	--eval_file_ner <path_to_test_file_ner> \
	--eval_file_dep <path_to_test_file_dep>
```

Example:

```
python3 run_phonlp.py --mode eval --save_dir ./phonlp_tmp \
	--eval_file_pos ../sample_data/pos_test.txt \
	--eval_file_ner ../sample_data/ner_test.txt \
	--eval_file_dep ../sample_data/dep_test.txt
```


### Annotate a corpus

```
cd phonlp/models
python3 run_phonlp.py --mode annotate --save_dir <model_folder_path> \
	--input_file <path_to_input_file> --output_file <path_to_output_file> 
```

Example:

```
python3 run_phonlp.py --mode annotate --save_dir ./phonlp_tmp \
	--input_file ../sample_data/input.txt --output_file ../sample_data/output.txt 
```

## Usage example: Python API

```python
import phonlp
# Download the pre-trained PhoNLP model 
# and save it in a local machine folder
phonlp.download(save_dir='./pretrained_phonlp')
# Load the pre-trained PhoNLP model
model = phonlp.load(save_dir='./pretrained_phonlp')
# Annotate a corpus where each line represents a word-segmented sentence
model.annotate(input_file='input.txt', output_file='output.txt')
# Annotate a word-segmented sentence
model.print_out(model.annotate(text="Tôi đang làm_việc tại VinAI ."))
```

By default, the output for each input sentence is formatted with 6 columns representing word index, word form, POS tag, NER label, head index of the current word and its dependency relation type:

```
1	Tôi		P	O	3	sub	
2	đang	R	O	3	adv
3	làm_việc	V	O	0	root
4	tại		E	O	3	loc
5	VinAI	Np 	B-ORG	4	prob
6	.		CH	O	3	punct
```

In addition, the output can be formatted following the 10-column CoNLL format where the last column is used to represent NER predictions. This can be done by adding `output_type='conll'` into the `model.annotate()` function. Also, in the `model.annotate()` function, the value of the parameter `batch_size` can be adjusted to fit your computer's memory instead of using the default one at 8  (`batch_size=8`). Here, a larger `batch_size` would lead to a faster performance speed.

## License

	MIT License

	Copyright (c) 2020 VinAI Research

	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:

	The above copyright notice and this permission notice shall be included in all
	copies or substantial portions of the Software.

	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
	SOFTWARE.
