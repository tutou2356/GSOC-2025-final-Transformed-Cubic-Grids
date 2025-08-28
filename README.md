# Google Summer of Code 2025 Final Report

- **Student:** Wanhui Geng 
- **Mentors:** Ali Tehrani, Farnaz Heidar-Zadeh, Marco Martínez-González
- **Organization:** QC Devs
- **Project:** Transformed Cubic Grids

## 1. Project Background

In quantum chemistry, the calculation of many key physical quantities (such as electron density and energy) relies on integrals. But analytical forms for these integrals are often very complex ($E=∫ρ(r)v(r)dr$) or even nonexistent. One of solution is the Numerical integration, which approximates the integral result by constructing a grid in space and performing a weighted summation. 

The central challenge is to design a grid that is both **highly accurate** and **computationally efficient**. The `grid` library already had several approaches:

- **Becke Grid:** A multi-center grid that is accurate near atomic nuclei but inefficient in the space between atoms, as each atom's grid spans the entire space.
- **Uniform Grid:** A simple grid with evenly spaced points. While easy to understand, but the accuracy is not ideal, and sometimes a huge number of points is required to meet the requirements. 
- **CubicProTransform Grid:** An intelligent grid that maps a simple uniform cube from $[-1, 1]^3$ to real space using a promolecular density function. This concentrates grid points in regions of high electron density, significantly improving accuracy.

My GSoC project aims to supplement the testing of the `CubicProTransform`  method and try to build a new adaptive grid method.

## 2. My Contributions

My work this summer can be divided into two main areas: 

### 2.1.  `CubicProTransform` Grid Integration and Analysis

- **BFit Data Processor**: I created the `BFitDataProcessor` class in `utils_promol.py`. This utility parses standard `.npz` files containing promolecular coefficients and exponents (BFit data), making the grid compatible with established quantum chemistry data sources.
- **Performance test suite**: I wrote a comprehensive test script, `test_CubicProTransform.py`, to compare the integration accuracy and performance of `UniformGrid` versus `CubicProTransform`. This test was run on a series of small molecules (He, H₂, HCl, CH₄, H₂O), demonstrating the clear advantage of the promolecular approach in achieving higher accuracy with fewer points.

### 2.2. Implementation of `AdaptiveUniformGrid`

The second main work was the implementation of the `AdaptiveUniformGrid` class in `cubic.py`. 

- **Algorithm:** The process begins with a coarse uniform grid. It then iteratively identifies cells with high integration error and subdivides them into finer cells. This process continues until a specified tolerance is met, resulting in a grid with high point density only where it's needed most.
- **Core Implementation:**
  - `refinement()`: The main public method that drives the adaptive loop.
  - `_estimate_error()`: A function to calculate the local error within a grid cell.
  - `_generate_subdivision_points()`: A function that generates the new points for a cell being refined.
- **Optimization:** To prevent excessive refinement and improve efficiency, I implemented several control mechanisms:
  - `max_depth` and `min_spacing`: Limit the recursion depth and the smallest possible cell size.
  - `refine_contrib_threshold`: Skips refinement in regions where the function's contribution to the total integral is negligible, saving significant computation time.

## 3. Pull Requests

My contributions have been submitted to the `theochem/grid` repository through the following pull requests:

- Add electron density integration tools and BFit data processor: https://github.com/theochem/grid/pull/280
- Implement adaptive uniformgrid refinement: https://github.com/theochem/grid/pull/279

## 4. Future Work

1. **Performance Optimization:** The `AdaptiveUniformGrid` implementation can be further optimized by exploring more advanced data structures and vectorization strategies.
2. **Improving Jupyter Notebook as a User Tutorial**: To make it easier for new users to understand and get started, I plan to expand the Jupyter Notebook used in the final report into a comprehensive, interactive user tutorial, making it a standalone documentation resource valuable to the community.

## 5. Acknowledgements

I want to extend my sincere gratitude to my mentors, **Ali Tehrani**, **Farnaz Heidar-Zadeh**, and **Marco Martínez-González**, for their invaluable guidance, support, and expertise throughout this project. I would also like to thank the **Theochem organization** and **Google** for providing this incredible opportunity to contribute to a meaningful open-source project. This experience has been immensely rewarding and has significantly deepened my skills in software development and computational science.
