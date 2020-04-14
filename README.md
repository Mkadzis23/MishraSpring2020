## Energy Conservation with Prediction Models in Data Transmission
Research Environment From MATLAB to Python (Jupyter Notebook)
- Collaborated with Professor Amitabh Mishra
- This repository is still work in progress :)

## Abstract
The impact of wireless technology in the healthcare system is exemplified in the form of Wireless Body area Sensor Networks (WBSNs) that deploy small sensors to monitor the conditions of patients by taking their measurements and wirelessly transmit information to a central console for analysis. These sensors are small to ensure wear-ability for the user, but that convenience comes at the cost of limiting the battery size. The longevity of the device depends on the battery, thereby limiting the maximum number of data transmissions possible. Energy demand can be reduced by dynamically selecting data to transmit instead of continual periodicity. This research proposes the use of predictive analysis models to simulate data between each selective transmission to optimize body sensor lifespans. 

In effort to expand on this, a transition of environment from MATLAB to scientific Python was pursued due to the availability of more open source libraries. With the addition of further libraries, it enables the continuation of testing on more predictive analysis models than previously possible. To ensure data fidelity, similar tests of the previous algorithms will be performed in scientific Python to baseline the general likeness or marginal differences of the models when switching between environments. The benchmarking goal of this research expansion is to find a predictive analysis model that best predicts the in-between values of each data transmission with the least error.

## Data Fidelity: MATLAB vs Python
```
##
## Here is where the data result comparisons between environments
## utilizing same dataset and predictive analysis models...
## Currently under working progress with dataset sorting
##
```

## Testing ARIMA Model
- Stationarity, Parameters and Forecast of ```samples_cu02.csv```
- Full code can be found in ```ECG Data Analysis.ipynb```

After successfully importing ```samples_cu02.csv```, I formatted the dataset so that it's easily manipulatable for following checks and partitions. Once the data was usable, I graphed the original dataset below for reference...

![Original Graph](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20original.png)

Looking at the graph we can see there are various oscillations, this is not always evident but can be visually reinforced by graphing the dataset's rolling mean and standard deviation. The reason this is important is because it helps verify the stationarity of the data, data that is stochastic and do not change when shifted in time.

### Rolling Mean:
![Mean](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Formulas/Rolling%20Mean.PNG)

### Rolling Standard Deviation:
![Std](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Formulas/Rolling%20Std.PNG)

### Stationarity Check:

![Stationarity Check](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20stationarity%20check.png)

Looking at the graph above, we can see a cyclic trend, this is expected since we are utilizing dataset sampled from an Electrocardiogram. To remove trend and stabilize the data, it's most common to difference the data by subtracting the previous datapoint with the current datapoint.

### Differencing:
![Difference](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Formulas/Difference.PNG)

![Differenced](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20difference%201.png)

### Sectioning:
Now that we have stabilized the data, it would visually be more beneficial to see the graph if we zoomed into a few cycles, in this case viewing two cycles of the data is a nice fit. Each cycle is 133 datapoints, thus 266 total points. The cycles are marked by red lines in the graph below:

![Section](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20section.png)

### Autocorrelation Graphs:
Stabilizing the data allows us to use it in a time-series, next we need to find the parameters of our time-series model. In the ARIMA model, there are three parameters, (p, d, q). 

- p = number of lag observations
- d = degree of differencing
- q = order of moving average

We already have the parameter d because earlier we differenced the dataset once to stabilize it, thus ```d = 1```. To find the parameters p and q, we can utilize:

- find p with the Partial Autocorrelation Function
- find q with the Autocorrelation Function

Using the autocorrelation graphs, the order can be estimated when the graph first crosses ```y = 0``` highlighted by the red line. The parameters resulted as ```p = 2``` and ~~q = 10-12~~. Further investigations are needed for the Autocorrelation Function- parameter q, this was later tweaked to ```q = 2``` in the final use of the model as an order of 10-12 yielded problems.

![PAF & AF](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20PAF%20%26%20AF.png)

### Forecast:
Now that we have the three parameters for our model, its now time to create the necessary partitions of our dataset into:

- dataset 1: the first 1250 datapoints that will be used for ```training```
- dataset 2: the last 1250 datapoints that will be used for ```forecasting```

For this example, we will be forecasting every other datapoint in dataset 2, this should cut the amount of datapoints transferred between the sensors by half. With a function to partition every other forecast, predicting values with ```ARIMA(2, 1, 2)```.The graph below shows the last two cycles of the forecasted and observed datasets.

![Forecast](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20forecast.png)

### Error:
In the graph above we can see that the forecast does pretty well in following the shape of the observed, this is probably helped by every other point being the actual. Some interesting parts that need look into is around ```[2330 - 2370]```, usually modeling forecasts smooth out quick jumps- but in this area it looks like it increased.

In the graph below we can see that overall the error distances are relatively small (the closer to zero the less distance) with an average error of ```0.023``` highlighted by a blue line. (The local average error is also 0.023)

![Error](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20error.png)

![ErrorBar](https://github.com/Mkadzis23/MishraSpring2020/blob/master/Graphs/ARIMA/samples_cu02%20errorBar.png)