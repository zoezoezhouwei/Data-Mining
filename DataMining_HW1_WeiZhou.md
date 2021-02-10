### Problem 1

\#Statement (A) Gas stations charge more if they lack direct competition
in sight (boxplot).

    ggplot(data = gas) +
      geom_boxplot ( aes(x = Competitors, y = Price ))

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-1-1.png)
We want to see if gas stations charge more if they lack direct
competition in sight. From the graph, we can see the statement is
supported, since the median price for no-competitor group is higher than
the group with competitors

# Statement (B) The richer the area, the higher the gas price (scatter plot).

    ggplot (data = gas) +
      geom_point ( mapping = aes(x= Income, y = Price ))

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-2-1.png)
We want to observe whether it is true that the richer the area, the
higher the gas price. From the result, we can see the statement is not
supported by the data. In the area with lowest income, the price is
higher than many other areas with higher incomes.

# Statement (C) Shell charges more than other brands (bar plot).

    Average1 <- gas %>%
      group_by(Name) %>%
      summarize(Name_mean=mean(Price))

    ggplot(data=Average1) +
      geom_col(mapping = aes(x=Name, y=Name_mean),position='dodge',color="pink") +
      coord_flip()

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-3-1.png)
We want to see if shell charges more than other brands. By comparing the
average price of different brand, we got that the statement isn’t
supported by the data. There are obviously some other brand have higher
prices, such as Phillips 66, Around the Corner Store，7-Eleven,
Conoco，Lamar Corner Store，Texaco.

# Statement（D) Gas stations at stoplights charge more (faceted histogram).

    ggplot(data=gas) +
      geom_histogram (aes(x= Price)) +
      facet_wrap(~Stoplight) 

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-4-1.png)
We want to see whether gas stations at stoplights charge more. From the
group of graphs, we can definitely tell that the stations with stoplight
have higher prices. The statement is surpported.

# Statement (E) Gas stations with direct highway access charge more (your choice of plot).

    ggplot(data = gas) +
      geom_boxplot ( aes(x = Highway, y = Price ))

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-5-1.png)
We want to see if gas stations with direct highway access charge more.
By comparing the median of the two groups of stations with or without
direct highway access, we can see the statement is supported. The
stations with highway access charge more.

### Problem 2

# Plot A: a line graph showing average bike rentals (total) versus hour of the day (hr).

    bike <-read.csv("/Users/weizhou/Documents/**2021spring course/DM/bikeshare.csv")

    bike_hr_average1 <- bike %>%
      group_by(hr) %>%
      summarize(hr_average = mean(total))

    ggplot(data=bike_hr_average1) +
      geom_line(aes(x=hr,y=hr_average)) +
      scale_x_continuous(breaks = 0:24) 

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-6-1.png)
In the graph, the x-axis is different time o’clock in one day. Y-axis
means the average count of total bike rentals in that hour, including
both casual and registered users. The graph describes the distribution
of average bike retal quantities at different times in one day. We can
see that the first rental peak is at 5:00pm, and the second peak is at
8:00am. Between the two peaks, the retal quantity is relatively high
between 12:00 and 1:00pm. At around 4:00pm, there are the least retal
quantity. Therefore, from a user’s perspective, we can try to avoid
rental bikes at 8:00am or 5:00pm, because there is less available bikes
or it might be more expensive. From companies’ perspective, it’s
reasonable to differentiate prices based on different marketing demands
at different time.

# Plot B: a faceted line graph showing average bike rentals versus hour of the day, faceted according to whether it is a working day (workingday).

    bike_hr_average2 <- bike %>%
      group_by(hr,workingday) %>%
      summarize(hr_average = mean(total))

    ## `summarise()` has grouped output by 'hr'. You can override using the `.groups` argument.

    ggplot(data=bike_hr_average2) +
      geom_line(aes(x=hr,y=hr_average)) +
      scale_x_continuous(breaks = 0:24) +
      facet_wrap(~workingday) 

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-7-1.png)
In the two graphs, the x-axis is different time o’clock in one day.
Y-axis means the average count of total bike rentals in that hour,
including both casual and registered users. The left graph is
conditional on non-working days. Tha right-hand side graph is restricted
in the working days. By comparing the two graphs, we can see that the
two peaks are obvious in workingdays, but the trend is different in
non-working days. In non-working days, the only one peak with the
highest quantity is at around 1:00pm. Accordingly, in the non-working
days, people could avoid to retal bikes at 1:00pm. Firms could design
their marketing strategy based on different trend, such as less server
support at times with lower rental amounts, or prepare more bikes at
peak times with higher price.

