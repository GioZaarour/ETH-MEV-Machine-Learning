# Modelling Ethereum MEV

This project utilizes machine learning to model realized Maximal-Extractable-Value (MEV) rewards in the proof-of-stake Ethereum blockchain, utilizing exogenous network variables. 

## Data Extraction & Feature Engineering

The data extraction notebook retrieves the most recent Flashbots block data from their [MEV blocks API](https://docs.flashbots.net/flashbots-data/blockapi). 

The data I ended up using was from the [Flashbots transparency dashboard](https://transparency.flashbots.net/) -- specifically the 600 thousand blocks from Q1 2023 and their relevant MEV data.

The block data includes useful fields such as the block proposer's ETH reward, whether the validator used the MEV-boost Flashbots relay, the block number, the Ethereum network gas price, and more.

We define **realized extractable value** as the difference between the block proposer's ETH balance before and after the block execution.

The feature engineering notebook cleans unnecessary data and creates new derived and aggregated predictors from the previous data. 

## Models Used

I tested various models including the following:

- ARIMA (autoregressive integrated moving average)
- Multi-Layer-Perceptron (MLP) Neural Network
- Random Forest Regressor
- Decision Tree Regressor
- XGBoost Gradient Boosting Tree Regressor

I found that the MLP had the lowest Mean-Squared-Error, while XGBoost and Random Forest came 2nd. 

In previous MEV studies I looked at, ensemble gradient boosting methods did the best alongside the ARIMA time-series-based model. ARIMA didn't do that well for me, but my implementations are not perfect and I had trouble figuring out if the 12-second block time in Ethereum would be a problem for the time frequency expectations of ARIMA. 

Another side note is that I expect the accuracy scores and error rates would be much better if outlier blocks (those with extremely high MEV rewards) were removed from the training data. These instances are one-time pure-profit opportunities, and deviate from any sort of predictable pattern. See analysis notes below. 

## Data Analysis

Correlations with realized MEV are as follows:

```
total_gas_fees                        0.087883
arbitrages_count                      0.083154
base_fee_per_gas_in_eth               0.066455
liquidations_count                    0.049498
gas_used                              0.044201
block_fullness                        0.044201
sandwiches_count                      0.026433
network_demand                        0.012891
sandwiched_swaps_count                0.008363
base_fee_change_percentage            0.007530
validator_is_registered_with_relay   -0.007486
is_fb_builder                        -0.007606
```

I found that certain hours of the day experience higher MEV rewards, likely due to netwark demand and chain activity. 

A large majority of blocks observe an MEV of under 1 ETH, but there are many significant outliers that have extremely high rewards, ranging in the hundreds of ETH. These instances are likely a result of transaction frontrunning of exposed, novel pure-profit opportunities. In other words, "big wins" for MEV bots and arbitrageurs. There doesn't seem to be an observable pattern or predictable method for this type of transaction from the data provided.

The graphs and outputs are all available in the analysis notebook under "/Modelling"




