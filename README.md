# Burrito Truck Location Optimization Project

## Project Overview

This project implements a sophisticated optimization model to solve the **Burrito Truck Location Problem** using Python and the Gurobi optimization library[1]. The model determines optimal truck locations to maximize profit while serving all demand nodes in a strategic facility location problem. The project explores multiple scenarios with varying cost structures to provide comprehensive business insights for decision-making.

## Problem Description

The Burrito Truck Location Problem is a variant of the classical facility location problem where:
- **Objective**: Maximize total profit by strategically placing burrito trucks
- **Constraints**: Each demand node must be served by exactly one truck
- **Decision Variables**: Which truck locations to open and which trucks serve which demand nodes
- **Key Parameters**: Burrito selling price, ingredient costs, truck operating costs, and scaled demand values

## Technical Implementation

### Libraries and Dependencies

```python
import pandas as pd
import gurobipy as gp
from gurobipy import GRB, quicksum
```

The project leverages:
- **Pandas**: Data manipulation and CSV file processing
- **Gurobi**: Commercial optimization solver for mixed-integer programming
- **Python 3.x**: Core programming environment

### Data Structure

The optimization model uses four primary datasets:

#### 1. Demand Node Data (`round1-day2_demand_node_data.csv`)
- **Columns**: `['index', 'name', 'x', 'y', 'demand']`
- Contains geographical coordinates and demand information for customer locations

#### 2. Truck Node Data (`round1-day2_truck_node_data.csv`)
- **Columns**: `['index', 'x', 'y']`
- Potential truck location coordinates and identifiers

#### 3. Demand-Truck Relationship Data (`round1-day2_demand_truck_data.csv`)
- **Columns**: `['demand_node_index', 'truck_node_index', 'distance', 'scaled_demand']`
- Critical relationships showing how many burritos each truck can serve to each demand node

#### 4. Problem Parameters (`round1-day2_problem_data.csv`)
- **Columns**: `['burrito_price', 'ingredient_cost', 'truck_cost']`
- Economic parameters for profit calculations

## Mathematical Model

### Decision Variables

- **z_i**: Binary variable (1 if truck i is opened, 0 otherwise)
- **x_{i,j}**: Binary variable (1 if truck i serves demand node j, 0 otherwise)

### Objective Function

```
Maximize: Σ(profit_per_burrito × scaled_demand[i,j] × x[i,j]) - Σ(truck_cost × z[i])
```

Where: `profit_per_burrito = burrito_price - ingredient_cost`

### Constraints

1. **Service Requirement**: Each demand node must be served exactly once
   ```
   Σ x[i,j] = 1 ∀j ∈ demand_nodes
   ```

2. **Truck Activation**: Trucks must be open to serve demand nodes
   ```
   x[i,j] ≤ z[i] ∀(i,j) ∈ valid_pairs
   ```

## Scenario Analysis

The project implements four distinct scenarios to analyze business sensitivity:

### Scenario 1: Base Case (Original Parameters)
- **Ingredient Cost**: $5.00
- **Optimal Profit**: $3,710.00
- **Trucks Selected**: 6 trucks (truck8, truck16, truck26, truck37, truck45, truck56)
- **Total Burritos Served**: 1,042

### Scenario 2: Increased Ingredient Cost
- **Ingredient Cost**: $7.00 (+40% increase)
- **Optimal Profit**: $1,714.00 (-53.8% decrease)
- **Trucks Selected**: 5 trucks (truck14, truck18, truck26, truck33, truck37)
- **Business Impact**: Significant profit reduction with fewer truck locations

### Scenario 3: Reduced Ingredient Cost
- **Ingredient Cost**: $4.00 (-20% decrease)
- **Optimal Profit**: $4,752.00 (+28.1% increase)
- **Trucks Selected**: 6 trucks (same as base case)
- **Business Impact**: Enhanced profitability with identical location strategy

### Scenario 4: Break-Even Analysis
- **Ingredient Cost**: $9.10 (near break-even point)
- **Optimal Profit**: -$50.70 (loss scenario)
- **Trucks Selected**: 3 trucks (truck15, truck26, truck33)
- **Business Impact**: Demonstrates cost threshold where business becomes unprofitable

## Key Features

### Optimization Engine
- **Solver**: Gurobi Optimizer version 12.0.1
- **Model Type**: Mixed Integer Binary Programming
- **Variables**: 395 binary variables
- **Constraints**: 405 constraints with 1,110 non-zeros

