# Model Card

## Model Description

**Input:** Eight black-box functions with inputs normalized to [0,1]. Dimensionality ranges from 2D to 8D. Each function receives a vector of continuous values representing different parameters (e.g., chemical concentrations, hyperparameters, recipe ingredients).

**Output:** A single scalar value representing the function's response. The goal is to maximize this output. Output ranges vary by function, from near-zero (Function 1) to thousands (Function 5).

**Model Architecture:** Gaussian Process regression with Upper Confidence Bound (UCB) acquisition function.

- Surrogate model: Gaussian Process with Matern kernel (nu=2.5) and WhiteKernel for noise
- Acquisition function: UCB(x) = mean(x) + beta * std(x)
- Beta schedule: Dimension-dependent, starting at 5.0 (2D) to 2.0 (8D), decaying linearly to 0.3-0.5 over 13 iterations
- Candidate selection: Maximum UCB from 10,000 random samples per iteration

## Performance

| Function | Dims | Initial Best | Final Best | Improvement |
|----------|------|--------------|------------|-------------|
| 1 | 2 | 0.0000 | 0.0000 | None |
| 2 | 2 | 0.6112 | 0.6112 | None |
| 3 | 3 | -0.0348 | -0.0234 | +33% |
| 4 | 4 | -4.0255 | +0.4461 | Sign flip |
| 5 | 4 | 1088.86 | 6531.56 | +500% |
| 6 | 5 | -0.7143 | -0.2041 | +71% |
| 7 | 6 | 1.3650 | 2.7128 | +99% |
| 8 | 8 | 9.5985 | 9.9285 | +3% |

Performance measured by best output found after 13 iterations compared to best value in initial data. Strong improvements on Functions 3-7. No improvement on Functions 1 and 2.

## Limitations

- Assumes smooth, twice-differentiable functions. Fails on sparse signals (Function 1) where the optimum is highly localized.
- Fixed beta schedule cannot adapt when queries stop improving.
- Random candidate sampling (10,000 points) provides poor coverage in high dimensions.
- Same strategy applied to all functions regardless of their characteristics.
- GP fitting scales O(n³), limiting use beyond a few hundred observations.

## Trade-offs

- Exploration vs exploitation: High beta early finds promising regions but wastes queries if the optimum is found quickly. Low beta late refines solutions but may miss better regions.
- Dimension-dependent beta: Lower exploration in high dimensions accepts that full coverage is impossible, but may miss narrow optima.
- Uniform strategy: Simpler to implement and debug, but underperforms compared to function-specific approaches.
- Random candidates vs gradient optimization: Faster per iteration but less efficient at finding the true UCB maximum.
