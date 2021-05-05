# Capstone_project
# Predicting Stocks' Hidden Factors   

Note: I'm not coming at this with any experience in stock markets, and certainly not in an effort to get rich.     

For my final project in this Data SCIENCE course I’ve done an experiment with data and data science methods. 
SVD, is a procedure which decomposes 1 matrix into 2 smaller matrices.     
For example, a table of stock values with the companies along the top and dates on the side, can be split into:    
1 table with dates on the side and, say, 100 columns and 1 table with the companies along the top and 100 rows
such that, through matrix multiplication, they can recombine to create a table as close as possible to the original.    

But why?    
Well now you have each date described by 100 values, every day has these 100 properties
(and each company is described by their 100 values -or how they interact with the properties of each day)    

Stock prices are extremely chaotic. But surely they aren’t entirely disconnected from the world?     
And there are things in the world, which should affect stock prices, that have predictable patterns, even if it’s just the world going around the sun. 
I am trying to find some predictability, certainly not in all, but hopefully in some of these factors which Singular Value Decomposition exposes to us

## Data
I used the Alpha Vantage API to get data for over 2500 stocks, on the New York Stock Exchange, over the last 20 years    
1 issue I found was that, of course, not all companies on the New York Stock Exchange have been there since 1999. 
Therefore I changed the range I was looking at to 2004 -2018, which gave me over 2100 stocks with over 100 datapoints. 
As a professional API, providing professional data, the data was reliable and reliably formatted.    

The API had a limit of 500 stocks per day.   
I used this data to find the change in each stock’s midpoint value for each day.    

## SVD
![SVD](https://github.com/Maltanno/Capstone_project/blob/main/SVD.png)     
At first I found the outliers were broadening the range of values too far, 
so I removed the highest and lowest 5% of values from the SVD input. 
I also tried this using the stocks’ relative changes instead of absolute, but the results weren’t as good.    

## EDA
![EDA](https://github.com/Maltanno/Capstone_project/blob/main/EDA.png)    
Not too much to glean but-     
The first graph looks like it could have some seasonality, lending it some predictability     
Whereas the graphs on the right seem more chaotic

## LSTM
 I performed hyperparameter tuning, but adopted the model from the Comparison Notebook, with more time I'd tune this model further.    

## Results
![RMSEs](https://github.com/Maltanno/Capstone_project/blob/main/RMSEs.png)    
This graph shows the RMSEs achieved for each factor by both the LSTM model and a mean-baseline.    
Although the data may fit more readily to being presented in a bar chart, 
with this many points it’s actually much clearer as a line graph.   
We can see that in most cases the blue line is below the orange, showing that the model performs better, though only very slightly, than the mean-baseline:    
The mean-baseline is the result if we used the average of the previous 90 days as the prediction.    

After making a prediction for a single day ahead I went on to look further into the future    

## Comparison
![Comp1](https://github.com/Maltanno/Capstone_project/blob/main/Comp1.png)    
I also needed something to compare this hidden factor method against:    
Training an LSTM directly on the stock values.   
Though doing all 2500 would be far too much, I chose 15 stocks and performed the prediction on them.    

As when you calculate RMSE the result is of the same scale as the original data:    
Not only can’t you compare results between the stocks and the factors, 
you can’t compare results between different stocks or between different factors.    

So I tried a couple of methods to compare them:    
Firstly, dividing each stock and factor’s RMSE by its standard deviation.    
In the top left are the LSTM results for all factors but,as it’s a bit crowded, below it are the 26 most accurately predicted factors. 
Again, the lower on the graph it is the less error there is and so it is more accurate. 
As expected, the further into the future we try to predict, the accuracy decreases

The right graph shows the results from predicting the stocks directly. 
Although some seem to be predicted quite well, the blue stock at the bottom shows that outliers can increase the standard deviation enough to improve the result undeservedly.   


![Comp2](https://github.com/Maltanno/Capstone_project/blob/main/Comp2.png)   
I also compared how well the model performed against the mean-baseline.   
In the top left we can see, at least, that most of the results are below 1 -meaning that the model performed better than the baseline.    
However, although some of the factor predictions performed better than the stock predictions:    
-the benefit gained using this method is only slight   

## Recommended Future Improvements
I think the first area to make improvements is with the Singular Value Decomposition.    
For one thing I’d like to try SVD++, I didn’t use it here, though, as it can take much longer to run than SVD.    
The SVD could also be improved by adding more factors, but again each factor would be another LSTM to run and make the process slower.   

Changes could also be made to the LSTM like including more data such as the volume and the absolute price.    

But I think there’s a more fundamental issue:    
My method makes an inherent assumption that the way companies interact with these Date-properties is static.   
-Whereas that is unlikely to be the case: companies change over time. 
But fortunately, they tend to change slowly.   
So I suggest calculating the stock matrix with a 6-month rolling window, but keeping the dates matrix fixed from the SVD for all days.    
This will show you how the companies change over time -How they react differently to the same stimuli. 
This can go toward predicting how the companies will change and also be fed back, used to produce a better day matrix.   


## Rebuild
I also use the predicted Dates matrix to make new predictions with the SVD and test these.
### Rebuild Results
Starts with:    
RMSE: 0.228    
against: 
actual std 0.268   

## Additional KNN, PCA
Additionally, I performed KNN on the Dates matrix -without, then with, PCA; to make predictions.    
To be expected the results were very poor as it only looked one day into the past.    
Might there be a way to find similar paths in k-space, rather than just points?


