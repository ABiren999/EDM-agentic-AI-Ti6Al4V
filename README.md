# Agentic AI for EDM of Ti-6Al-4V: code and data

Code and data for the article

> [Author names]. *Agentic artificial intelligence for self-auditing machine-learning modelling and
> multi-objective optimisation of electrical discharge machining of Ti-6Al-4V.* [Journal], 2026. [DOI]

Archived version of this repository: [Zenodo DOI, e.g. https://doi.org/10.5281/zenodo.XXXXXXX]

The notebook implements a tool-using multi-agent framework: an Orchestrator and eleven specialist agents
(Data, MCDM, DoE, AutoML, Critic, Selection, XAI, Optimisation, Decision, Verification, Report) that share a
blackboard memory and act only through registered tools. Starting from 27 EDM experiments (pulse-on time,
pulse-off time, LV and HV currents, flushing pressure → R<sub>a</sub>, MRR, TWR), the agents build and audit
surrogate models, explain them, run multi- and single-objective optimisation, and verify the recommended
settings. Every step is recorded in an agent trace.

## Contents

| File / folder | Description |
|---|---|
| `Agentic_AI_EDM_Ti6Al4V.ipynb` | The notebook: all agents, tools, analyses and figures of the article, saved with the outputs of the full run |
| `Agentic_AI_EDM_Ti6Al4V_executed.html` | The same notebook with all outputs; opens in any web browser, no Python needed |
| `data/edm_ti6al4v_27runs.csv` | The 27 experimental runs and their TOPSIS closeness coefficients (Table 2 of the article) |
| `results/Agentic_EDM_results.xlsx` | Results workbook, one sheet per analysis (ANOVA, model benchmark, Critic actions, nested CV, Pareto set, recommendations, Sobol indices, agent decisions, …) |
| `results/agent_trace.json` | Complete agent trace (plans, thoughts, actions, observations, decisions, reflections) |
| `results/executive_summary.md` | Summary written by the Report agent |
| `results/TableS5_workflow_comparison.csv`, `results/Fig10_scan_minima.csv` | Comparison of the preliminary, corrected and agentic workflows (Fig. 10, Table S5) |
| `requirements.txt` | Python packages (with the versions used for the article) |
| `LICENSE` | MIT licence (code); CC BY 4.0 (data) |
| `CITATION.cff` | Citation metadata |

The data are also embedded in the notebook, so the notebook runs on its own.

## How to run

Requires Python 3.11 or later (tested with Python 3.13).

```bash
pip install -r requirements.txt
jupyter lab Agentic_AI_EDM_Ti6Al4V.ipynb        # then: Run > Run All Cells
```

or, without a graphical interface:

```bash
jupyter nbconvert --to notebook --execute --ExecutePreprocessor.timeout=-1 \
        --output Agentic_AI_EDM_Ti6Al4V_executed.ipynb Agentic_AI_EDM_Ti6Al4V.ipynb
```

The notebook also runs in Visual Studio Code (Jupyter extension) with *Run All*.

* **Run time:** about 10–17 min on a two-core computer; most of it is hyper-parameter tuning and the
  nested cross-validation audit. Independent jobs run in parallel on all available cores.
* **Quick test:** set the environment variable `EDM_RUN_MODE=fast` before starting Jupyter to run reduced
  budgets in a few minutes (the numbers then differ from the article).
* **Outputs:** figures are written to `figures/` (600-dpi PNG and vector PDF; set `save_tiff=True` in the
  configuration for TIFF) and tables to `results/`.

## Reproducibility

All stochastic components (cross-validation splits, TPE samplers, evolutionary algorithms, bootstrap and
quasi-Monte-Carlo samples) are seeded from one seed (`seed = 2024` in the `Config` object), and every agent
decision is taken by a deterministic, documented rule policy, so repeated runs give identical numbers.

The article was produced with Python 3.13, NumPy 2.5.3, pandas 3.0.5, SciPy 1.18.1, scikit-learn 1.9.1,
XGBoost 3.4.1, Optuna 5.0.0, pymoo 0.6.2 and Matplotlib 3.11.2. Other versions may change results in the
last digits.

## Organisation of the notebook

| Section | Content | Results in the article |
|---|---|---|
| 1. Set-up and configuration | Packages, figure style, `Config` object with all budgets and thresholds | Table 4; settings of Sections 2.5–2.11 |
| 2. Experimental data | The 27 runs, factor bounds, cross-check of the TOPSIS closeness | Tables 1 and 2 |
| 3. Agentic core | Agent trace, blackboard, tool registry, reasoning engine, Orchestrator | Fig. 1; Table S1 |
| 4. Domain tools | TOPSIS and weighting, ANOVA, model library and search spaces, TPE tuning, LOOCV, nested CV, model audit, Shapley values, Sobol indices, optimisers | Eqs. (1)–(13); Table S3 |
| 5. Specialist agents | The eleven agents and their decision rules | Table 3 |
| 6. Run | Registers the 22 tools, assembles the agents and executes the goal | All results; Table S6 |
| 7. Key result tables | Printed summaries of every analysis | Tables 5–8; Tables S2, S4 |
| 8. Figures | One cell per figure; headings carry the figure numbers of the article | Figs. 1–9; Figs. S1–S13 |
| 9. Single-surrogate workflows | Audit of the preliminary single-surrogate analysis; the corrected GPR of the companion study is refitted and compared | Fig. 10; Table S5 |
| 10. Reproducibility | Package versions, run mode, output index, executive summary | – |

Figure files are named after the article: `Fig01_…` to `Fig10_…` for the main text and `FigS01_…` to
`FigS13_…` for the supplementary file.

## Changes in version 1.1

* Section 9 also refits the corrected single-surrogate workflow of the companion study (one GPR with a
  constant × Matérn ν = 5/2 kernel on standardised inputs with a normalised target, seed 42) and adds it to
  Fig. 10 (blue dashed curve and bars) and to the comparison table (Table S5).
* The optimum of the companion study is stored as the constant `COMPANION` in the data cell (Section 2). No
  other code was changed, and all other results are identical to version 1.0 (only the measured run times
  differ from machine to machine).

## Companion study

The same 27 experiments are reported, with the surface characterisation and a single-surrogate
GPR–NSGA-II–TOPSIS–SHAP optimisation, in: P.K. Mohanty, M. Das, T.R. Mahapatra, *Multi-objective
optimization of EDM for Ti-6Al-4V using Gaussian process regression modeling, NSGA-II, TOPSIS and SHAP*,
submitted for publication.

## Optional LLM reasoning engine

By default the agents decide with the rule policy. Setting `use_llm=True` (and `llm_provider`,
`llm_model`) in the `Config` object lets a large language model (Anthropic, OpenAI or a local Ollama model)
answer the same decision questions. Its answer is accepted only if it is one of the admissible options;
otherwise the rule-based decision is used. The API key is read from `ANTHROPIC_API_KEY` or `OPENAI_API_KEY`.
This option was **not** used for any result in the article.

## Licence and citation

Code: MIT licence. Data: CC BY 4.0. If you use the code or data, please cite the article (see `CITATION.cff`).
