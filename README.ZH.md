# OGC 2026 Baseline 使用说明

本目录是 OGC 2026 Optimization Challenge 的 starter kit，包含算法模板、图形化测试工具和 Conda 环境配置。

## 目录说明

- `baseline/`：算法开发目录，主要修改 `myalgorithm.py`。
- `baseline/baseline_greedy.py`：官方提供的参考贪心算法，可以作为起点或被 `myalgorithm.py` 调用。
- `baseline/utils.py`：可行性检查和评分工具，通常不要修改。
- `alg_tester/`：图形化算法测试器，用来加载问题实例、运行算法并查看可行性和目标值。
- `ogc2026_env.yml`：官方 Conda 环境配置文件。

`baseline/` 和 `alg_tester/` 不是两个完全独立的项目。`baseline/` 负责放算法代码，`alg_tester/` 负责启动 GUI 并调用算法目录中的 `myalgorithm.py`。

## 算法开发位置

默认在 `baseline/myalgorithm.py` 中开发算法，并保持函数签名不变：

```python
def algorithm(prob_info: dict, timelimit: float) -> dict:
    ...
    return solution
```

也可以复制一份 `baseline/` 作为自己的算法目录，例如 `my_alg/`。只要目录里包含 `myalgorithm.py` 且函数签名不变，测试器就可以运行。

## 推荐环境安装

建议使用 Miniforge 或 Miniconda。由于默认源在国内可能较慢，推荐先配置 Conda 和 pip 镜像。

### 1. 配置 Conda 镜像

```powershell
conda config --set show_channel_urls yes
conda config --remove-key channels
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
conda config --set channel_priority strict
```

如果 `conda config --remove-key channels` 提示没有该配置，可以忽略。

### 2. 配置 pip 镜像

环境文件中有不少 pip 依赖，例如 `torch`、`tensorflow`、`ortools`、`gurobipy` 等，建议也配置 pip 镜像：

```powershell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 3. 创建环境

在项目根目录执行：

```powershell
conda env create -f ogc2026_env.yml
```

创建完成后激活环境：

```powershell
conda activate ogc2026
```

## 更快的创建方式：使用 mamba

如果 `conda env create` 卡在 `Collecting package metadata` 或解析依赖很慢，推荐先安装 `mamba`：

```powershell
conda install -n base mamba -c conda-forge
```

然后用 `mamba` 创建环境：

```powershell
mamba env create -f ogc2026_env.yml
```

`mamba` 和 `conda` 使用同一个环境文件，但依赖解析通常更快。

## 运行 GUI 测试器

GUI 从 `alg_tester/` 目录启动，算法目录单独选择 `baseline/`。

```powershell
conda activate ogc2026
cd alg_tester
python alg_tester_app.py
```

打开 GUI 后：

1. `Instance` 选择问题实例 JSON，例如 `alg_tester/example/example_B2_b10.json`。
2. `Algorithm` 选择算法目录，例如当前项目下的 `baseline/`。
3. 设置时间限制。
4. 点击 `Run` 查看可行性检查结果和目标值。

## Torch 是否是 CPU 版本

`ogc2026_env.yml` 中写的是：

```yaml
- torch==2.11.0
- torchvision==0.26.0
```

它没有显式指定 CPU 版或 CUDA 版。实际安装结果取决于 pip 源、平台和 PyTorch wheel 的发布情况。安装后可以用下面命令检查当前环境是否能使用 CUDA：

```powershell
python -c "import torch; print(torch.__version__); print(torch.cuda.is_available())"
```

输出 `False` 表示当前 torch 环境不能使用 CUDA。
