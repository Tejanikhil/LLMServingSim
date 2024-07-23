# LLMServingSim: A HW/SW Co-Simulation Infrastructure for LLM Inference Serving at Scale

## Build LLMServingSim

### 1. Git Clone
```bash
git clone --recurse-submodules https://github.com/casys-kaist/LLMServingSim.git
cd LLMServingSim
```

### 2. `Conda` install (Optional)
Conda can be downloaded from the following [link](https://repo.anaconda.com/archive/)
```
curl -O https://repo.anaconda.com/archive/Anaconda3-2024.06-1-Linux-x86_64.sh
bash Anaconda3-2024.06-1-Linux-x86_64.sh
```

### 3. Install Dependency (tested in python 3.9, GCC, G++ 7.5.0)

### Using `conda` environment.yml (Recommended)
```bash
conda env create -p ./env -f ./environment.yml
conda activate ./env
```

### Clean `conda` Install 
```bash
conda create -n env_name python=3.9
conda activate env_name
conda install conda-forge::libprotobuf=3.6.1
conda install conda-forge::cmake=3.15
conda install cctbx202208::boost-cpp=1.74.0

pip install -r requirements.txt
```

### 4. Build ASTRA-Sim, Chakra, Polymath

```bash
cd astra-sim
./build/astra_analytical/build.sh
cd extern/graph_frontend/chakra
pip install .
cd ../../../../execution_engine/polymath
pip install .
cd ../..
```

## Run LLMServingSim

### 1. Set Input Configurations

Config & Dataset Path:

- Network config path: `astra-sim/inputs/network/analytical/{config_name}.json`
- NPU config path: `execution_engine/codelets_src/codelets/examples/genesys/configs/{config_name}.json`
- Dataset path: `astra-sim/dataset/{dataset_name}.tsv`

### 2. Run LLMServingSim

Test Run

```bash
python3 -u main.py --model_name 'gpt3-6.7b' --npu_num 1 --npu_group 1 --npu_mem 24 --dataset 'dataset/share-gpt-req100-rate10.tsv'

python3 -u main.py --model_name 'llama-7b' --npu_num 1 --npu_group 1 --npu_mem 24 --dataset 'dataset/share-gpt-req100-rate10.tsv'
```

## Parameters of `main.py`

| Parameters | Supporting Options | Default Value | Notes |
| --- | --- | --- | --- |
| model_name | 'gpt2', 'gpt3-6.7b', 'gpt3-125m', 'gpt3-350m', 'gpt3-760m', 'gpt3-1.3bm', 'gpt3-2.7b', 'gpt3-6.7b', 'gpt3-13b', 'gpt3-30b', 'gpt3-175b', 'opt-125m', 'opt-350m', 'opt-1.3b', 'opt-2.7b', 'opt-2.7b', 'opt-6.7b', 'opt-13b', 'opt-30b', 'opt-66b', 'opt-175b', 'llama-7b', 'llama-30b', 'llama-70b' | 'gpt2' |  |
| npu_num  | Integer | 16 |  |
| max_batch | Integer | 0 | 0: no limit |
| batch_delay  | Integer | 0 |  |
| scheduling | 'none', 'orca' | 'orca' |  |
| parallel | 'pipeline', 'tensor', 'hybrid' | 'hybrid' |  |
| npu_group | Integer | 1 |  |
| npu_mem | Integer | 40 |  |
| kv_manage | 'max', 'pow2', 'oracle', 'vllm' | 'vllm' |  |
| block_size | Integer | 8 |  |
| pim_type | 'none', 'local', 'pool' | 'none' |  |
| sub_batch | Flag | False | Sub-batch Scheduling On/Off |
| dataset | Dataset Path | None | None: manually add requests in main.py |
| network | JSON File Name | None | None: following convention "fully_connected_{network_dim}d_{number_of_NPUs}d.json" |
| output | Output TSV Path | None | None: no tsv output only stdout |
| gen | Flag | False | Skip initiation phase On/Off |
| fast_run | Flag | False | Skip all compilation and force to use cached trace for fast simulation |

## Outputs of `main.py`

In all outputs, the unit of throughput is `tokens/second`, and the unit of simulation time is `milliseconds`.

### 1. Standard Output

The standard output shows which requests are being processed in each iteration of the simulator and displays the measured throughput at regular intervals. 
Additionally, it provides a summary of throughput and simulation time at the end.

### 2. Throughput TSV File

`{output_filename}-throughput.tsv` contains the prompt and generation throughput at each interval.


### 3. Simulation Time TSV File

`{output_filename}-simulation-time.tsv` contains the simulation time of each components.


## Evaluation

### Move to Evaluation Folder

```bash
cd evaluation
```

### Run Each Evaluation Script

```bash
./evaluation1.sh
./evaluation2.sh
...
./evaluation5.sh
```

### Run All Evaluation Script

```bash
./evaluation_all.sh
```

For detailed information about the evaluation, please refer to the `README` file in the `evaluation` folder.
