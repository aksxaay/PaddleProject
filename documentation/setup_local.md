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