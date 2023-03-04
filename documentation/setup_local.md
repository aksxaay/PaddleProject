### commands

```sh
cd PaddleOCR
pip install -r requirements.txt
```


```sh
pip install -r ppstructure/kie/requirements.txt
pip install paddlepaddle-gpu==2.3.1
pip install paddleocr==2.5.0.3 -U
```


since I've installed `pyenv` which managers python versions
time to install whatever.

[pyenv documentation](https://realpython.com/intro-to-pyenv/#:~:text=%E2%80%9CSystem%20Python%E2%80%9D%20is%20the%20Python,get%20a%20nice%20Python%20REPL.&text=To%20install%20a%20package%20into,to%20run%20sudo%20pip%20install%20.)

```sh
python local 3.5.10
```
this creates `.python-version` which manages python version for you.

invoke pip the python module way
```sh
python -m pip list
```

```sh
pyenv activate venv_paddle
```

only problem vscode can't detect this whatsoever.
had to upgrade pip to v 23.1

### Prepare the dataset
```sh
mkdir train_data
cd train_data
# download and uncompress the dataset
wget https://paddleocr.bj.bcebos.com/ppstructure/dataset/XFUND.tar && tar -xf XFUND.tar
cd ..
```

### download the models
```sh
mkdir pretrained_model
cd pretrained_model
# download and uncompress the SER trained model
wget https://paddleocr.bj.bcebos.com/ppstructure/models/vi_layoutxlm/ser_vi_layoutxlm_xfund_pretrained.tar && tar -xf ser_vi_layoutxlm_xfund_pretrained.tar

# download and uncompress the RE trained model
wget https://paddleocr.bj.bcebos.com/ppstructure/models/vi_layoutxlm/re_vi_layoutxlm_xfund_pretrained.tar && tar -xf re_vi_layoutxlm_xfund_pretrained.tar
```


if we want OCR engine to obtain end-to-end prediction results:

### SER
```sh
python3 tools/infer_kie_token_ser.py \
  -c configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=./ppstructure/docs/kie/input/zh_val_42.jpg
```

### SER + RE
```sh
# predict using SER and RE trained model at the same time
python3 ./tools/infer_kie_token_ser_re.py \
  -c configs/kie/vi_layoutxlm/re_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/re_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=./train_data/XFUND/zh_val/image/zh_val_42.jpg \
  -c_ser configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o_ser Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy
```

the fix for undefined symbol `core_avx.so`
-> 
### Important
```sh
paddlepaddle=2.4.0.rc0
```


attempt (enable CPU) because I don't wanna do GPU just yet
```sh
python3 ./tools/infer_kie_token_ser_re.py \
  -c configs/kie/vi_layoutxlm/re_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/re_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=./train_data/XFUND/zh_val/image/zh_val_42.jpg \
  Global.use_gpu=False \
  -c_ser configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o_ser Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy
```

trying to resume or something, downloading two models as well.

[path 1](/home/axsae/.paddlenlp/models/layoutxlm-base-uncased/layoutxlm-base-uncased/tokenizer_config.json)

[path 2](/home/axsae/.paddlenlp/models/layoutxlm-base-uncased/layoutxlm-base-uncased/special_tokens_map.json)

JPEG data corrupt

we got the first hit baby let's go
save result to 
[result](../PaddleOCR/output/re/xfund_zh/with_gt/zh_val_42_ser_re.jpg)
sent the copy , changed file name to json formatted
[first ever result](./infer_results.json)


it saves paddle files on home
search paddle on `.home` directory


attempt #2
```sh
python3 ./tools/infer_kie_token_ser_re.py \
  -c configs/kie/vi_layoutxlm/re_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/re_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=../documentation/Invoice.png \
  Global.use_gpu=False \
  -c_ser configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o_ser Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy
```
do not have spaces in the middle of the `sh` commands


Attempt #2 results

[result output](../PaddleOCR/output/re/xfund_zh/with_gt/Invoice_ser_re.jpg) - visualization


okay these are good results

### Attempt #3
```sh
python3 ./tools/infer_kie_token_ser_re.py \
  -c configs/kie/vi_layoutxlm/re_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/re_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=../documentation/us_tax_form.jpg \
  Global.use_gpu=False \
  -c_ser configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o_ser Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy
```



### Attempt #3
```sh
python3 ./tools/infer_kie_token_ser_re.py \
  -c configs/kie/vi_layoutxlm/re_vi_layoutxlm_xfund_zh.yml \
  -o Architecture.Backbone.checkpoints=./pretrained_model/re_vi_layoutxlm_xfund_pretrained/best_accuracy \
  Global.infer_img=../documentation/john_sample.jpg \
  Global.use_gpu=False \
  -c_ser configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
  -o_ser Architecture.Backbone.checkpoints=./pretrained_model/ser_vi_layoutxlm_xfund_pretrained/best_accuracy
```


### Question

1. Do you want us to improve the current token method which uses [layoutLM](https://www.microsoft.com/en-us/research/publication/layoutlm-pre-training-of-text-and-layout-for-document-image-understanding/#:~:text=LayoutLM%20is%20a%20simple%20but,SOTA%20results%20on%20multiple%20datasets.)
- grid based
- token based
- GCN based
- e2e based methods


or go a different route?

what was the so called problem mentioned?

should I look at other libraries

or further understand the paddleOCR?

why didn't they show us this before?

didn't they run into version complications?
paddlepaddle installation issues?

maybe a layout LM replacement?
LayoutLM can only be trained on a single language
[LiLT](https://www.youtube.com/watch?v=EVONngnrJbE) is language independent.
[LayoutLM](https://www.youtube.com/watch?v=aGcLSH9TTLU&list=PLcP8bHQl_w-F9RZeczGmJc16XoRHSiGQz&index=5) is the other model that we can use
[impira DocQuery](https://www.youtube.com/watch?v=sDPNWg3RYyY&list=PLcP8bHQl_w-F9RZeczGmJc16XoRHSiGQz&index=7) is the third model, they have a web app which is docQA, this however only seems to have querying

gonna try out LayoutLM separately


found a medium article
[Fine-Tuning Transformer Model for Invoice Recognition](https://towardsdatascience.com/fine-tuning-transformer-model-for-invoice-recognition-1e55869336d4)

transformers -?
[LayoutLMv3 training with CORD (receipts dataset)](https://www.youtube.com/watch?v=bsT_1uDRQVo)

I was thinking lemme run it first for CORD save the results that I'm satisfied with and then test the other things.
also apparently we can setup huggingface to save our pretrained / trained models, which sounds really convenient.


found something based on these keywords
[fined tuned layoutLMv3 on FUNSD](https://www.google.com/search?q=fine+tune+layoutlmv3+on+FUNSD&oq=fine+tune+layoutlmv3+on+FUNSD&aqs=chrome..69i57.10656j0j1&sourceid=chrome&ie=UTF-8)

session crashed because I used up all the ram bro what.

so it doesn't depend on the size of your image, but the fact that all those images are being loaded into the damn ram.

so now trying it with the shuffle dataset

it loaded the GPU Ram with upto 11.3/15.0 GB gigs of them holy shit which means it can handle just a little more

damn I didn't know it needed all this for it work wth.



## 2nd March

After having done LayoutLMv2 and LayoutLMv3 I have way more context into how the the process generally flows, and I'm starting to have a lot of context into how these things are structured.

PPstructure basically claims to have better accuracy than the other methods, considering that I train them really well.

Anyways, on 

[KIE Doc](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/doc/doc_en/kie_en.md#Data-Preparation)

it tells us we can use these datasets XFUND or FUNSD, and use their scripts to match our own needs.

So I'm going to try and do that.


so `kie/tools` is this repo which basically has helper functions that we need for this project.

such a function is `trans_funsd_label.py` which converts the `FUNSD` dataset into what LayouXLM can understand.

had to run the `trans_funsd_label.py` and it said `OK` bruh what typa shit is this.


> successfully prepared the FUNSD dataset I believe.

so basically you specify this `PaddleOCR/configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml` file which says all the things that have to be done

it also makes sense now that I've used LayoutLM

and this is the [PP-StructureV2 Paper](https://arxiv.org/abs/2210.05391) that talks about proposing `VI-LayoutXLM` which is the highlight.


new terminology alert - SDGMR

Spatial Dual-Modality Graph Reasoning for Key Information Information Extraction


finally found this whole document of [environment_en.md](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/doc/doc_en/environment_en.md#1.3)

bruh this talks about how to prepare your environment.
- anaconda
- docker

I need to test out docker implementation of this.


for more KIE models training and configuration files, you can go into configs/kie/ or refer to [Frontier KIE algorithms](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/doc/doc_en/algorithm_overview_en.md).

Supported
- VI-LayoutXLM
- LayoutLM
- LayoutLMv2
- LayouXLM
- SDMGR

they say on XFUND_zh dataset the algorithm is also mentioned as follows

[VI-LayoutXLM algorithm_kie_vi_layoutxlm_en](https://github.com/PaddlePaddle/PaddleOCR/blob/release/2.6/doc/doc_en/algorithm_kie_vi_layoutxlm_en.md)

after training I need to export it to inference model.
I don't know what the right approach is after preparing the dataset for PPstructurev2

there's nothing mentioned actually.

I'm going to simplify this problem.

something I noticed is that there's a slight difference in how this is being done.

The prompt of STGI and the prompt I got from PaddleOCR repo's main page are almost the same. so that's that.

I've been thinking of using Obsidian to understand this thing's document structure once and for all.

> U-DML knowledge distillation algorithm and TB-YX sorting algorithm


I was thinking about this obsidian hack which can help me easily navigate this hard ass structure

```sh
^(.(?!(jpe*g|gif|png|md|sh|txt|yml|pdf|svg)))*$
```

use this on the `fsearch` app and I hit delete and everything works just fine.
this finds all `.*md` files.

this removed everything, but there's still blank folders left behind I would think.

NVM shit failed, 

```sh
find . -empty -type d -delete
```

this removed all directories because shit was empty bruh.

iregex was the answer don't ask me how.


running both those commands the file sizes are now manageable
```
189 directories, 675 files
```



> change the config file (yml)
>

transferred `class_list_xfun.txt` to `train_data/FUNSD`

moved the `train.json` and `test.json` into `training_data` and `testing_data` folders respectively for FUNSD

okay prepared the `ser_vi_layoutxlm_funsd_en_udml.yml` file


it's saving a model here too
`/home/axsae/.paddlenlp/models/vi-layoutxlm-base-uncased/vi-layoutxlm-base-uncased`

bruh

got a long ass exception
`Exception: train_data/FUNSD/training_data/image/0000990274.png does not exist!`

maxxed out ram and swap and just Killed itself
reducing `num_workers` to 2