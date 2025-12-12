# Black-Box Optimisation Capstone Project

## Section 1: Project Overview

This project addresses the problem of optimising eight unknown black-box functions where only function evaluations are observable. Each function takes between two and eight input dimensions, with all inputs normalised to the ([0,1]) range, and returns a single scalar output. For each function, a fixed budget of 13 queries is available to identify inputs that maximise the output.

The functions span a range of application domains. Functions 1 and 2 are two-dimensional problems involving radiation source detection with sparse signals and noisy log-likelihood optimisation. Function 3 models a drug discovery scenario in which compound mixtures are tuned to reduce side effects. Functions 4 and 5 are four-dimensional problems focused on warehouse placement in a dynamic environment and chemical yield maximisation, respectively, with the latter being largely unimodal. Function 6 optimises a cake recipe using noisy human taste evaluations. Functions 7 and 8 represent machine learning hyperparameter optimisation tasks in six and eight dimensions, where the curse of dimensionality makes identifying strong local optima more realistic than global optimisation.

From a professional perspective, this project builds intuition for decision-making under uncertainty. Many applied machine learning problems involve limited feedback and constrained experimentation budgets, such as deciding when to stop data collection or which model architecture to pursue. The optimisation strategies explored here closely mirror those used in production systems, where exploration must be balanced against the need to deliver reliable performance.

---

## Section 2: Inputs and Outputs

Each function has a fixed input dimensionality and is accompanied by an initial set of observations. The number of dimensions ranges from 2 to 8, with initial sample counts increasing for higher-dimensional functions.

All inputs must lie in the interval ([0,1]). Queries are submitted as dash-separated strings with six decimal places per dimension. For example:

```text
2D: 0.372077-0.993565
3D: 0.971080-0.999591-0.982864
8D: 0.050675-0.062857-0.048435-0.144023-0.868954-0.168585-0.186447-0.284064
```

Each query returns a single floating-point value corresponding to the function evaluation at the specified input. No information about gradients, smoothness, or analytical structure is provided, and the optimisation process relies entirely on these numerical outputs.

---

## Section 3: Challenge Objectives

The objective is to maximise each function’s output within the constraint of 13 additional queries beyond the initial data. Since the query budget is strictly limited, each evaluation must be chosen carefully.

There is no access to the internal structure of the functions, their derivatives, or any closed-form representation. Responses are delayed after submission, and the functions may exhibit challenging characteristics such as multiple local maxima or discontinuities. As dimensionality increases, the effective search space grows exponentially, further complicating the optimisation process.

The main challenge is therefore to develop a strategy that performs consistently across all eight functions despite significant differences in dimensionality and response surface complexity.

---

## Section 4: Technical Approach

The optimisation strategy is based on Gaussian Process regression combined with an Upper Confidence Bound acquisition function. The Gaussian Process provides a probabilistic model of the unknown function, producing both a predictive mean and an uncertainty estimate at any candidate input. The UCB acquisition function selects points that maximise the sum of the predicted mean and a multiple of the predictive uncertainty.

A key design choice is the scheduling of the UCB exploration parameter. The parameter controlling the exploration–exploitation trade-off is decayed linearly over the 13 optimisation iterations, encouraging broad exploration early on and more focused exploitation as additional data is collected.

The initial value of this parameter is adjusted based on dimensionality. In lower-dimensional settings, the initial samples provide relatively good coverage of the space, making aggressive exploration more effective. In contrast, higher-dimensional functions remain sparsely sampled even with larger initial datasets, so the strategy shifts toward exploitation earlier. At iteration three, the exploration parameter is set higher for two-dimensional functions and progressively lower for four-, six-, and eight-dimensional functions to reflect this trade-off.

The Gaussian Process uses a Matérn 5/2 kernel with a single shared length scale across dimensions. This kernel allows for moderate local variation while maintaining smoothness assumptions that are reasonable given the limited data. While an isotropic kernel may underrepresent differences in importance across dimensions, it provides a simpler and more stable model in low-data regimes.

To select the next evaluation point, a large set of random candidate points is sampled uniformly from the input domain, and the point with the highest UCB value is chosen. Although this approach is less sophisticated than directly optimising the acquisition function, it is robust, avoids issues with local optima, and performs reliably given the small query budget.

As optimisation proceeds, the distribution of high-performing points will be monitored to identify potential structure in the input space. If clear patterns emerge, the strategy may be adapted in later iterations. At this stage, however, the approach remains deliberately general due to the lack of prior information about the underlying functions.

