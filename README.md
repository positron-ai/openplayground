# Positron notes
One thing to note is to remember to run this specifying the --models

##in one terminal:
```sh
cd app && npm install && npx parcel watch src/index.html --no-cache
```

##in the second terminal

```sh
pushd server && pip3 install -r requirements.txt && popd
```
then:
```sh
python3 -m server.app --host=0.0.0.0 --models=server/models.json
```

## in Mike's venv overall  (python version 3.11)
```sh
$ python --version
Python 3.11.4

$ pip list
Package                  Version
------------------------ ----------
aiodns                   3.0.0
aiohttp                  3.8.4
aiohttp-retry            2.8.3
aiosignal                1.3.1
aleph-alpha-client       2.16.1
anthropic                0.2.3
anyio                    3.7.0
async-timeout            4.0.2
attrs                    23.1.0
blinker                  1.6.2
cachetools               5.3.0
certifi                  2023.5.7
cffi                     1.15.1
charset-normalizer       3.1.0
click                    8.1.3
cmake                    3.26.4
filelock                 3.12.2
Flask                    2.2.3
Flask-Cors               3.0.10
frozenlist               1.3.3
h11                      0.14.0
httpcore                 0.17.2
httpx                    0.24.1
huggingface-hub          0.13.2
idna                     3.4
itsdangerous             2.1.2
Jinja2                   3.1.2
lit                      16.0.6
MarkupSafe               2.1.3
mpmath                   1.3.0
multidict                6.0.4
networkx                 3.1
numpy                    1.25.0
nvidia-cublas-cu11       11.10.3.66
nvidia-cuda-cupti-cu11   11.7.101
nvidia-cuda-nvrtc-cu11   11.7.99
nvidia-cuda-runtime-cu11 11.7.99
nvidia-cudnn-cu11        8.5.0.96
nvidia-cufft-cu11        10.9.0.58
nvidia-curand-cu11       10.2.10.91
nvidia-cusolver-cu11     11.4.0.1
nvidia-cusparse-cu11     11.7.4.91
nvidia-nccl-cu11         2.14.3
nvidia-nvtx-cu11         11.7.91
openai                   0.27.2
openplayground           0.1.5
packaging                23.1
Pillow                   9.5.0
pip                      23.1.2
psutil                   5.9.4
pycares                  4.3.0
pycparser                2.21
python-dotenv            1.0.0
PyYAML                   6.0
regex                    2023.6.3
requests                 2.28.2
sentencepiece            0.1.97
setuptools               65.5.0
six                      1.16.0
sniffio                  1.3.0
sseclient                0.0.27
sympy                    1.12
tokenizers               0.13.3
torch                    2.0.0
tqdm                     4.65.0
transformers             4.27.1
triton                   2.0.0
typing_extensions        4.6.3
urllib3                  1.26.16
Werkzeug                 2.3.6
wheel                    0.40.0
yarl                     1.9.2
```

# Original README
# openplayground

An LLM playground you can run on your laptop.

https://user-images.githubusercontent.com/111631/227399583-39b23f48-9823-4571-a906-985dbe282b20.mp4

#### Features

- Use any model from [OpenAI](https://openai.com), [Anthropic](https://anthropic.com), [Cohere](https://cohere.com), [Forefront](https://forefront.ai), [HuggingFace](https://huggingface.co), [Aleph Alpha](https://aleph-alpha.com), [Replicate](https://replicate.com), [Banana](https://banana.dev) and [llama.cpp](https://github.com/ggerganov/llama.cpp).
- Full playground UI, including history, parameter tuning, keyboard shortcuts, and logprops.
- Compare models side-by-side with the same prompt, individually tune model parameters, and retry with different parameters.
- Automatically detects local models in your HuggingFace cache, and lets you install new ones.
- Works OK on your phone.
- Probably won't kill everyone.

## Try on nat.dev

Try the hosted version: [nat.dev](https://nat.dev).

## How to install and run

```sh
pip install openplayground
openplayground run
```

Alternatively, run it as a docker container:
```sh
docker run --name openplayground -p 5432:5432 -d --volume openplayground:/web/config natorg/openplayground
```

This runs a Flask process, so you can add the typical flags such as setting a different port `openplayground run -p 1235` and others.

## How to run for development

```sh
git clone https://github.com/nat/openplayground
cd app && npm install && npx parcel watch src/index.html --no-cache
cd server && pip3 install -r requirements.txt && cd .. && python3 -m server.app
```

## Docker

```sh
docker build . --tag "openplayground"
docker run --name openplayground -p 5432:5432 -d --volume openplayground:/web/config openplayground
```

First volume is optional. It's used to store API keys, models settings.

## Ideas for contributions

- Add a token counter to the playground
- Add a cost counter to the playground and the compare page
- Measure and display time to first token
- Setup automatic builds with GitHub Actions
- The default parameters for each model are configured in the `server/models.json` file. If you find better default parameters for a model, please submit a pull request!
- Someone can help us make a homebrew package, and a dockerfile
- Easier way to install open source models directly from openplayground, with `openplayground install <model>` or in the UI.
- Find and fix bugs
- ChatGPT UI, with turn-by-turn, markdown rendering, chatgpt plugin support, etc.
- We will probably need multimodal inputs and outputs at some point in 2023

### llama.cpp

## Adding models to openplayground

Models and providers have three types in openplayground:

- Searchable
- Local inference
- API

You can add models in `server/models.json` with the following schema:

#### Local inference

For models running locally on your device you can add them to openplayground like the following (a minimal example):

```json
"llama": {
    "api_key" : false,
    "models" : {
        "llama-70b": {
            "parameters": {
                "temperature": {
                    "value": 0.5,
                    "range": [
                        0.1,
                        1.0
                    ]
                },
            }
        }
    }
}
```

Keep in mind you will need to add a generation method for your model in `server/app.py`. Take a look at `local_text_generation()` as an example.

#### API Provider Inference

This is for model providers like OpenAI, cohere, forefront, and more. You can connect them easily into openplayground (a minimal example):

```json
"cohere": {
    "api_key" : true,
    "models" : {
        "xlarge": {
            "parameters": {
                "temperature": {
                    "value": 0.5,
                    "range": [
                        0.1,
                        1.0
                    ]
                },
            }
        }
    }
}
```

Keep in mind you will need to add a generation method for your model in `server/app.py`. Take a look at `openai_text_generation()` or `cohere_text_generation()` as an example.

#### Searchable models

We use this for Huggingface Remote Inference models, the search endpoint is useful for scaling to N models in the settings page.

```json
"provider_name": {
    "api_key": true,
    "search": {
        "endpoint": "ENDPOINT_URL"
    },
    "parameters": {
        "parameter": {
            "value": 1.0,
            "range": [
                0.1,
                1.0
            ]
        },
    }
}
```

#### Credits

Instigated by Nat Friedman. Initial implementation by [Zain Huda](https://github.com/zainhuda) as a repl.it bounty. Many features and extensive refactoring by [Alex Lourenco](https://github.com/AlexanderLourenco).
