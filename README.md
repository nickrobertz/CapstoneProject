# Capstone Project: Black-Box Function Optimization

## Overview  
This repository contains my capstone project implementing Bayesian Optimization to maximize eight unknown black-box functions. Using Gaussian Process surrogate models with Upper Confidence Bound acquisition, the project explores the exploration-exploitation tradeoff under a limited query budget of 13 iterations per function.

## Problem  
Optimize eight black-box functions with varying dimensionality (2D to 8D) where:
- Function internals are unknown
- Each query is expensive (limited to 13 per function)
- Inputs are normalized to [0,1]
- Goal is to maximize output values

## Documentation  
- [Datasheet](Documentation/datasheet.md) - Details on the dataset: motivation, composition, collection process, and intended uses
- [Model Card](Documentation/model_card.md) - Details on the optimization approach: strategy, performance, assumptions, and limitations

## Project Structure  
```
├── Data/
│   ├── InitialData/        # Starting samples for each function
│   └── Predictions/        # Submitted queries and results
├── Documentation/
│   ├── datasheet.md        # Dataset documentation
│   └── model_card.md       # Model/approach documentation
├── Notebooks/
│   └── Initial_Data_Inspection.ipynb
├── SourceCode/
│   └── bayesian_optimization.ipynb  # Main optimization implementation
└── README.md
```

## Key Technologies  
- Python (core programming language)  
- NumPy, Pandas, Scikit-learn (data wrangling & GP modeling)  
- Matplotlib (visualizations)  
- Jupyter Notebooks (experiments & analysis)  

## Results Summary  
After 9 iterations, significant improvements on 6 of 8 functions:
- Function 5 (chemical yield): +500% improvement  
- Function 7 (hyperparameter tuning): +99% improvement  
- Function 4 (warehouse optimization): Found positive regime from negative initial data  
- Functions 1 and 2: No improvement (sparse signal / local optima challenges)

See the [Model Card](Documentation/model_card.md) for detailed performance analysis.

