# Model Card: GP-UCB Bayesian Optimization

## Overview

Name: GP-UCB Bayesian Optimizer with Dimension-Dependent Beta Schedule

Type: Sequential model-based optimization using Gaussian Process surrogate with Upper Confidence Bound acquisition

Version: 1.0 (Capstone Project Implementation)

Developer: Nick Roberts

Date: February 2026


## Intended Use

Primary intended uses:
- Optimizing expensive-to-evaluate black-box functions where gradients are unavailable
- Problems with continuous input spaces normalized to [0,1]
- Scenarios with limited evaluation budgets (10-50 total queries)
- Functions expected to be reasonably smooth (at least twice differentiable)

Users:
- Researchers exploring hyperparameter tuning
- Engineers optimizing simulation-based designs
- Students learning Bayesian optimization concepts

Use cases to avoid:
- Functions with highly discontinuous or sparse signals (see Function 1 failure)
- Very high-dimensional problems (beyond 10D) where GP scaling becomes problematic
- Problems requiring real-time decisions (GP fitting adds latency)
- Situations where function evaluations are cheap (random search may suffice)
- Dynamic functions that change between evaluations


## Approach Details

Strategy overview:
The approach uses a Gaussian Process to model the unknown function based on observed input-output pairs. At each iteration, the GP provides both a predicted mean and uncertainty estimate for any candidate input. The Upper Confidence Bound acquisition function balances exploitation (high predicted mean) with exploration (high uncertainty) by computing UCB(x) = mean(x) + beta * std(x).

Key design choices:

1. Kernel selection: Matern kernel with nu=2.5, which assumes the function is twice differentiable. Length scale bounds set to [0.1, 5.0] for normalized inputs. Isotropic kernel (same length scale for all dimensions).

2. Beta schedule: Dimension-dependent starting values (5.0 for 2D down to 2.0 for 8D) with linear decay to 0.3-0.5 over 13 iterations. Higher dimensions start with lower beta because the space is exponentially larger and pure exploration is impractical.

3. Candidate generation: Next query selected by evaluating UCB on 10,000 random candidates and picking the maximum. No gradient-based optimization of the acquisition function.

4. No function-specific adaptation: All eight functions use the same strategy, differing only by dimensionality.

Evolution across rounds:
- Iterations 1-4: High exploration phase. Beta values ranged from 2.5-5.0 depending on dimension. Queries spread across input space to build GP coverage.
- Iterations 5-7: Transition phase. Beta values around 1.5-2.5. Mix of exploration and exploitation based on what the GP learned.
- Iterations 8-10: Exploitation phase. Beta values dropped below 1.5 for higher dimensions. Queries concentrated near promising regions identified earlier.
- Iterations 11-13: Final exploitation. Beta values at minimum (0.3-0.5). Focus on refining best-known solutions.


## Performance Summary

Results after 9 iterations (best output achieved):

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

Metrics used:
- Best output found (maximization objective)
- Improvement over initial best
- Iteration at which best was found

Performance patterns:
- Strong performance on Functions 4, 5, 6, 7 with substantial improvements
- Function 5 showed clear unimodal behavior in high-value corner, easy to exploit
- Function 4 discovered a positive-value regime that initial data missed entirely
- Functions 1 and 2 showed no improvement, suggesting the approach failed to find optimal regions
- Function 8 improved modestly despite high dimensionality


## Assumptions and Limitations

Key assumptions:

1. Smoothness: The Matern kernel assumes functions are twice differentiable with similar behavior across the input space. Functions with discontinuities, sharp spikes, or sparse signals violate this assumption.

2. Stationarity: The GP uses the same length scale everywhere, assuming the function's variability is uniform across regions. Non-stationary functions (smooth in some areas, rough in others) are poorly modeled.

3. Noise structure: WhiteKernel assumes Gaussian observation noise. Functions with non-Gaussian noise or heteroscedastic noise may be misrepresented.

4. Adequate initial coverage: The approach assumes initial samples provide enough information for the GP to make reasonable predictions. Extremely sparse initial data in high dimensions limits what the GP can learn.

Limitations:

1. No function-specific adaptation: The one-size-fits-all strategy treats sparse-signal problems (Function 1) the same as unimodal problems (Function 5). An adaptive approach that recognized function characteristics would perform better.

2. Random candidate selection: Using 10,000 random candidates to maximize UCB is inefficient compared to gradient-based optimization, especially in higher dimensions.

3. Computational scaling: GP fitting scales as O(n³) with observations. Beyond a few hundred points, sparse approximations would be needed.

4. Deterministic suggestions: Given the same data, the approach generates the same next query (modulo random candidate sampling). No explicit mechanism to escape local optima.

Failure modes:
- Sparse signals: If the optimum occupies a tiny fraction of the space, random candidate generation is unlikely to find it
- Multimodal landscapes: The GP may commit to one mode and miss better modes elsewhere
- High dimensions: 10,000 candidates in 8D provides very sparse coverage


## Ethical Considerations

Transparency and reproducibility:
The complete codebase, data, and decision logic are documented in the project repository. Another researcher can:
- Inspect the exact beta schedule and kernel parameters
- Replay all queries using saved input/output files
- Understand why each query was selected by examining the UCB calculation
- Modify parameters and compare results

The approach makes no hidden decisions. All randomness comes from candidate generation, which uses numpy's default random state.

Real-world adaptation considerations:
Before deploying this approach in real applications (actual drug discovery, chemical processes, etc.), practitioners should:
- Validate the smoothness assumption for their specific function
- Consider whether the 13-iteration budget is appropriate for their problem
- Evaluate whether dimension-dependent beta schedules match their problem characteristics
- Add domain constraints if certain input combinations are physically impossible or dangerous
- Implement safety checks if function evaluations have real-world consequences

What this model card does not cover:
- Comparison with alternative methods (random search, other acquisition functions)
- Theoretical convergence guarantees
- Confidence intervals on reported improvements
- Cost analysis of function evaluations

Why current structure is sufficient:
This model card documents the key decisions, assumptions, and results needed to understand and reproduce the approach. Additional detail on GP theory or UCB derivations would not improve practical usefulness for someone reviewing the capstone project. The limitations section explicitly acknowledges where the approach failed and why, which is more valuable than additional technical depth.