# Plot C: a faceted bar plot showing average ridership during the 8 AM hour by weather situation code (weathersit), faceted according to whether it is a working day or not. Note: remember you can focus on a specific subset of rows of a data set using filter, e.g.

    bike_hr_average3 <- bike %>%
      filter(hr==8) %>%
      group_by(weathersit,workingday) %>%
      summarize(hr_average = mean(total))

    ## `summarise()` has grouped output by 'weathersit'. You can override using the `.groups` argument.

    ggplot(data=bike_hr_average3) +
      geom_col(aes(x=weathersit,y=hr_average)) +
      scale_x_continuous() +
      facet_wrap(~workingday) 

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-8-1.png)
In the two graphs, the x-axis is three weather codes in one day. Here is
the explanation for the codes: 1: Clear, Few clouds, Partly cloudy,
Partly cloudy 2: Mist + Cloudy, Mist + Broken clouds, Mist + Few clouds,
Mist 3: Light Snow, Light Rain + Thunderstorm + Scattered clouds, Light
Rain + Scattered clouds) Y-axis means the average count of total bike
rentals in that hour, including both casual and registered users. When
we compare working days and non-working days, we can see that the retal
amount in working days is mugh higher. Furthermore, with in both
working-day group or non-working-day group, the rental amount is the
highest in clear weather. When the weather is mist, rental amount is
slightly lower. But in the rainy/snowy/cloudy day, the rental amount is
dramatically decreasing.

### Problem 3

For the given data, I am interested in three questions: (1) Which is the
best carrier from which we can expect less delay? (2) Which month does
the delay happen more frequently than others for different carriers? (3)
By comparing flights departing from Austin and flights arriving at
Austin, which one is more likely to delay? Is it different between
different carriers?

    abia <-read.csv("/Users/weizhou/Documents/**2021spring course/DM/ABIA.csv")

    abia<- abia %>% 
      mutate(AD = ifelse(Dest=="AUS",'D','A'))

    abia1 <- subset(abia,ArrDelay!="NA")

    d1 = abia1 %>% 
      group_by(UniqueCarrier, AD, Month) %>%
      summarise(arrd_average=mean(ArrDelay))

    ## `summarise()` has grouped output by 'UniqueCarrier', 'AD'. You can override using the `.groups` argument.

    d1

    ## # A tibble: 350 x 4
    ## # Groups:   UniqueCarrier, AD [32]
    ##    UniqueCarrier AD    Month arrd_average
    ##    <chr>         <chr> <int>        <dbl>
    ##  1 9E            A         1       6.15  
    ##  2 9E            A         2       3.79  
    ##  3 9E            A         3      14.6   
    ##  4 9E            A         4       3.43  
    ##  5 9E            A         5      -4.72  
    ##  6 9E            A         6      -2.22  
    ##  7 9E            A         7      -3.86  
    ##  8 9E            A         8      -2.92  
    ##  9 9E            A         9      -0.0923
    ## 10 9E            A        10      -2.05  
    ## # … with 340 more rows

    ggplot(data=d1) +
      geom_col(mapping = aes(x=Month, y=arrd_average, fill=AD),position='dodge') +
      scale_x_continuous(breaks = 1:12) +
      facet_wrap(~UniqueCarrier) +
      labs(title="Arrival Delay of Flights Departing From/ Arriving At Austin",
           y="Average of Delay Time (min)",
           x="Month",
           fill="Departure/Arrival")

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-9-1.png)
For statistics, we are using average deplayed arrival time to see in
which situation, the flight is more likely to delay. Based on the
results, we can see that three carriers have higher likelihood of delay,
which are EV, DL and OH, whereas F9, US, WN and XE is relatively
reliable.  
For most carriers, the delay in December is relatively higher. It might
because of Christmas Holiday, and people prefers to travel in that
month. High frequent flights tend to make trouble of delay. For most of
carriers, the flights in summer also tend to delay, but not so much
extent as it in December. It’s reasonable since students in summer break
are less than people in winter vacation. For YV, UA, XE, AA and B6, the
flights departing from Austin is more likely to delay than the flights
arriving at Austin. But for DL and EV, it looks different. In most time,
the flights arriving at Austin delays more than ones departing from
Austin. Therefore, if we choose to flight for a vacation in summer or
December, we can expect more delay than usual. In general, we can choose
carriers with less delay in total, such as US, WN or F9. But in summer
or winter break, we should avoid EV with highest average delay time
amount.

### Problem 4

# plot the data

    sclass %>% 
        filter(trim=="350"|trim=="65 AMG") %>% 
        ggplot() + 
            geom_point(mapping = aes(x = mileage, y = price, colour=trim)) 

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-11-1.png)

Subset two datasets

    sclass350 = subset(sclass, trim == '350')
    sclass65 = subset(sclass, trim == '65 AMG')

    ggplot(data = sclass350) + 
      geom_point(mapping = aes(x = mileage, y = price))  

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-12-1.png)

    ggplot(data = sclass65) + 
      geom_point(mapping = aes(x = mileage, y = price))  

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-13-1.png)

## Part 1- Group of 350

Split the data into a training and a testing set.

    sclass350_split =  initial_split(sclass350, prop=0.8)
    sclass350_train = training(sclass350_split)
    sclass350_test  = testing(sclass350_split)

# define values of k to evaluate

    count(sclass350_train)

    ##     n
    ## 1 333

    k = c(1:333)

# define KNN function for different k

    #knn1 = knnreg(price ~ mileage, data=sclass350_train, k=1)
    #rmse(knn1, sclass350_test)

    make_knn_pred = function(k=1, training, predicting){
      pred = caret::knnreg(price ~ mileage, data=sclass350_train, k=k)
      rmse(pred, sclass350_test)
    }

