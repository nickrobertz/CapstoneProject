# Datasheet: Black-Box Optimization Query Dataset

## 1. Motivation

**Why was this dataset created?**
This dataset was created as part of a capstone project to explore and optimize eight unknown black-box functions. The goal was to find input combinations that maximize each function's output using a limited query budget of 13 iterations per function.

**What task does it support?**
The dataset supports sequential decision-making under uncertainty. It provides training data for Gaussian Process surrogate models and documents the exploration-exploitation tradeoff in Bayesian optimization.

**Who created it and who funded it?**
Created by Nick Roberts as an individual capstone project. No external funding.


## 2. Composition

**What does the dataset contain?**
Input-output pairs for eight black-box functions with varying dimensionality:

| Function | Dims | Initial Samples | Added Queries | Total Samples | Domain |
|----------|------|-----------------|---------------|---------------|--------|
| 1 | 2 | 10 | 13 | 23 | Contamination detection |
| 2 | 2 | 10 | 13 | 23 | Noisy log-likelihood |
| 3 | 3 | 15 | 13 | 28 | Drug discovery |
| 4 | 4 | 30 | 13 | 43 | Warehouse optimization |
| 5 | 4 | 20 | 13 | 33 | Chemical process yield |
| 6 | 5 | 20 | 13 | 33 | Recipe optimization |
| 7 | 6 | 30 | 13 | 43 | ML hyperparameter tuning |
| 8 | 8 | 40 | 13 | 53 | High-dimensional optimization |

**What is the format?**
- Initial data: NumPy arrays (.npy files)
- Query submissions: Text files with Python array notation (inputs1.txt through inputs13.txt)
- Query results: Text files with numpy float values (outputs1.txt through outputs13.txt)
- All inputs normalized to [0,1] range
- Outputs are scalar values with function-specific ranges

**What is the size?**
Total of 279 input-output pairs across all eight functions. File sizes are small (few KB each).

**Are there gaps or missing data?**
No missing values. However, the dataset is sparse relative to input space dimensions. Function 1 shows a notable gap: no query found a non-zero signal region despite 13 attempts.

**Is the data self-contained?**
Yes. All inputs and outputs needed to reproduce the optimization trajectory are stored in the Data folder.


## 3. Collection Process

**How were the initial samples acquired?**
Initial samples were provided as part of the capstone project specification, presumably collected through random or space-filling sampling by the course instructors.

**How were the queries generated?**
Queries were generated using Bayesian optimization:
- Gaussian Process regression with Matern kernel (nu=2.5) as the surrogate model
- Upper Confidence Bound (UCB) acquisition function
- Dimension-dependent beta schedule starting high (5.0 for 2D, 2.0 for 8D) and decaying linearly over 13 iterations
- Next query selected by maximizing UCB over 10,000 random candidates

**What was the time frame?**
Data was collected over approximately two weeks, with iterations submitted in batches. Each iteration involved running the notebook, generating predictions, submitting to the black-box evaluation system, and recording outputs.

**Who was involved in data collection?**
Queries were generated algorithmically using the Bayesian optimization notebook. Black-box function evaluations were performed by an external system provided by the course.

**Were there any ethical considerations?**
No human subjects or sensitive data involved. All functions are synthetic or simulated scenarios.


## 4. Preprocessing and Uses

**What preprocessing was applied?**
- All inputs were pre-normalized to [0,1] by the data providers
- Outputs were used as-is without transformation
- GP model uses normalize_y=True internally for numerical stability
- No outlier removal or data augmentation was performed

**What are the intended uses?**
- Training and evaluating Bayesian optimization algorithms
- Studying exploration-exploitation tradeoffs
- Comparing acquisition function performance
- Demonstrating GP-based surrogate modeling

**What uses should be avoided?**
- Should not be used to claim general superiority of any optimization method
- Function 1's sparse signal characteristics make it unsuitable for evaluating methods that assume smooth landscapes
- Limited sample sizes mean the data cannot support conclusions about global optima
- Results should not be extrapolated to real domain applications without validation


## 5. Distribution and Maintenance

**Where is the dataset available?**
The dataset is stored in the project repository:
- /Data/InitialData/ contains original samples
- /Data/Predictions/ contains query submissions and results

**What are the terms of use?**
Created for educational purposes as part of a capstone project. Available for academic review and reproducibility verification.

**Who maintains it?**
Nick Roberts maintains the dataset. The dataset is now frozen following project completion.

**How can errors be reported?**
Contact the author directly through the course or project repository.