### Performance Metrics
- **Solution Time**: Sub-second optimization across all scenarios
- **Solution Quality**: Optimal solutions found for all test cases
- **Scalability**: Efficient handling of 60+ demand nodes and 60+ potential truck locations

### Business Intelligence
- **Sensitivity Analysis**: Cost impact assessment across ingredient price ranges
- **Location Strategy**: Optimal truck placement recommendations
- **Profitability Thresholds**: Break-even analysis for strategic planning

## Project Structure

```
├── project-group-3.ipynb          # Main Jupyter notebook with complete analysis
├── round1-day2_demand_node_data.csv    # Customer location data
├── round1-day2_truck_node_data.csv     # Potential truck locations
├── round1-day2_demand_truck_data.csv   # Demand-supply relationships
├── round1-day2_problem_data.csv        # Economic parameters
└── README.md                           # This documentation
```

## Usage Instructions

### Prerequisites
```bash
pip install pandas gurobipy
```

**Note**: Gurobi requires a valid license (academic licenses available for students)

### Running the Analysis
1. **Load the Jupyter Notebook**: Open `project-group-3.ipynb`
2. **Execute Cells Sequentially**: Run all code cells to reproduce results
3. **Modify Scenarios**: Adjust `ingredient_cost` values to test new scenarios
4. **Analyze Results**: Review optimization output and truck assignments

### Code Execution Example
```python
# Load and process data
df_demand = pd.read_csv("round1-day2_demand_node_data.csv")
df_truck = pd.read_csv("round1-day2_truck_node_data.csv")

# Create and solve optimization model
m = gp.Model("Burrito_Profit_UsingScaledDemand")
# ... (model setup)
m.optimize()

# Extract results
if m.status == GRB.OPTIMAL:
    print(f"Optimal Profit = {m.objVal:.2f}")
```

## Results Summary

| Scenario | Ingredient Cost | Optimal Profit | Trucks Used | Profit Change |
|----------|----------------|----------------|-------------|---------------|
| Base Case | $5.00 | $3,710.00 | 6 | Baseline |
| High Cost | $7.00 | $1,714.00 | 5 | -53.8% |
| Low Cost | $4.00 | $4,752.00 | 6 | +28.1% |
| Break-Even | $9.10 | -$50.70 | 3 | -101.4% |

## Business Insights

### Strategic Recommendations
1. **Cost Management**: Ingredient cost management is critical - each $1 increase reduces profit by approximately $1,000
2. **Location Stability**: Optimal truck locations remain consistent in profitable scenarios
3. **Break-Even Threshold**: Business becomes unprofitable when ingredient costs exceed $9.00 per burrito
4. **Operational Efficiency**: Fewer trucks operate in high-cost scenarios, concentrating service areas

### Risk Analysis
- **High Sensitivity**: 40% ingredient cost increase leads to 54% profit reduction
- **Operational Flexibility**: Model adapts truck count based on profitability constraints
- **Market Viability**: Clear cost thresholds identify sustainable operating conditions

## Technical Specifications

- **Programming Language**: Python 3.x
- **Optimization Framework**: Gurobi 12.0.1
- **Data Processing**: Pandas for CSV handling
- **Model Type**: Binary Integer Programming
- **Solution Method**: Branch-and-bound with cutting planes
- **Computational Complexity**: Solved optimally in milliseconds

## Future Enhancements

### Potential Extensions
1. **Capacity Constraints**: Add truck capacity limitations
2. **Multi-Period Planning**: Extend to dynamic scheduling
3. **Stochastic Demand**: Incorporate demand uncertainty
4. **Geographic Constraints**: Add distance-based service limitations
5. **Multi-Objective Optimization**: Balance profit with service coverage

### Advanced Analytics
- **Sensitivity Reports**: Automated scenario generation
- **Visualization Tools**: Geographic plotting of optimal solutions
- **Performance Dashboards**: Real-time optimization monitoring

## License and Usage

This project is developed for educational and research purposes. The Gurobi optimizer requires appropriate licensing for commercial use. Please ensure compliance with Gurobi's licensing terms when using this code.

## Contact Information

For questions about the implementation, optimization model, or business applications, please refer to the project documentation or create an issue in the repository.

---

**Note**: This optimization model demonstrates practical applications of operations research in business decision-making, combining mathematical modeling with real-world constraints to deliver actionable insights for strategic planning.

[1] https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/46181816/87932834-d006-45c4-9706-cf05b98bd2ba/project-group-3.ipynb
