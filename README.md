# podman-ramalama-api-remoting-test-results

Ref:
- https://developers.redhat.com/articles/2025/09/18/reach-native-speed-macos-llamacpp-container-inference#



### Run API remoting by Podman desktop

- Download this model:
`https://cas-bridge.xethub.hf.co/xet-bridge-us/6893a98c68b00fe99f3c65d7/d779b1a2a8ce68a82b9d7453116f4a53b344479790650a98d4769b22d28c9609?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=cas%2F20251209%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20251209T114524Z&X-Amz-Expires=3600&X-Amz-Signature=04e0d6128da4d5c60e045291b615c50d1ee6662534d443ed8b98692360e2a23b&X-Amz-SignedHeaders=host&X-Xet-Cas-Uid=public&response-content-disposition=attachment%3B+filename*%3DUTF-8%27%27Qwen3-4B-Thinking-2507-Q4_K_M.gguf%3B+filename%3D%22Qwen3-4B-Thinking-2507-Q4_K_M.gguf%22%3B&x-id=GetObject&Expires=1765284324&Policy=eyJTdGF0ZW1lbnQiOlt7IkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTc2NTI4NDMyNH19LCJSZXNvdXJjZSI6Imh0dHBzOi8vY2FzLWJyaWRnZS54ZXRodWIuaGYuY28veGV0LWJyaWRnZS11cy82ODkzYTk4YzY4YjAwZmU5OWYzYzY1ZDcvZDc3OWIxYTJhOGNlNjhhODJiOWQ3NDUzMTE2ZjRhNTNiMzQ0NDc5NzkwNjUwYTk4ZDQ3NjliMjJkMjhjOTYwOSoifV19&Signature=WvCECP9G2d5%7E9VId1dgUReFPA45hZ1O6ouh1MDQvhmgtUXotmn9jBwRwNvw8NdCIzO3XEWpkPWyGwSpRikzG0MpV8GAO4jcg5ik3or4WZmJMBneZ7QDTyxu7usXgVleUjIGpGolpFSYSU74S3UoGGO2MES0oP6zuxU7cKuGtDQ2NC5S5%7ERppbyrGAtL8ClIiGjCrsd96SbyTcCwe%7EfVObnMWXbn6sgenEdR4UdI5grEmucSecjwLHN%7Eg1rBPAh8SS9fsTOJ20SvcY9bVOfJED%7EWg-JWpc0rI1%7Eswd54GRWhKSAp496Nr46f9F-DasrDq12ttw4BBYwZsVj8o4mzIeA__&Key-Pair-Id=K2L8F4GPSG1IFC`


- Follow the steps inside "https://developers.redhat.com/articles/2025/09/18/reach-native-speed-macos-llamacpp-container-inference"; If you are lost, you probably need to refer to these screenshots to locate the buttons in the Podman desktop UI interface:

<img width="1380" height="1043" alt="image" src="https://github.com/user-attachments/assets/f747aa56-ea0f-40e3-a3f4-8987b5a9349a" />

<img width="1380" height="1043" alt="image" src="https://github.com/user-attachments/assets/3e817e97-49f6-4fde-b0d1-e8158624f002" />

<img width="1380" height="1043" alt="image" src="https://github.com/user-attachments/assets/fd2e72b3-58bd-4fc8-bd10-8f10abc53cc1" />


- Install ramalama to test the API

```
curl -fsSL https://ramalama.ai/install.sh | bash
ramalama chat --url http://127.0.0.1:1234
```

### Run API remoting by Podman ramalama scripts

```
curl -L -Ssf https://github.com/crc-org/llama.cpp/releases/download/b6298-remoting-0.1.6_b5/llama_cpp-api_remoting-b6298-remoting-0.1.6_b5.tar | tar xv
cd llama_cpp-api_remoting-b6298-remoting-0.1.6_b5
bash ./update_krunkit.sh
bash ./podman_start_machine.api_remoting.sh
export CONTAINERS_MACHINE_PROVIDER=libkrun
ramalama run --image quay.io/crcont/remoting:v0.12.1-apir.rc1_apir.b6298-remoting-0.1.6_b5    hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M
```

### Benchmarking

```
# API remoting
ramalama bench --image quay.io/crcont/podman-desktop-remoting-ext:v0.1.3_b6298-remoting-0.1.6_b5  hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M

# container / vulcan
ramalama bench hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M

# native
ramalama bench --nocontainer  hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M
```

