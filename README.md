<p align="center">
  <img src="https://raw.githubusercontent.com/huggingface/alignment-handbook/main/assets/handbook.png">
</p>

<p align="center">
    🤗 <a href="https://huggingface.co/collections/alignment-handbook/handbook-v01-models-and-datasets-654e424d22e6880da5ebc015" target="_blank">Models & Datasets</a> | 📃 <a href="https://arxiv.org/abs/2310.16944" target="_blank">Technical Report</a>
</p>

# 对齐手册

稳健的方法，用于继续预训练和将语言模型与人类和 AI 偏好对齐。

## 这是什么？

仅仅一年前，聊天机器人已经过时，大多数人还没有听说过通过人类反馈进行强化学习（RLHF）来将语言模型与人类偏好对齐等技术。然后，OpenAI 推出了 ChatGPT，Meta紧随其后发布了 Llama 系列的语言模型，使得机器学习社区能够构建自己的功能强大的聊天机器人。这导致了一个丰富的数据集和模型生态系统，这些数据集和模型主要集中在通过监督微调（SFT）教导语言模型遵循指令。

然而，我们从 [InstructGPT](https://huggingface.co/papers/2203.02155) 和 [Llama2](https://huggingface.co/papers/2307.09288) 论文中得知，通过增加人类（或 AI）偏好来增强 SFT 可以带来显著的帮助和安全性提升。同时，将语言模型与一组偏好对齐是一个相当新颖的想法，目前还没有太多公开资源可用于指导如何训练这些模型、收集什么样的数据以及衡量最佳下游性能的指标。

对齐手册旨在通过提供一系列稳健的训练方法来填补这一空白。

## 新闻 🗞️
* **2024年4月12日**：我们与 Argilla 和 Kaist AI 合作发布了 Zephyr 141B (A35B)，同时提供了使用 ORPO 对 Mixtral 8x22B 进行微调的方法 🪁
* **2024年3月12日**：我们发布了 StarChat2 15B，并提供了训练能力强大的编码助手的方法 🌟
* **2024年3月1日**：我们发布了 Zephyr 7B Gemma，这是一种将 Gemma 7B 与 RLAIF 对齐的新方法 🔥
* **2024年2月1日**：我们发布了一种使用宪法 AI 对开放 LLM 进行对齐的方法 📜！请参阅[配方](https://github.com/huggingface/alignment-handbook/tree/main/recipes/constitutional-ai)和[博客文章](https://huggingface.co/blog/constitutional_ai)了解详细信息。
* **2024年1月18日**：我们发布了 DPO vs KTO vs IPO 的一系列评估，详见[配方](recipes/pref_align_scan/README.md)和[博客文章](https://huggingface.co/blog/pref-tuning)。
* **2023年11月10日**：我们发布了复制 Zephyr-7b-β 的所有训练代码 🪁！我们还发布了[No Robots](https://huggingface.co/datasets/HuggingFaceH4/no_robots)，这是一个全新的数据集，由熟练的人类标注者完全编写的 10,000 条指令和演示。

## 链接 🔗

* [Zephyr 7B 模型、数据集和演示](https://huggingface.co/collections/HuggingFaceH4/zephyr-7b-6538c6d6d5ddd1cbb1744a66)

## 如何浏览此项目 🧭

本项目设计简单，主要包括以下内容：

* [`scripts`](./scripts/)：用于训练和评估模型的脚本。包括四个步骤：继续预训练、聊天的监督微调（SFT）、使用 DPO 进行偏好对齐，以及使用 ORPO 进行带偏好对齐的监督微调。每个脚本都支持使用 DeepSpeed ZeRO-3 进行完整模型权重的分布式训练，或者使用 LoRA/QLoRA 进行参数高效的微调。
* [`recipes`](./recipes/)：用于复现 Zephyr 7B 等模型的配方配置文件。每个配方采用 YAML 文件的形式，其中包含与单个训练运行相关的所有参数。还提供了一个 `gpt2-nl` 配方，用于说明如何使用本手册进行语言或领域适应，例如通过在不同语言上继续预训练，然后对结果进行 SFT 和 DPO 调优。

我们还在编写一系列指南，以解释直接偏好优化（DPO）等方法的工作原理，以及从实践中收集人类偏好的经验教训。要开始，请参阅以下内容：

1. 按照[安装说明](#installation-instructions)设置环境等。
2. 通过按照[配方说明](./recipes/zephyr-7b-beta/README.md)复制 Zephyr-7b-β。

如果您想在自己的数据集上训练聊天模型，我们建议按照[此处](./scripts/README.md#fine-tuning-on-your-datasets)的数据集格式化说明进行操作。

## 内容

手册的初始版本将重点介绍以下技术：

* **继续预训练**：将语言模型适应到新的语言或领域，或者通过在新数据集上继续预训练（因果语言建模）来改进它。
* **监督微调**：教导语言模型遵循指令的方法，以及如何收集和整理自己的训练数据集的技巧。
* **奖励建模**：教导语言模型根据人类或 AI 偏好区分模型响应的方法。
* **拒绝抽样**：一种简单但强大的技术，可以提高 SFT 模型的性能。
* **直接偏好优化（DPO）**：一种强大且有前景的 PPO 替代方法。
* **赔率比偏好优化（ORPO）**：一种将人类偏好与 SFT 和 DPO 结合在单个阶段中微调语言模型的技术。

## 安装说明

要运行此项目中的代码，首先使用 Conda 创建一个 Python 虚拟环境：

```shell
conda create -n handbook python=3.10 && conda activate handbook
```

然后，安装 PyTorch `v2.1.2` - 精确的版本对于可重现性很重要！由于这取决于硬件，我们将您引导到[PyTorch安装页面](https://pytorch.org/get-started/locally/)。

接下来，可以按照以下方式安装其余的软件包依赖项：

```shell
git clone https://github.com/huggingface/alignment-handbook.git
cd ./alignment-handbook/
python -m pip install .
```

您还需要安装 Flash Attention 2，可以通过运行以下命令完成：

```shell
python -m pip install flash-attn --no-build-isolation
```

> **注意**
> 如果您的计算机内存少于 96GB，并且具有许多 CPU 核心，请减少 `MAX_JOBS` 参数，例如 `MAX_JOBS=4 pip install flash-attn --no-build-isolation`

然后，通过以下命令登录您的 Hugging Face 账户：

```shell
huggingface-cli login
```

最后，安装 Git LFS，以便您可以将模型推送到 Hugging Face Hub：

```shell
sudo apt-get install git-lfs
```

现在，您可以查看 `scripts` 和 `recipes` 目录，了解如何训练一些模型 🪁！

## 项目结构

```
├── LICENSE
├── Makefile                    <- 包含诸如 `make style` 等命令的 Makefile
├── README.md                   <- 开发人员使用的顶级 README
├── chapters                    <- 在 hf.co/learn 上呈现的教育内容
├── recipes                     <- 配方配置文件、加速配置、slurm 脚本
├── scripts                     <- 用于训练和评估聊天模型的脚本
├── setup.cfg                   <- 安装配置（主要用于配置代码质量和测试）
├── setup.py                    <- 使项目可通过 pip 安装（pip install -e .），以便可以导入 `alignment`
├── src                         <- 用于此项目的源代码
└── tests                       <- 单元测试
```

## 引用

如果您在工作中发现这个仓库的内容对您有用，请按如下方式进行引用：

```bibtex
@misc{alignment_handbook2023,
  author = {Lewis Tunstall and Edward Beeching and Nathan Lambert and Nazneen Rajani and Shengyi Huang and Kashif Rasul and Alexander M. Rush and Thomas Wolf},
  title = {The Alignment Handbook},
  year = {2023},
  publisher = {GitHub},
  journal = {GitHub repository},
  howpublished = {\url{https://github.com/huggingface/alignment-handbook}}
}
```
