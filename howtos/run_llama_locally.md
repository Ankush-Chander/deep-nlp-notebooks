# How to run llama-2 locally
1. Clone the repository [llama-cpp](https://github.com/ggerganov/llama.cpp)
```bash
git clone git@github.com:ggerganov/llama.cpp.git 
```
2. Build the project
```bash
cd llama.cpp
make
```
3. 
a. Download weights from [huggingface Llama-2-7b repo](https://huggingface.co/meta-llama/Llama-2-7b) and place in the `models` folder
```bash
mkdir models
cd models
git clone git@hf.co:meta-llama/Llama-2-7b 
rm -rf Llama-2-7b/.git
```
b. Convert the weights to the hf format using the script [convert_llama_weights_to_hf.py](https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/convert_llama_weights_to_hf.py)
```bash
# run from llama.cpp folder
wget -c https://raw.githubusercontent.com/huggingface/transformers/main/src/transformers/models/llama/convert_llama_weights_to_hf.py
# it"s a RAM intensive command. Try after closing all other applications if it"s killed due to memory error
python3 convert_llama_weights_to_hf.py --input_dir models/Llama-2-7b/ --model_size 7B --output_dir models/7B-hf
```
4. Quantize and run
```bash
# install Python dependencies
python3 -m pip install -r requirements.txt
# convert the 7B model to ggml FP16 format
python3 convert.py models/7B-hf/

# quantize the model to 4-bits (using q4_0 method)
./quantize ./models/7B-hf/ggml-model-f16.gguf ./models/7B-hf/ggml-model-q4_0.gguf q4_0

# update the gguf filetype to current if older version is unsupported by another application
./quantize ./models/7B/ggml-model-q4_0.gguf ./models/7B/ggml-model-q4_0-v2.gguf COPY

# run the inference
./main -m ./models/7B-hf/ggml-model-q4_0.gguf -n 128

```
5. Interactive mode
```bash
# interactive mode
./main -m ./models/7B-hf/ggml-model-q4_0.gguf -c 512 -b 1024 -n 256 --keep 48     --repeat_penalty 1.0 --color -i     -r "User:" -f prompts/chat-with-bob.txt
```

References:
- [Github: llama.cpp](https://github.com/ggerganov/llama.cpp)
- [HuggingFace: llama-2 repo](https://huggingface.co/meta-llama/Llama-2-7b)
- [Stack-overflow: Use LLama 2 7B with python](https://stackoverflow.com/a/76967510/2893777)
- [Github: llama-cpp-python](https://github.com/abetlen/llama-cpp-python)