### Results

```
ramalama bench --image quay.io/crcont/remoting:v0.12.1-apir.rc1_apir.b6298-remoting-0.1.6_b5  hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M

APIR: using DRM device /dev/dri/renderD128
APIR: log_call_duration: waited 87498ns for the API Remoting handshake host reply...
APIR: log_call_duration: waited 2.29ms for the API Remoting LoadLibrary host reply...
APIR: ggml_backend_remoting_frontend_reg: initialzed
| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | RemotingFrontend | 999 |           pp512 |        831.85 ± 9.07 |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | RemotingFrontend | 999 |           tg128 |         61.52 ± 1.68 |

build: fc2c13f (1)




ramalama bench hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M

ggml_vulkan: Found 1 Vulkan devices:
ggml_vulkan: 0 = Virtio-GPU Venus (Apple M1 Max) (venus) | uma: 1 | fp16: 1 | bf16: 0 | warp size: 32 | shared memory: 32768 | int dot: 0 | matrix cores: none
| model                          |       size |     params | backend    | ngl |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | --------------: | -------------------: |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | Vulkan     | 999 |           pp512 |        463.31 ± 0.81 |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | Vulkan     | 999 |           tg128 |         37.38 ± 1.28 |

build: b52edd2 (1)




ramalama --nocontainer bench hf.co/unsloth/Qwen3-4B-Thinking-2507-GGUF:Q4_K_M
ggml_metal_device_init: tensor API disabled for pre-M5 and pre-A19 devices
ggml_metal_library_init: using embedded metal library

ggml_metal_library_init: loaded in 11.945 sec
ggml_metal_rsets_init: creating a residency set collection (keep_alive = 180 s)
ggml_metal_device_init: GPU name:   Apple M1 Max
ggml_metal_device_init: GPU family: MTLGPUFamilyApple7  (1007)
ggml_metal_device_init: GPU family: MTLGPUFamilyCommon3 (3003)
ggml_metal_device_init: GPU family: MTLGPUFamilyMetal3  (5001)
ggml_metal_device_init: simdgroup reduction   = true
ggml_metal_device_init: simdgroup matrix mul. = true
ggml_metal_device_init: has unified memory    = true
ggml_metal_device_init: has bfloat            = true
ggml_metal_device_init: has tensor            = false
ggml_metal_device_init: use residency sets    = true
ggml_metal_device_init: use shared buffers    = true
ggml_metal_device_init: recommendedMaxWorkingSetSize  = 22906.50 MB
| model                          |       size |     params | backend    | threads |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | --------------: | -------------------: |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | Metal,BLAS |       5 |           pp512 |       868.69 ± 18.30 |
| qwen3 4B Q4_K - Medium         |   2.32 GiB |     4.02 B | Metal,BLAS |       5 |           tg128 |         71.17 ± 0.71 |

build: db9783738 (7310)
```



Results (polished):

| Command Variant | Backend | Threads / NGL | Test | Tokens/sec (± stdev) | Notes |
| --- | --- | --- | --- | --- | --- |
| `ramalama bench --image … remoting` | RemotingFrontend | ngl 999 | `pp512` | 831.85 ± 9.07 | Containerized remoting build `fc2c13f` |
| `ramalama bench --image … remoting` | RemotingFrontend | ngl 999 | `tg128` | 61.52 ± 1.68 | Same run as above |
| `ramalama bench hf.co/…` | Vulkan | ngl 999 | `pp512` | 463.31 ± 0.81 | Native Vulkan build `b52edd2` on Virtio-GPU (M1 Max) |
| `ramalama bench hf.co/…` | Vulkan | ngl 999 | `tg128` | 37.38 ± 1.28 | Same run as above |
| `ramalama --nocontainer bench …` | Metal + BLAS | 5 threads | `pp512` | 868.69 ± 18.30 | Native Metal build `db9783738`; tensor API disabled |
| `ramalama --nocontainer bench …` | Metal + BLAS | 5 threads | `tg128` | 71.17 ± 0.71 | Same run as above |




More:

How ramalama runs multimodal LLM?
- https://developers.redhat.com/articles/2025/10/27/multimodal-ai-edge-deploy-vision-language-models-ramalama#vision_language_models_for_the_edge





