**SheetCopilot**: Bringing Software Productivity to the Next Level through Large Language Models
========

[![arXiv](https://img.shields.io/badge/arXiv-2305.19308-b31b1b.svg)](http://arxiv.org/abs/2305.19308) 
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://GitHub.com/Naereen/StrapDown.js/graphs/commit-activity) 
[![Awesome](https://awesome.re/badge.svg)]()

<p align="center">
<img src="assets/icon.png" width="50%">
<br>
<b>SheetCopilot Icon</b>
</p>

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#setup">Setup</a> •
  <a href="#dataset">Dataset</a> •
  <a href="#sheetcopilot-usage">Sheetcopilot Usage</a> •
  <a href="#evaluation">Evaluation</a> •
  <a href="https://neurips.cc/media/PosterPDFs/NeurIPS%202023/70193.png?t=1698641001.038527">Poster</a> •
  <a href="http://arxiv.org/abs/2305.19308">Paper</a> •
  <a href="#citation">Citation</a>

</p>

<p align="center">
<br />
<a href="https://sheetcopilot-demo.github.io/"><strong>Explore the project website »</strong></a>
<br />
</p>

We release the SheetCopilot  agent as well as the evaluation environment in this repository.

SheetCopilot is an assistant agent that manipulates spreadsheets by following user commands. It breaks new ground in human-computer interaction, opening up possibilities for enabling non-expert users to complete their mundane work on complex software (e.g. Google Sheets and Excel) via a language interface.

## What's New
- **[2024/02/24]** 🛠 Full SheetCopilot was released.
- **[2023/12/26]** 🛠 SheetCopilot equipped with Chain-of-Thoughts and external document retrieval was released.
- **[2023/11/15]** ✨ **SheetCopilot for Google Sheets was released!** You can now use SheetCopilot directly on Google Sheets. Check out our Google Sheets plugin store [page](https://workspace.google.com/u/0/marketplace/app/sheetcopilot/393386705978) and watch this [tutorial](https://sheetcopilot.github.io/support.html) for installation and usage guide.

- **[2023/10/27]** 🛠 **More ground truths!** We added more reference solutions to our benchmark (```dataset/task_sheet_answers_v2```) to obtain more accurate evaluation results.

- **[2023/10/25]** SheetCopilot benchmark was open-sourced.

- **[2023/9/22]** 🎉 Our [**paper**](https://openreview.net/forum?id=tfyr2zRVoK) was accepted to NeurIPS 2023.

- **[2023/5/19]** 👷🏻‍♂️ SheetCopilot was completed.

## TODO
- Update the function call parsing code to fix the quote parsing errors
- Update API implementations
- Update the evaluation script to improve the checking accuracy

# Overview

SheetCopilot employs a novel way of directing Large Language Models (LLMs) to manipulate spreadsheets like a human expert. To achieve elegant closed-loop control, SheetCopilot observes the spreadsheet state and polishes generated solutions according to external action documents and error feedback, thereby improving its success rate and efficiency.

<p align="center">
<img src="assets/SheetCopilot-teaser.png" width="100%">
</p>
<br>

**💁‍♂️💁💁‍♀️ Join Us on [Discord](https://discord.gg/XawesbJV)!**

# Setup
### 1. Prepare the Conda environment

SheetCopilot is only available on **Windows**. Python 3.10 is required to support the asynchronous implementation of SheetCopilot.

```
conda create -n sheetcopilot python=3.10
```

### 2. In this conda env, run:

```
pip install -r requirements.txt
```


# Dataset
We released a spreadsheet task dataset containing 28 workbooks and 221 tasks applied to these workbooks. Each task is given one or more hand-made solutions.

Here is the overview of the dataset:

<p align="center">
<img src="assets/two_clouds.png" width="85%">
</p>
<br/>

Our dataset contains diverse task categories and involves a wide range of operations:

<p align="center">
<img src="assets/CatePropAndVerbNoun.png" width="85%">
</p>
<br/>

Our dataset provides tasks with diverse complexity:

<p align="center">
<img src="assets/Instruc&ActDistributions.png" width="85%">
</p>
<br/>

44 operations are supported and more will be added:

- **Entry & manipulation**: Write, CopyPaste, CutPaste, SetHyperlink, RemoveHyperlink, AutoFill, InsertRow, InsertColumn, Delete, Clear
- **Management**: Sort, Filter, DeleteFilter, MoveRow, MoveColumn, RemoveDuplicate
- **Formatting**: SetFormat, DeleteFormat, SetDataType, SetCellMerge, AutoFit, ResizeRowColumn, SetConditionalFormat, SetDataValidation, SetCellLock, FreezePanes, UnfreezePanes
- **Chart**: CreateChart, SetChartTrendline, SetChartTitle, SetChartHasAxis, SetChartAxis, SetChartHasLegend, SetChartLegend, SetChartType, AddChartErrorBars, RemoveChartErrorBars, AddDataLabels, RemoveDataLabels, SetChartMarker
- **Pivot Table**: CreatePivotTable, CreateChartFromPivotTable, CreateSheet, RemoveSheet

This dataset can be used to evaluate any spreadsheet agent including RL, LLM-based, or rule-based methods.

In the ```dataset``` folder, ```dataset.xlsx``` lists the 221 tasks, containing the target workbook name, task number, instruction, task categories, and involved atomic actions.

The fields are explained one by one as follows:

- ```Sheet Name```: The name of the sheet this task is applied to.
- ```No.```: The number of this task.
- ```Context```: The brief description of the sheet this task is applied to. This context will be added to the prompt to inform the LLM of the spreadsheet usage.
- ```Instructions```: The task content.
- ```Categories```: Each task is classified into multiple categories according to the atomic actions involved in the task.
- ```Atomic actions```: The atomic actions used to solve the task
- ```Seed task```: The number of the seed task (stored in ```dataset/seed_tasks.xlsx```) this task originates from. Our 221 tasks were produced by adapting the 67 seed tasks to apply them to the task sheets (the ```task_sheets``` folder).

The ```task_sheets``` folder contains the 28 evaluation workbooks these tasks are applied to.

The ```task_sheet_answers``` folder contains the reference solutions of the tasks. Each solution consists of a reference workbook showing the expected outcome of the corresponding task and a *.yaml file listing the necessary sheet states to compare. If the necessary states of the result match those of one of the references, the result is seen as correct. (The v1 version is used in our paper while the v2 version contains more reference solutions collected after our paper was submitted)

Each solution folder (e.g. ```1_BoomerangSales```) contains at least 1 reference, which comprises a final spreadsheet (1_BoomerangSales_gt1.xlsx) and a checking list (1_BoomerangSales_gt1_check.yaml). Different tasks need different atomic actions so the checking lists are tailored to corresponding tasks.

The ```dataset_20Samples.xlsx``` file lists the 20 selected tasks used to compare the representative LLMs in our experiments (Table 1).

To dive deeper into the dataset collection details, refer to this [tutorial](/dataset/collecting_scripts/).

# SheetCopilot Usage

## For Excel
This repo releases a simplified version of the SheetCopilot agent, whose state machine can do CoT reasoning and retrieve external documents.

SheetCopilot calls customized atomic actions to execute its generated solutions. We implement each atomic action using the ```pywin32``` library. Please refer to [API definitions](/agent/Agent/xwAPI.py) to see the details. To compare with our SheetCopilot, your own agents should also adopt this action space.
 
Before running an experiment, please set max tokens, temperature, model_name, and API keys in ```config/config.yaml```.

You can see two ChatGPT configs in this file - ChatGPT_1 is used to do planning while ChatGPT_2 is used to revise the format of the planning results. You can set ```use_same_LLM: true``` to use ChatGPT_1 to carry out both two jobs.

The underlying implementation of SheetCopilot is a state machine that implements planning by transitioning among 4 states (See the below figure). ```max_cycle_times``` is used to limit the number of times the agent visits the states.

<p align="center">
<img src="assets/StateMachine.jpg" width="85%">
<br>
<b>SheetCopilot State Machine</b>
</p>

<br/>

## Interactive mode

Open an Excel workbook before running this command:

```
python interaction.py -c config/config.yaml
```

Now you can enter instructions and wait for SheetCoilot to finish them without human intervention.

### Example
To try SheetCopilot quickly, please open ```dataset/task_sheets/BoomerangSales.xlsx``` and then enter these instructions in order:

1. Calculate the revenue for each transaction considering the corresponding retail price and discount.

2. Highlight the Revenue cells greater than 500 in blue text.

3. Create a pivot table in a new sheet to show the counts of the websites on which boomerangs were sold.

4. Plot a bar chart for the pivot table in the same sheet.

5. Set the y-axis title as "Count" and turn off legends.

6. Create another pivot table in a new sheet to show the revenue sums of each product.

7. Plot a pie chart for the pivot table with the chart title "Revenue by Product" in this sheet.

You can also try more vague instructions like: ```Analyze the data and plot charts for the results.```

Afterward, you may see SheetCopilot create pivot tables and plot proper charts for you (see the figure below).

<p align="center">
<img src="assets/example_result.png" width="85%">
<br>
<b>Result of the example task</b>
</p>

[Caution] Any operation executed by SheetCopilot cannot be undone by clicking the "Undo" button! We **strongly** recommend that our users use SheetCopilot on GoogleSheets to automate their spreadsheet tasks.

## For Google Sheets

Open a GoogleSheets spreadsheet and install SheetCopilot on the Google Workspace Market like this:

<p align="center">
<img src="assets/install_on_google_sheets.png" width="75%">
<br>
<b>Install SheetCopilot for GoogleSheets</b>
</p>

Then you can hack SheetCopilot happily via chatting ...

<p align="center">
<img src="assets/GoogleSheets_demo.png" width="75%">
<br>
<b>Let SheetCopilot solve complex tasks for you</b>
</p>

You can undo any operations executed by SheetCopilot by just using ```Ctrl + Z```.

# Evaluation

The results generated by any method should be organized like this:

```
results
  └── ([Order]_[Sheet Name])
  └── 1_BoomerangSales
  |   └── ([Order]_[Sheet Name]_[Repeat_No.].xlsx)
  |   └── 1_BoomerangSales_log.yaml
  ...
  └── 9_BoomerangSales
  └── 10_DemographicProfile
  ...
  └── 17_Dragging
  ...
  └── 24_Dragging
  ...
  └── 221_XYScatterPlot
```

[Order] is the row index of the task minus 1 and [Sheet Name] is the items of column A in ```dataset.xlsx```. [Repeat_NO.] is used to differentiate multiple repeats of the same task. If you run each task only once (controlled by ```repeat``` in the config file), [Repeat_NO.] is 1.

```1_BoomerangSales_log.yaml``` is the running log of the task saving the content of the planning process. Likewise, your method should also record a log for each task.

You can also use the "[No.]_[Sheet Name]" naming convention as follows ([No.] are the items of column B in ```dataset.xlsx```):
```
results
  └── ([No.]_[Sheet Name])
  └── 1_BoomerangSales
  |   └── ([No.]_[Sheet Name]_[Repeat_No.].xlsx)
  |   └── 1_BoomerangSales_log.yaml
  ...
  └── 9_BoomerangSales
  ...
  └── 1_Dragging
  ...
  └── 8_Dragging
  ...
```
You should set the global variable ```USE_NO_AND_SHEETNAME``` in ```evaluation.py``` as True to use such a naming convention.

As different agents may present plans in various formats, we recommend that each method outputs each step using this Chain-of-Thoughts (CoT) format:
```
Step X. [Thought]
Action API: @[Action call]@
```

For example,
```
Step 3. Fill the formula to other cells.
Action API: @AutoFill(source="Sheet1!A2", destination="Sheet1!A2:A36")@
```

```agent/SheetCopilot_example_logs``` shows examples of the required log format (use the "[Order]_[Sheet Name]" naming convention).

Specify the correct paths in ```agent/config/config.yaml``` and then run this code within the ```agent``` folder to evaluate your results:
```
python evaluation.py
```

The evaluation results will be recorded in a file named ```eval_result.yaml``` under the result folder.

The evaluation can restart from a checkpoint if it has been aborted. If you want to re-evaluate, just delete the ```eval_result.yaml``` in the result folder.

**Important:** NOTE that
- Every new sheet must be created to the left of the very first sheet for correct matching with the references since sheet names are not to be checked.
- The sheet content must start from cell A1 and each sheet is required to contain contiguous tables.

## Evaluation results

The performances of SheetCopilot with 3 leading LLMs as its back-end on ```dataset/dataset_20Samples.xlsx```.

| Models        | Exec@1 | Pass@1 | A50  | A90  |
|---------------|--------|--------|------|------|
| GPT-3.5-Turbo | **85.0%**  | 45.0%  | 2.00 | 4.50 |
| GPT-4         | 65.0%  | **55.0%**  | **1.33** | **2.00** |
| Claude        | 80.0%  | 40.0%  | 1.50 | 4.40 |

The performances of SheetCopilot and a VBA-based method were evaluated on ```dataset/dataset.xlsx``` using ```dataset/task_sheet_answers``` as the ground truths. (Note: as we also included the functionally correct results generated by GPT-3.5-Turbo to ```dataset/task_sheet_answers_v2```, the evaluation results for this model remain the same whether you use v1 or v2 ground truths.)

| Methods       | Exec@1 | Pass@1 |
|---------------|--------|--------|
| GPT-3.5-Turbo | 87.3%  | 44.3%  |
| VBA-based     | 77.8%  | 16.3%  |

## The aspects of a spreadsheet SheetCopilot can control
(1) **Manipulation**: Writing values and formulas, deleting cells, inserting a row/column, auto-filling, copy-pasting values, find-and-replacing, setting hyperlinks, removing duplicates, creating sheets, clearing formats.

(2) **Management**: Sorting, filtering, and freezing panes.

(3) **Formatting**: Setting format and conditional format (font, bold, italic, underline, text color, and fill color), setting data type (date, text, number, currency, time, general, percentage), and merging.

(4) **Charts**: Creating charts, creating charts from pivot tables, setting chart title/axis title/legends/chart type/marker/trendline/data labels.

(5) **Pivot table**: Creating pivot tables.

(More operations will be added once the developers finish testing them. Besides, you can raise issues to ask for more supported operations or pull requests to upload your implementations.) 

## Demo

This video shows that SheetCopilot conducts GDP data analysis successfully.

[![GDP analysis Demo](./assets/DemoCover.png)](./assets/EasyGDP_Demo.mp4)

The video below shows SheetCopilot deployed on Google Sheets.

[![ See the magic of SheetCopilot on Google Sheets - Control your sheets like chatting ](https://res.cloudinary.com/marcomontalbano/image/upload/v1686982272/video_to_markdown/images/youtube--69Qu7v55fBY-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://youtu.be/69Qu7v55fBY " See the magic of SheetCopilot on Google Sheets - Control your sheets like chatting ")

You can upload ```task_sheets/BoomerangSales.xlsx``` and type in these instructions to reproduce the results in the demo:

1. Calculate the revenue for each transaction in the sales table considering the corresponding retail price and discount.
2. Highlight the Revenue cells greater than 500 in blue text.
3. Create a pivot table in a new sheet to show the counts of the websites on which boomerangs were sold.
4. Plot a bar chart for the pivot table in the same sheet.
5. Set the y-axis title as "Count" and turn off legends.
6. Create another pivot table in a new sheet to show the revenue sums of each product.
7. Plot a pie chart for the pivot table with chart title "Revenue by Product" in this sheet.

<br/>

# Citation
SheetCopilot and the dataset can only be used for non-commercial purposes.

If you use the SheetCopilot agent and benchmark, feel free to cite us.

```bibtex
@inproceedings{
li2023sheetcopilot,
title={SheetCopilot: Bringing Software Productivity to the Next Level through Large Language Models},
author={Hongxin Li and Jingran Su and Yuntao Chen and Qing Li and Zhaoxiang Zhang},
booktitle={Thirty-seventh Conference on Neural Information Processing Systems},
year={2023},
url={https://openreview.net/forum?id=tfyr2zRVoK}
}