# get requested train RMSEs

    knn_train_rmse = sapply(k, make_knn_pred, 
                          training = sclass350_train, 
                          predicting = sclass350_training)

# get requested test RMSEs

    knn_test_rmse = sapply(k, make_knn_pred, 
                          training = sclass350_train, 
                          predicting = sclass350_test)

# determine “best” k

    best_k = k[which.min(knn_test_rmse)]
    best_k

    ## [1] 14

# find overfitting, underfitting, and “best”" k

    fit_status = ifelse(k < best_k, "Over", ifelse(k == best_k, "Best", "Under"))
    best_k

    ## [1] 14

# summarize results

    knn_results = data.frame(
      k,
      round(knn_train_rmse, 2),
      round(knn_test_rmse, 2),
      fit_status
    )
    colnames(knn_results) = c("k", "Train_RMSE", "Test_RMSE", "Fit")

# display results

    ggplot( data=knn_results)+
      geom_point(mapping = aes(x=k,y=Test_RMSE),color='blue', size=0.3)+
      scale_x_continuous(breaks=seq(from=1,to=333, by=10))

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-23-1.png)
\# When k=12 as the best can, we can plot the fitted values.

    knnop1 = knnreg(price ~ mileage, data=sclass350_train, k=best_k)
    rmse(knnop1, sclass350_test)

    ## [1] 10261.48

    sclass350_test = sclass350_test %>%
      mutate(s350_pred = predict(knnop1, sclass350_test))

    p_test = ggplot(data = sclass350_test) + 
      geom_point(mapping = aes(x = mileage, y = price), color= 'blue', alpha=0.7) 
    p_test

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-24-1.png)

    p_test + geom_line(aes(x = mileage, y = s350_pred), color='red', size=0.5)

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-24-2.png)

##### When trim is 65 AMG

## Part 2- Group of 65

Split the data into a training and a testing set.

    sclass65 = subset(sclass, trim == '65 AMG')
    sclass65_split =  initial_split(sclass65, prop=0.8)
    sclass65_train = training(sclass65_split)
    sclass65_test  = testing(sclass65_split)

# define values of k to evaluate

    count(sclass65_train)

    ##     n
    ## 1 234

    k = c(1:234)

# Define KNN functions for different k

    #knn1 = knnreg(price ~ mileage, data=sclass65_train, k=1)
    #rmse(knn1, sclass65_test)
    make_knn_pred = function(k=1, training, predicting){
      pred = caret::knnreg(price ~ mileage, data=sclass65_train, k=k)
      rmse(pred, sclass65_test)
    }

# get requested train RMSEs

    knn_train_rmse = sapply(k, make_knn_pred, 
                          training = sclass65_train, 
                          predicting = sclass65_train)

# get requested test RMSEs

    knn_test_rmse = sapply(k, make_knn_pred, 
                          training = sclass65_train, 
                          predicting = sclass65_test)

# determine “best” k

    best_k = k[which.min(knn_test_rmse)]
    best_k

    ## [1] 2

# find overfitting, underfitting, and “best”" k

    fit_status = ifelse(k < best_k, "Over", ifelse(k == best_k, "Best", "Under"))
    best_k

    ## [1] 2

# summarize results

    knn_results = data.frame(
      k,
      round(knn_train_rmse, 2),
      round(knn_test_rmse, 2),
      fit_status
    )
    colnames(knn_results) = c("k", "Train_RMSE", "Test_RMSE", "Fit?")

# display results

    ggplot( data=knn_results)+
      geom_point(mapping = aes(x=k,y=Test_RMSE),color='blue', size=0.3)+
      scale_x_continuous(breaks=seq(from=1,to=234, by=10))

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-33-1.png)

# When k= as the best can, we can plot the fitted values.

    knnop2 = knnreg(price ~ mileage, data=sclass65_train, k=best_k)
    modelr::rmse(knnop2, sclass65_test)

    ## [1] 19155.58

    sclass65_test = sclass65_test %>%
      mutate(s65_pred = predict(knnop2, sclass65_test))

    p_test = ggplot(data = sclass65_test) + 
      geom_point(mapping = aes(x = mileage, y = price), color= 'blue', alpha=0.7) 
    p_test

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-34-1.png)

    p_test + geom_line(aes(x = mileage, y = s65_pred), color='red', size=0.5)

![](DataMining_HW1_WeiZhou_files/figure-markdown_strict/unnamed-chunk-34-2.png)
Which trim yields a larger optimal value of K? Why do you think this is?
Since I randomly split the training set and test set, every time I get
different k value, for both trim. However, the trim of 350 might have a
larger optimal k, because the total sample size is bigger than trim 65
AMG. When the sample size is smaller, we need a relatively smaller
optimal. In that case, there will be more error. In addition, we also
need to consider the balance of the data. For example, when more data is
on one side, and less data is on the other side, it might generate a
different conclusion, like what we see in the situation of random
sampling process. In sum, we don’t have a exact answer to determine
which optimal k is larger. It depends on many facts, and the answer
varies accordingly.
