# 生殖健康管理通用数据交换格式 (第一版)

## 序言

本格式旨在发展一种通用的数据交换格式，用于生殖健康管理及数据分析。

本格式主要为男性的生殖健康管理需求设计。

对于需要元数据的情形，本格式应标记为 RpHMGDEF1，属于 CSV 的细分类型。

本格式主要基于如下数据交换格式设计：

- CSV
- ISO 8601
- JSON

本文以 [GFDL 1.3](https://www.gnu.org/licenses/fdl-1.3.html) 许可证发布。

Permission is granted to copy, distribute and/or modify this document under the terms of the GNU Free Documentation License, Version 1.3 or any later version published by the Free Software Foundation; with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts. A copy of the license is included in the section entitled "GNU Free Documentation License".

## 目录

- 1\. 基本思想
- 2\. 通用表示格式
- 3\. 基本数据字段
  - 3.1. 字段定义
  - 3.2. 字段值
    - 3.2.1 事件类型（Class）
    - 3.2.2 时区（Timezone）
- 4\. 扩展数据字段
  - 4.1. 格式要求
  - 4.2. 字段定义
  - 4.3. 字段值
- 5\. 示例数据
- 6\. 实用工具
- 7\. 致谢

## 1. 基本思想

本格式的数据的基本单位是记录（Record），即记载每一次事件发生的时间和其他信息。

考虑如下样例：

```
2020-01-28T17:32:10.880Z;A
```

这行文本可以视为 CSV 文件的其中一行，包含 2 个字段，用半角分号区隔。

第 1 个字段是事件的时间，以 ISO 8601 表示一个 UTC 时间戳，不考虑事件发生的时区。

第 2 个字段是事件的类型，A 代表自慰射精，B 代表飞机杯，C 代表标准的性生活。本字段具有可扩展性，可以定义新的值。

## 2. 通用表示格式

根据 CSV 的实践，对于空缺字段，其值表示为空字符串。

例如，对于有 3 个字段的 CSV 数据，如果其中第 2 个字段没有数据，那么应该会看到两个连续的分号。

如果某一行结尾有连续多个分号，那么这些分号可以省略。

当数据本身存在分号时，应使用 encodeURIComponent 将其转化为 "%3B"。

## 3. 基本数据字段

### 3.1 字段定义

字段序号 | 标准名称 | 定义
--- | --------- | ---
0   | Time      | ISO 8601 时间戳，必须以 Z 结尾（例："2020-01-28T17:32:10.880Z"）
1   | Class     | 事件类型（参考章节 3.2.1）
2   | Timezone  | 时区（参考章节 3.2.2）
3   | Blur      | 时间精确性；用 "accurate" 表示精确，用 "blur" 表示非精确；非精确时间表示只有日期是可靠的
4   | N/A       | 预留字段，尚未定义

### 3.2 字段值

#### 3.2.1 事件类型（Class）

事件类型一般由单个字母表示。可以通过追加额外的数字来表示某种具体细分场景。

需要可扩展性时，可以使用多个大写字母（从 A 到 Z 表示 0 到 23，跳过 I、O，使用 24 进制）表示大类，多个数字（从 0 到 9，使用 10 进制）表示小类。

可选值 | 定义
--- | ---
A   | 自慰射精
A1  | 传统手动自慰射精
A2  | 借助 dildo 玩具的自慰射精
A3  | 借助电刺激玩具的自慰射精
B   | 飞机杯
C   | 标准性生活射精
C1  | 标准性生活射精（标准）
C2  | 标准性生活射精（口射）
C3  | 标准性生活射精（撸射）
C4  | 标准性生活射精（顶射）
C5  | 标准性生活射精（手动前列腺按摩）
C6  | 标准性生活射精（双头龙）
D   | 保留值，表示未具体指定的其他类型（可能出于兼容性或隐私考量）
E   | 参与 C 类型的性生活但没有射精（例：作为受，或仅为他人口交）
F   | 在 BDSM 活动中的射精
F1  | 在 BDSM 活动中的射精（有射精意愿的）
F2  | 在 BDSM 活动中的射精（无射精意愿的）
G   | 遗精
G1  | 梦遗
G2  | 清醒状态下的遗精
H   | 多人娱乐活动
H1  | 多人娱乐活动中的一次射精
H2  | 参与了一次多人娱乐活动，在过程未射精
I   | 在一些字体中与 "1" 太过相似，禁止使用
K   | 意外射精（例：直肠指检）

#### 3.2.2 时区（Timezone）

时区的表示由 5 个字符构成，分为 3 部分：

- 正负标记（加号表示东，减号表示西）
- 小时数（2 位数字，例如 08 表示基于 UTC 偏差 8 小时）
- 分钟数（2 位数字，例如 30 表示 30 分钟）

例如，"+0830"（平壤时间，已弃用）可以表示 UTC+08:30。

如果时区为 UTC，那么整体应写作 "00000"。

## 4. 扩展数据字段

### 4.1. 格式要求

所有的扩展数据应是 JSON 格式。

字段的排序，以 Unicode 字典序为基准，序号低的靠前。省去所有不必要的空格。内部不允许使用分号；当 JSON 里的字符串内容内容本身存在分号时，应使用 encodeURIComponent 将其转化为 "%3B"。

将此 JSON 字符串视为 CSV 中的纯文本数据，当作第 6 个字段，序号为 5。

注意，扩展数据字段的存在会导致所有的分号不能省略。

例如：

```
2020-01-28T17:32:10.880Z;A;+0800;blur;;{"_Foo":"Hello","Bar":"World"}
```

### 4.2. 字段定义

待补充。

### 4.2. 字段值

待补充。

## 5. 示例数据

```
2020-01-28T17:32:10.880Z;A;+0800;blur;
```

```
2020-01-28T17:32:10.880Z;A;+0800;;{"_Foo":"Hello","Bar":"World"}
```

## 6. 实用工具

- [neruthes.xyz/RpHMGDEF1](https://neruthes.xyz/RpHMGDEF1/) 单条记录生成工具（by Neruthes）
- [neruthes.xyz/RpHMGDEF1/analytics](https://neruthes.xyz/RpHMGDEF1/analytics/) 基本数据分析工具（by Neruthes）

## 7. 致谢

- **Dong, Shida** (Mr.), 董先生的生殖健康管理记录启发了作者来创造本格式。
