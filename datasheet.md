# Datasheet: Black-Box Optimization Query Dataset

## Motivation

Why was this dataset created?
This dataset was created as part of a capstone project to explore and optimize eight unknown black-box functions. The goal was to find input combinations that maximize each function's output using a limited query budget of 13 iterations per function.

What task does it support?
The dataset supports sequential decision-making under uncertainty. It provides training data for Gaussian Process surrogate models and documents the exploration-exploitation tradeoff in Bayesian optimization. It can be used to analyze optimization trajectories, compare acquisition strategies, and study how well GP models approximate unknown functions from sparse observations.

Who created it and who funded it?
Created by Nick Roberts as an individual capstone project. No external funding.


## Composition

What does the dataset contain?
The dataset contains input-output pairs for eight black-box functions with varying dimensionality:

| Function | Dimensions | Initial Samples | Added Queries | Total Samples | Domain Description |
|----------|------------|-----------------|---------------|---------------|---------------------|
| 1 | 2 | 10 | 9 | 19 | Contamination detection |
| 2 | 2 | 10 | 9 | 19 | Noisy log-likelihood |
| 3 | 3 | 15 | 9 | 24 | Drug discovery |
| 4 | 4 | 30 | 9 | 39 | Warehouse optimization |
| 5 | 4 | 20 | 9 | 29 | Chemical process yield |
| 6 | 5 | 20 | 9 | 29 | Recipe optimization |
| 7 | 6 | 30 | 9 | 39 | ML hyperparameter tuning |
| 8 | 8 | 40 | 9 | 49 | High-dimensional optimization |

What is the format?
- Initial data: NumPy arrays stored as .npy files (initial_inputs.npy, initial_outputs.npy)
- Query submissions: Text files with Python array notation (inputs1.txt through inputs9.txt)
- Query results: Text files with numpy float values (outputs1.txt through outputs9.txt)
- All inputs are normalized to [0,1] range
- Outputs are scalar values with function-specific ranges

What is the size?
Total of 247 input-output pairs across all eight functions. Individual functions range from 19 to 49 samples. File sizes are small (few KB each).

Are there gaps or missing data?
No missing values. However, the dataset is sparse relative to the input space dimensions. For example, 49 samples in 8D space provides extremely limited coverage. Function 1 shows a gap in that no query found a non-zero signal region despite 9 attempts.

Is the data self-contained?
Yes. All inputs and outputs needed to reproduce the optimization trajectory are stored in the Data folder.


## Collection Process

How were the initial samples acquired?
Initial samples were provided as part of the capstone project specification. They represent prior evaluations of each black-box function, presumably collected through random or space-filling sampling by the course instructors.

How were the queries generated?
Queries were generated using Bayesian optimization with the following strategy:
- Gaussian Process regression with Matern kernel (nu=2.5) as the surrogate model
- Upper Confidence Bound (UCB) acquisition function: UCB(x) = mean(x) + beta * std(x)
- Dimension-dependent beta schedule starting high (5.0 for 2D, 2.0 for 8D) and decaying linearly to 0.3-0.5 over 13 iterations
- Next query selected by maximizing UCB over 10,000 random candidates

What was the time frame?
Data was collected over approximately one week, with one iteration submitted per session. Each iteration involved running the notebook, generating predictions, submitting to the black-box evaluation system, and recording outputs.

Who was involved in data collection?
I generated all queries algorithmically using the Bayesian optimization notebook. The black-box function evaluations were performed by an external system provided by the course.

Were there any ethical considerations?
No human subjects or sensitive data involved. All functions are synthetic or simulated scenarios.


## Preprocessing and Uses

What preprocessing was applied?
- All inputs were pre-normalized to [0,1] by the data providers
- Outputs were used as-is without transformation
- GP model uses normalize_y=True internally for numerical stability
- No outlier removal or data augmentation was performed

What are the intended uses?
- Training and evaluating Bayesian optimization algorithms
- Studying exploration-exploitation tradeoffs
- Comparing acquisition function performance
- Demonstrating GP-based surrogate modeling

What uses should be avoided?
- The dataset should not be used to claim general superiority of any optimization method, as it reflects a single strategy with specific hyperparameters
- Function 1's sparse signal characteristics make it unsuitable for evaluating methods that assume smooth landscapes
- The limited sample sizes (especially for high-dimensional functions) mean the data cannot support conclusions about global optima
- Results should not be extrapolated to real contamination detection, drug discovery, or other domain applications without validation


## Distribution and Maintenance

Where is the dataset available?
The dataset is stored locally in the project repository:
- /Data/InitialData/ contains original samples
- /Data/Predictions/ contains query submissions and results
- /Data/Saves/ contains backups

What are the terms of use?
Created for educational purposes as part of a capstone project. No formal license. Available for academic review and reproducibility verification.

Who maintains it?
Nick Roberts maintains the dataset. No plans for updates after the capstone project concludes, as the optimization trajectory is complete.

Will it be updated?
The final 3 iterations (10-12) will be added as they are completed. After iteration 13, the dataset will be frozen.

How can errors be reported?
Contact the author directly through the course or project repository.

