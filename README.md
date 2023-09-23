### Client
https://huggingface.co/spaces/yuekai/triton-asr-client

https://github.com/yuekaizhang/Triton-ASR-Client
### Server
```sh
docker pull soar97/triton-whisper:23.06

docker run -it --name "whisper-server" --gpus all --net host -v $your_mount_dir --shm-size=2g soar97/triton-whisper:23.06

apt-get install git-lfs
git-lfs install
git clone https://huggingface.co/yuekai/model_repo_whisper_large_v2.git

export CUDA_VISIBLE_DEVICES="1"

model_repo_path=./model_repo_whisper

tritonserver --model-repository $model_repo_path \
            --pinned-memory-pool-byte-size=2048000000 \
            --cuda-memory-pool-byte-size=0:4096000000 \
            --http-port 10086 \
            --metrics-port 10087
```

### Benchmark Results
Decoding on a single V100 GPU, audios are padding to 30s, using aishell1 test set files

| Model | Backend   | Concurrency | RTF     |
|-------|-----------|-----------------------|---------|
| Large-v2 | ONNX FP16 | 4                   | 0.14 |

|Module| Time Distribution|
|--|--|
|feature_extractor|0.8%|
|encoder|9.6%|
|decoder|67.4%|
|greedy search|22.2%|