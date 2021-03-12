### Problem 1 Visualization

##### Visual Task - 1

    cap = mutate(cap,day_of_week = factor(day_of_week, levels=c("Mon", "Tue", "Wed","Thu", "Fri", "Sat", "Sun")),
                         month = factor(month,levels=c("Sep", "Oct","Nov")))
    hourfreq <- cap %>% 
      group_by(hour_of_day,day_of_week,month) %>%
      summarise(hourmean=mean(boarding))

    ## `summarise()` has grouped output by 'hour_of_day', 'day_of_week'. You can override using the `.groups` argument.

    ggplot(hourfreq) +
      geom_line(aes(x=hour_of_day,y=hourmean,color=month)) +
      facet_wrap(~day_of_week)

![](DM_HW2_WeiZhou_files/figure-markdown_strict/unnamed-chunk-1-1.png)

###### From the graph, we can see that the peaks are not always the same from day to day. From Monday to Friday, there is a clear peak, but in Saturday and Sunday, there is no obvious peak. This result makes sense, since people go by bus to campus on weekdays more than weekend.

###### The average value on Mondays in September are lower than other months. The reason is that the first Monday in September is labor day, most people don’t go to campus. Thus, it lowers the entire average on Monday in Sep. In Novemver there is a three-day holiday for Thanks Giving Festival, which are just Wed, Thu, Fri. This holiday lowers the average on W/TH/F in November as well.

##### Visual Task - 2

    ggplot(cap) +
      geom_point(aes(x=temperature,y=boarding,color=weekend)) +
      facet_wrap(~hour_of_day)

![](DM_HW2_WeiZhou_files/figure-markdown_strict/unnamed-chunk-2-1.png)

###### From the scatter plot, we can see when temperature is higher there are relatively more data points than lower teperature. This would be helpful for the bus company to make better strategy.

### Problem 2

    SaratogaHouses$price<-as.numeric(SaratogaHouses$price)
    SaratogaHouses$lotSize <-as.numeric(SaratogaHouses$lotSize)
    SaratogaHouses$age <-as.numeric(SaratogaHouses$age)
    SaratogaHouses$landValue <-as.numeric(SaratogaHouses$landValue)
    SaratogaHouses$livingArea <-as.numeric(SaratogaHouses$livingArea)
    SaratogaHouses$pctCollege <-as.numeric(SaratogaHouses$pctCollege)
    SaratogaHouses$bedrooms <-as.numeric(SaratogaHouses$bedrooms)
    SaratogaHouses$fireplaces <-as.numeric(SaratogaHouses$fireplaces)
    SaratogaHouses$bathrooms <-as.numeric(SaratogaHouses$bathrooms)
    SaratogaHouses$rooms <-as.numeric(SaratogaHouses$rooms)
    # In order to better compare the two models, I scale all the numeric variables at first.
    saratoga_scale = SaratogaHouses %>%
      mutate(across(where(is.double),scale))

##### Task 1: Linear Model (stepwise)

    saratoga_split = initial_split(saratoga_scale, prop = 0.8)
    saratoga_train = training(saratoga_split)
    saratoga_test = testing(saratoga_split)
    # baseline medium model with 11 main effects
    lm_medium = lm(price ~ lotSize + age + livingArea + pctCollege + bedrooms + 
                     fireplaces + bathrooms + rooms + heating + fuel + centralAir, data=saratoga_train)
    AIC(lm_medium) # 2844.872

    ## [1] 2909.667

    lm_step = step(lm_medium, 
                   scope=~(.)^2)
    getCall(lm_step)

    # Result:
    lm_best1 <- lm(formula = price ~ lotSize + age + livingArea + pctCollege + 
         bedrooms + fireplaces + bathrooms + rooms + heating + fuel + 
         centralAir + livingArea:centralAir + age:pctCollege + age:heating + 
         pctCollege:fireplaces + livingArea:fireplaces + livingArea:fuel + 
         bedrooms:bathrooms + pctCollege:fuel + bathrooms:fuel + age:centralAir + 
         rooms:heating + rooms:fuel + age:fuel + bedrooms:centralAir + 
         lotSize:fireplaces + age:bathrooms + fuel:centralAir, data = saratoga_train)
    summary(lm_best1)

    ## 
    ## Call:
    ## lm(formula = price ~ lotSize + age + livingArea + pctCollege + 
    ##     bedrooms + fireplaces + bathrooms + rooms + heating + fuel + 
    ##     centralAir + livingArea:centralAir + age:pctCollege + age:heating + 
    ##     pctCollege:fireplaces + livingArea:fireplaces + livingArea:fuel + 
    ##     bedrooms:bathrooms + pctCollege:fuel + bathrooms:fuel + age:centralAir + 
    ##     rooms:heating + rooms:fuel + age:fuel + bedrooms:centralAir + 
    ##     lotSize:fireplaces + age:bathrooms + fuel:centralAir, data = saratoga_train)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.4453 -0.3800 -0.0756  0.2756  5.1216 
    ## 
    ## Coefficients:
    ##                               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                   0.057426   0.043934   1.307 0.191404    
    ## lotSize                       0.048314   0.018406   2.625 0.008765 ** 
    ## age                          -0.147543   0.071499  -2.064 0.039250 *  
    ## livingArea                    0.731325   0.054162  13.503  < 2e-16 ***
    ## pctCollege                    0.074821   0.025481   2.936 0.003377 ** 
    ## bedrooms                     -0.162093   0.042538  -3.811 0.000145 ***
    ## fireplaces                    0.001126   0.021363   0.053 0.957968    
    ## bathrooms                     0.165203   0.033880   4.876 1.21e-06 ***
    ## rooms                         0.071498   0.035536   2.012 0.044418 *  
    ## heatinghot water/steam       -0.065466   0.057163  -1.145 0.252307    
    ## heatingelectric              -0.426622   0.328264  -1.300 0.193951    
    ## fuelelectric                  0.133569   0.330503   0.404 0.686174    
    ## fueloil                       0.198092   0.126114   1.571 0.116477    
    ## centralAirNo                 -0.090807   0.055244  -1.644 0.100460    
    ## livingArea:centralAirNo      -0.266393   0.056491  -4.716 2.66e-06 ***
    ## age:pctCollege                0.070037   0.019929   3.514 0.000456 ***
    ## age:heatinghot water/steam    0.123734   0.042707   2.897 0.003825 ** 
    ## age:heatingelectric          -0.346185   0.719737  -0.481 0.630603    
    ## pctCollege:fireplaces        -0.077132   0.019350  -3.986 7.07e-05 ***
    ## livingArea:fireplaces         0.040339   0.014171   2.847 0.004486 ** 
    ## livingArea:fuelelectric       0.070895   0.091290   0.777 0.437535    
    ## livingArea:fueloil           -0.299414   0.091329  -3.278 0.001071 ** 
    ## bedrooms:bathrooms           -0.029312   0.019525  -1.501 0.133527    
    ## pctCollege:fuelelectric      -0.128137   0.051152  -2.505 0.012361 *  
    ## pctCollege:fueloil           -0.063339   0.047124  -1.344 0.179145    
    ## bathrooms:fuelelectric       -0.160410   0.077797  -2.062 0.039410 *  
    ## bathrooms:fueloil             0.021566   0.081507   0.265 0.791368    
    ## age:centralAirNo              0.174264   0.077379   2.252 0.024478 *  
    ## rooms:heatinghot water/steam -0.132982   0.052535  -2.531 0.011476 *  
    ## rooms:heatingelectric        -0.465612   0.164715  -2.827 0.004772 ** 
    ## rooms:fuelelectric            0.391972   0.166585   2.353 0.018767 *  
    ## rooms:fueloil                 0.080384   0.079840   1.007 0.314209    
    ## age:fuelelectric              0.412429   0.720863   0.572 0.567327    
    ## age:fueloil                  -0.092192   0.050151  -1.838 0.066241 .  
    ## bedrooms:centralAirNo         0.090655   0.054310   1.669 0.095307 .  
    ## lotSize:fireplaces           -0.029896   0.014972  -1.997 0.046049 *  
    ## age:bathrooms                -0.008662   0.020084  -0.431 0.666322    
    ## fuelelectric:centralAirNo     0.077880   0.125303   0.622 0.534353    
    ## fueloil:centralAirNo         -0.296381   0.149408  -1.984 0.047493 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.652 on 1344 degrees of freedom
    ## Multiple R-squared:  0.5979, Adjusted R-squared:  0.5865 
    ## F-statistic: 52.59 on 38 and 1344 DF,  p-value: < 2.2e-16

    AIC(lm_best1)

    ## [1] 2782.29

    rmse(lm_best1,saratoga_test) 

    ## [1] 0.6016652

##### Task 2: KNN

    make_knn_pred = function(k=1, training, predicting){
      pred = caret::knnreg(price ~ lotSize + age + livingArea + pctCollege + bedrooms + 
                             fireplaces + bathrooms + rooms, data=saratoga_train, k=k)
      rmse(pred, saratoga_test)
    }
    #count(saratoga_train)
    k = c(1:300)
    knn_train_rmse = sapply(k, make_knn_pred, 
                            training = saratoga_train, 
                            predicting = saratoga_pred)
    knn_test_rmse = sapply(k, make_knn_pred, 
                           training = saratoga_train, 
                           predicting = saratoga_test)
    best_k = k[which.min(knn_test_rmse)]
    knn_best = knnreg(price ~ lotSize + age + livingArea + pctCollege + bedrooms + 
                     fireplaces + bathrooms + rooms + heating + fuel + centralAir, data=saratoga_train, k = best_k)
    rmse(knn_best, saratoga_test)  

    ## [1] 0.6151218

###### To ensure the accuracy of the comparison and the models, we scaled all of the numeric variables at first. Then, we use a stepwise method and get the best linear model with a higher AIC than medium model. Then we apply the KNN method, choose the best k, and get our best KNN model. Afterthat, we compare the two model’s out-of-sample prediction performance by calculating the RMSE of different models. Since the KNN model has a RMSE of 0.6734326, and the best linear model has RMSE of 0.6418865, therefore, we maintain that the linear model has better performance.

### Problem 3

##### Task 1 - Bar Plot

    # colnames(gc)
    d = gc %>%
      group_by(history) %>%
      summarize(pct=sum(Default=="1")/n())
    ggplot(data=d) +
      geom_col(mapping = aes(x=history,y=pct))

![](DM_HW2_WeiZhou_files/figure-markdown_strict/unnamed-chunk-10-1.png)

###### From this graph, we can see that the probability of Default is higher when the credit history is better. This result looks not reasonable. It means this sample might not be a good sample. Therefore, it is not good for predicting. It’s highly possible that it cannot represent the whole population.

    gc_glm = glm(Default~ duration + amount + installment + age + history + purpose + foreign, data=gc, family='binomial')
    summary(gc_glm)

    ## 
    ## Call:
    ## glm(formula = Default ~ duration + amount + installment + age + 
    ##     history + purpose + foreign, family = "binomial", data = gc)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.3464  -0.8050  -0.5751   1.0250   2.4767  
    ## 
    ## Coefficients:
    ##                       Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)         -7.075e-01  4.726e-01  -1.497  0.13435    
    ## duration             2.526e-02  8.100e-03   3.118  0.00182 ** 
    ## amount               9.596e-05  3.650e-05   2.629  0.00856 ** 
    ## installment          2.216e-01  7.626e-02   2.906  0.00366 ** 
    ## age                 -2.018e-02  7.224e-03  -2.794  0.00521 ** 
    ## historypoor         -1.108e+00  2.473e-01  -4.479 7.51e-06 ***
    ## historyterrible     -1.885e+00  2.822e-01  -6.679 2.41e-11 ***
    ## purposeedu           7.248e-01  3.707e-01   1.955  0.05058 .  
    ## purposegoods/repair  1.049e-01  2.573e-01   0.408  0.68346    
    ## purposenewcar        8.545e-01  2.773e-01   3.081  0.00206 ** 
    ## purposeusedcar      -7.959e-01  3.598e-01  -2.212  0.02694 *  
    ## foreigngerman       -1.265e+00  5.773e-01  -2.191  0.02849 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 1221.7  on 999  degrees of freedom
    ## Residual deviance: 1070.0  on 988  degrees of freedom
    ## AIC: 1094
    ## 
    ## Number of Fisher Scoring iterations: 4

###### Here the model shows similar results as the graph. Terrible and poor credit history imply lower probability of being default. Yes, the bank need to change sampling process. A much Larger sample or other sampling technology is needed.

### Problem 4

##### Task 1 - Model building

    hd$date=ymd(hd$arrival_date)
    hd = mutate(hd,
                year=year(date)%>%factor(),
                wday=wday(date)%>%factor(),
                month=month(date)%>%factor())
    # head(hd)
    hd_split = initial_split(hd,prop=0.8)
    hd_train = training(hd_split)
    hd_test = testing(hd_split)

###### Small liear model

    lm_small = lm( children ~ market_segment + adults + customer_type + is_repeated_guest , data=hd_train)
    summary(lm_small)

    ## 
    ## Call:
    ## lm(formula = children ~ market_segment + adults + customer_type + 
    ##     is_repeated_guest, data = hd_train)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.18792 -0.11318 -0.09429 -0.02434  1.03149 
    ## 
    ## Coefficients:
    ##                               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                  -0.020625   0.030975  -0.666 0.505513    
    ## market_segmentComplementary   0.095791   0.033624   2.849 0.004390 ** 
    ## market_segmentCorporate       0.015253   0.030362   0.502 0.615407    
    ## market_segmentDirect          0.117143   0.030121   3.889 0.000101 ***
    ## market_segmentGroups          0.006772   0.030444   0.222 0.823968    
    ## market_segmentOffline_TA/TO   0.019550   0.030109   0.649 0.516135    
    ## market_segmentOnline_TA       0.080195   0.030001   2.673 0.007519 ** 
    ## adults                        0.018896   0.003013   6.270 3.64e-10 ***
    ## customer_typeGroup           -0.018009   0.017820  -1.011 0.312196    
    ## customer_typeTransient        0.015821   0.007742   2.043 0.041024 *  
    ## customer_typeTransient-Party -0.012373   0.008234  -1.503 0.132930    
    ## is_repeated_guest            -0.043060   0.007686  -5.603 2.13e-08 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2693 on 35989 degrees of freedom
    ## Multiple R-squared:  0.0324, Adjusted R-squared:  0.0321 
    ## F-statistic: 109.5 on 11 and 35989 DF,  p-value: < 2.2e-16

###### Big linear Model

    lm_big = lm( children ~ .-arrival_date, data=hd_train)
    summary(lm_big)

    ## 
    ## Call:
    ## lm(formula = children ~ . - arrival_date, data = hd_train)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.93094 -0.08664 -0.03775  0.01221  1.08499 
    ## 
    ## Coefficients:
    ##                                      Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                        -3.883e+00  2.306e+00  -1.684 0.092268 .  
    ## hotelResort_Hotel                  -3.530e-02  3.242e-03 -10.888  < 2e-16 ***
    ## lead_time                           7.315e-05  1.776e-05   4.120 3.80e-05 ***
    ## stays_in_weekend_nights            -3.250e-03  2.151e-03  -1.511 0.130907    
    ## stays_in_week_nights                1.680e-03  1.001e-03   1.678 0.093368 .  
    ## adults                             -4.180e-02  2.845e-03 -14.695  < 2e-16 ***
    ## mealFB                              1.767e-02  1.919e-02   0.920 0.357351    
    ## mealHB                             -8.881e-03  4.164e-03  -2.133 0.032955 *  
    ## mealSC                             -5.009e-02  4.765e-03 -10.513  < 2e-16 ***
    ## mealUndefined                       8.431e-03  1.241e-02   0.679 0.496983    
    ## market_segmentComplementary         5.653e-02  3.103e-02   1.822 0.068485 .  
    ## market_segmentCorporate             3.912e-02  2.644e-02   1.480 0.139005    
    ## market_segmentDirect                4.458e-02  2.837e-02   1.571 0.116169    
    ## market_segmentGroups                5.918e-02  2.764e-02   2.141 0.032289 *  
    ## market_segmentOffline_TA/TO         6.800e-02  2.770e-02   2.455 0.014105 *  
    ## market_segmentOnline_TA             5.983e-02  2.765e-02   2.164 0.030461 *  
    ## distribution_channelDirect          1.346e-02  1.155e-02   1.165 0.243884    
    ## distribution_channelGDS            -7.457e-02  2.782e-02  -2.681 0.007343 ** 
    ## distribution_channelTA/TO          -4.578e-03  9.737e-03  -0.470 0.638257    
    ## is_repeated_guest                  -2.491e-02  7.251e-03  -3.435 0.000593 ***
    ## previous_cancellations             -1.069e-04  4.898e-03  -0.022 0.982579    
    ## previous_bookings_not_canceled     -2.572e-03  9.070e-04  -2.836 0.004571 ** 
    ## reserved_room_typeB                 1.777e-01  1.505e-02  11.806  < 2e-16 ***
    ## reserved_room_typeC                 5.337e-01  1.616e-02  33.033  < 2e-16 ***
    ## reserved_room_typeD                -6.473e-02  4.835e-03 -13.386  < 2e-16 ***
    ## reserved_room_typeE                -3.121e-02  8.673e-03  -3.599 0.000320 ***
    ## reserved_room_typeF                 2.890e-01  1.294e-02  22.337  < 2e-16 ***
    ## reserved_room_typeG                 4.257e-01  1.741e-02  24.456  < 2e-16 ***
    ## reserved_room_typeH                 5.781e-01  3.330e-02  17.359  < 2e-16 ***
    ## reserved_room_typeL                -1.121e-01  1.648e-01  -0.680 0.496338    
    ## assigned_room_typeB                 2.220e-02  1.035e-02   2.145 0.031962 *  
    ## assigned_room_typeC                 9.186e-02  9.432e-03   9.739  < 2e-16 ***
    ## assigned_room_typeD                 5.949e-02  4.210e-03  14.132  < 2e-16 ***
    ## assigned_room_typeE                 5.509e-02  7.756e-03   7.103 1.24e-12 ***
    ## assigned_room_typeF                 7.631e-02  1.111e-02   6.866 6.73e-12 ***
    ## assigned_room_typeG                 1.092e-01  1.522e-02   7.177 7.28e-13 ***
    ## assigned_room_typeH                 1.018e-01  2.859e-02   3.561 0.000370 ***
    ## assigned_room_typeI                 8.460e-02  1.928e-02   4.389 1.14e-05 ***
    ## assigned_room_typeK                 2.439e-02  1.991e-02   1.225 0.220500    
    ## booking_changes                     2.038e-02  1.708e-03  11.932  < 2e-16 ***
    ## deposit_typeNon_Refund              2.722e-02  3.507e-02   0.776 0.437636    
    ## deposit_typeRefundable              1.709e-02  2.811e-02   0.608 0.543079    
    ## days_in_waiting_list               -9.949e-06  8.688e-05  -0.115 0.908826    
    ## customer_typeGroup                 -1.121e-02  1.555e-02  -0.721 0.471188    
    ## customer_typeTransient              1.636e-03  7.002e-03   0.234 0.815300    
    ## customer_typeTransient-Party       -4.003e-02  7.504e-03  -5.335 9.64e-08 ***
    ## average_daily_rate                  9.432e-04  3.994e-05  23.615  < 2e-16 ***
    ## required_car_parking_spacesparking  1.622e-03  4.331e-03   0.375 0.707989    
    ## total_of_special_requests           3.255e-02  1.677e-03  19.408  < 2e-16 ***
    ## date                                2.352e-04  1.402e-04   1.678 0.093384 .  
    ## year2016                           -8.309e-02  5.144e-02  -1.615 0.106260    
    ## year2017                           -1.850e-01  1.025e-01  -1.804 0.071172 .  
    ## wday2                              -8.939e-03  5.051e-03  -1.770 0.076783 .  
    ## wday3                              -1.698e-02  6.513e-03  -2.607 0.009129 ** 
    ## wday4                              -1.728e-02  6.329e-03  -2.730 0.006342 ** 
    ## wday5                              -1.747e-02  5.910e-03  -2.955 0.003127 ** 
    ## wday6                              -1.846e-02  5.409e-03  -3.414 0.000641 ***
    ## wday7                               3.485e-03  4.913e-03   0.709 0.478212    
    ## month2                              1.596e-02  8.168e-03   1.954 0.050735 .  
    ## month3                             -1.920e-02  1.069e-02  -1.795 0.072658 .  
    ## month4                             -2.722e-02  1.433e-02  -1.900 0.057444 .  
    ## month5                             -6.544e-02  1.813e-02  -3.609 0.000307 ***
    ## month6                             -6.895e-02  2.223e-02  -3.102 0.001921 ** 
    ## month7                             -2.916e-02  2.629e-02  -1.109 0.267396    
    ## month8                             -4.184e-02  3.049e-02  -1.372 0.170038    
    ## month9                             -1.079e-01  3.478e-02  -3.102 0.001923 ** 
    ## month10                            -9.097e-02  3.888e-02  -2.340 0.019306 *  
    ## month11                            -9.899e-02  4.318e-02  -2.293 0.021877 *  
    ## month12                            -7.348e-02  4.754e-02  -1.546 0.122166    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2328 on 35932 degrees of freedom
    ## Multiple R-squared:  0.2784, Adjusted R-squared:  0.277 
    ## F-statistic: 203.9 on 68 and 35932 DF,  p-value: < 2.2e-16

###### Stepwise Selection

    # Stepwise selection: we start with a reasonable guess
    lm_step = step(lm_small, 
                   scope=~(hotel+adults+meal+market_segment
                           +distribution_channel+is_repeated_guest+reserved_room_type+assigned_room_type
                           +customer_type+required_car_parking_spaces+total_of_special_requests+wday+month)^2)
    # what variables are included?
    getCall(lm_step)

    lm_best2 <- lm(children ~ market_segment + adults + customer_type + is_repeated_guest +
                    reserved_room_type + total_of_special_requests + month +
                    assigned_room_type + market_segment:reserved_room_type +
                    adults:reserved_room_type + reserved_room_type:month, data=hd)
    summary(lm_best2)

    ## 
    ## Call:
    ## lm(formula = children ~ market_segment + adults + customer_type + 
    ##     is_repeated_guest + reserved_room_type + total_of_special_requests + 
    ##     month + assigned_room_type + market_segment:reserved_room_type + 
    ##     adults:reserved_room_type + reserved_room_type:month, data = hd)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.13478 -0.07701 -0.03856 -0.00028  1.08859 
    ## 
    ## Coefficients: (25 not defined because of singularities)
    ##                                                   Estimate Std. Error t value
    ## (Intercept)                                     -0.0253436  0.0308999  -0.820
    ## market_segmentComplementary                     -0.0062593  0.0332584  -0.188
    ## market_segmentCorporate                         -0.0078298  0.0300465  -0.261
    ## market_segmentDirect                             0.0190220  0.0300031   0.634
    ## market_segmentGroups                            -0.0055844  0.0300946  -0.186
    ## market_segmentOffline_TA/TO                     -0.0003520  0.0299152  -0.012
    ## market_segmentOnline_TA                          0.0017879  0.0298676   0.060
    ## adults                                           0.0022596  0.0029092   0.777
    ## customer_typeGroup                              -0.0016631  0.0138882  -0.120
    ## customer_typeTransient                           0.0263686  0.0060335   4.370
    ## customer_typeTransient-Party                     0.0040864  0.0064596   0.633
    ## is_repeated_guest                               -0.0470849  0.0058462  -8.054
    ## reserved_room_typeB                              0.8744628  0.0487027  17.955
    ## reserved_room_typeC                              1.1527441  0.1032050  11.169
    ## reserved_room_typeD                              0.0013648  0.0461625   0.030
    ## reserved_room_typeE                             -0.0919745  0.2309686  -0.398
    ## reserved_room_typeF                              0.8236698  0.0510448  16.136
    ## reserved_room_typeG                              0.6920352  0.0548485  12.617
    ## reserved_room_typeH                              0.8539910  0.1023227   8.346
    ## reserved_room_typeL                             -0.0112927  0.5087608  -0.022
    ## total_of_special_requests                        0.0368443  0.0014500  25.410
    ## month2                                           0.0169337  0.0070202   2.412
    ## month3                                           0.0023858  0.0067250   0.355
    ## month4                                           0.0138924  0.0068553   2.027
    ## month5                                          -0.0001828  0.0067483  -0.027
    ## month6                                           0.0057994  0.0069220   0.838
    ## month7                                           0.0535338  0.0067483   7.933
    ## month8                                           0.0556457  0.0066621   8.353
    ## month9                                          -0.0034595  0.0068444  -0.505
    ## month10                                          0.0009881  0.0066829   0.148
    ## month11                                         -0.0112711  0.0071230  -1.582
    ## month12                                          0.0044849  0.0074574   0.601
    ## assigned_room_typeB                              0.0299404  0.0089123   3.359
    ## assigned_room_typeC                              0.0699377  0.0079951   8.748
    ## assigned_room_typeD                              0.0500603  0.0036186  13.834
    ## assigned_room_typeE                              0.0414985  0.0067038   6.190
    ## assigned_room_typeF                              0.0601763  0.0096306   6.248
    ## assigned_room_typeG                              0.0930326  0.0133567   6.965
    ## assigned_room_typeH                              0.0307216  0.0246245   1.248
    ## assigned_room_typeI                              0.0157102  0.0159428   0.985
    ## assigned_room_typeK                              0.0122328  0.0179642   0.681
    ## market_segmentComplementary:reserved_room_typeB  0.1369818  0.0644508   2.125
    ## market_segmentCorporate:reserved_room_typeB      0.2518079  0.1383142   1.821
    ## market_segmentDirect:reserved_room_typeB         0.0004879  0.0262537   0.019
    ## market_segmentGroups:reserved_room_typeB         0.1258620  0.1179421   1.067
    ## market_segmentOffline_TA/TO:reserved_room_typeB -0.1075533  0.0518641  -2.074
    ## market_segmentOnline_TA:reserved_room_typeB             NA         NA      NA
    ## market_segmentComplementary:reserved_room_typeC -0.8120931  0.1229224  -6.607
    ## market_segmentCorporate:reserved_room_typeC      0.2818278  0.0992132   2.841
    ## market_segmentDirect:reserved_room_typeC         0.0355414  0.0265944   1.336
    ## market_segmentGroups:reserved_room_typeC         0.0022249  0.0787896   0.028
    ## market_segmentOffline_TA/TO:reserved_room_typeC -0.0620735  0.0455857  -1.362
    ## market_segmentOnline_TA:reserved_room_typeC             NA         NA      NA
    ## market_segmentComplementary:reserved_room_typeD  0.0478428  0.0578797   0.827
    ## market_segmentCorporate:reserved_room_typeD      0.0274783  0.0482688   0.569
    ## market_segmentDirect:reserved_room_typeD         0.0789045  0.0444110   1.777
    ## market_segmentGroups:reserved_room_typeD         0.0406988  0.0457123   0.890
    ## market_segmentOffline_TA/TO:reserved_room_typeD  0.0661304  0.0442028   1.496
    ## market_segmentOnline_TA:reserved_room_typeD      0.0331075  0.0439946   0.753
    ## market_segmentComplementary:reserved_room_typeE  0.0995471  0.2345490   0.424
    ## market_segmentCorporate:reserved_room_typeE      0.0104078  0.2309281   0.045
    ## market_segmentDirect:reserved_room_typeE         0.1085289  0.2301330   0.472
    ## market_segmentGroups:reserved_room_typeE         0.0157682  0.2306816   0.068
    ## market_segmentOffline_TA/TO:reserved_room_typeE  0.0153869  0.2301959   0.067
    ## market_segmentOnline_TA:reserved_room_typeE     -0.0223570  0.2300463  -0.097
    ## market_segmentComplementary:reserved_room_typeF -0.4305937  0.0488151  -8.821
    ## market_segmentCorporate:reserved_room_typeF     -0.3461607  0.1323694  -2.615
    ## market_segmentDirect:reserved_room_typeF        -0.5126944  0.0147465 -34.767
    ## market_segmentGroups:reserved_room_typeF        -0.3523210  0.0962214  -3.662
    ## market_segmentOffline_TA/TO:reserved_room_typeF -0.3855921  0.0438264  -8.798
    ## market_segmentOnline_TA:reserved_room_typeF             NA         NA      NA
    ## market_segmentComplementary:reserved_room_typeG -0.5281739  0.0426134 -12.395
    ## market_segmentCorporate:reserved_room_typeG     -0.5878669  0.0887192  -6.626
    ## market_segmentDirect:reserved_room_typeG        -0.1144038  0.0183400  -6.238
    ## market_segmentGroups:reserved_room_typeG        -0.5405747  0.0688898  -7.847
    ## market_segmentOffline_TA/TO:reserved_room_typeG  0.0739988  0.0673255   1.099
    ## market_segmentOnline_TA:reserved_room_typeG             NA         NA      NA
    ## market_segmentComplementary:reserved_room_typeH -0.9002447  0.2350566  -3.830
    ## market_segmentCorporate:reserved_room_typeH             NA         NA      NA
    ## market_segmentDirect:reserved_room_typeH        -0.3859212  0.0360453 -10.707
    ## market_segmentGroups:reserved_room_typeH                NA         NA      NA
    ## market_segmentOffline_TA/TO:reserved_room_typeH         NA         NA      NA
    ## market_segmentOnline_TA:reserved_room_typeH             NA         NA      NA
    ## market_segmentComplementary:reserved_room_typeL         NA         NA      NA
    ## market_segmentCorporate:reserved_room_typeL             NA         NA      NA
    ## market_segmentDirect:reserved_room_typeL                NA         NA      NA
    ## market_segmentGroups:reserved_room_typeL                NA         NA      NA
    ## market_segmentOffline_TA/TO:reserved_room_typeL         NA         NA      NA
    ## market_segmentOnline_TA:reserved_room_typeL             NA         NA      NA
    ## adults:reserved_room_typeB                      -0.4150071  0.0146270 -28.373
    ## adults:reserved_room_typeC                      -0.2740539  0.0297768  -9.204
    ## adults:reserved_room_typeD                      -0.0508952  0.0061004  -8.343
    ## adults:reserved_room_typeE                       0.0130920  0.0110623   1.183
    ## adults:reserved_room_typeF                      -0.1327357  0.0204932  -6.477
    ## adults:reserved_room_typeG                      -0.0932301  0.0185674  -5.021
    ## adults:reserved_room_typeH                      -0.0067475  0.0298433  -0.226
    ## adults:reserved_room_typeL                      -0.0645477  0.3218379  -0.201
    ## reserved_room_typeB:month2                       0.0251938  0.0550316   0.458
    ## reserved_room_typeC:month2                       0.2273863  0.0913887   2.488
    ## reserved_room_typeD:month2                       0.0201969  0.0187460   1.077
    ## reserved_room_typeE:month2                      -0.0056789  0.0260276  -0.218
    ## reserved_room_typeF:month2                      -0.0220105  0.0408588  -0.539
    ## reserved_room_typeG:month2                       0.0743263  0.0483569   1.537
    ## reserved_room_typeH:month2                      -0.1171529  0.0853370  -1.373
    ## reserved_room_typeL:month2                              NA         NA      NA
    ## reserved_room_typeB:month3                      -0.0185656  0.0623916  -0.298
    ## reserved_room_typeC:month3                      -0.2357651  0.0891952  -2.643
    ## reserved_room_typeD:month3                       0.0229498  0.0177561   1.293
    ## reserved_room_typeE:month3                       0.0226528  0.0249514   0.908
    ## reserved_room_typeF:month3                      -0.0327350  0.0399199  -0.820
    ## reserved_room_typeG:month3                       0.0128421  0.0486127   0.264
    ## reserved_room_typeH:month3                      -0.0436954  0.0971779  -0.450
    ## reserved_room_typeL:month3                              NA         NA      NA
    ## reserved_room_typeB:month4                      -0.0366783  0.0586668  -0.625
    ## reserved_room_typeC:month4                       0.0902923  0.0920425   0.981
    ## reserved_room_typeD:month4                       0.0076146  0.0176119   0.432
    ## reserved_room_typeE:month4                       0.0350964  0.0244934   1.433
    ## reserved_room_typeF:month4                       0.0533046  0.0375755   1.419
    ## reserved_room_typeG:month4                      -0.0009194  0.0466991  -0.020
    ## reserved_room_typeH:month4                       0.1375614  0.0897308   1.533
    ## reserved_room_typeL:month4                              NA         NA      NA
    ## reserved_room_typeB:month5                       0.0477294  0.0690043   0.692
    ## reserved_room_typeC:month5                      -0.1494200  0.0905220  -1.651
    ## reserved_room_typeD:month5                       0.0035961  0.0172403   0.209
    ## reserved_room_typeE:month5                       0.0342822  0.0242251   1.415
    ## reserved_room_typeF:month5                      -0.0881769  0.0383905  -2.297
    ## reserved_room_typeG:month5                      -0.1141172  0.0458944  -2.487
    ## reserved_room_typeH:month5                      -0.1754875  0.0868028  -2.022
    ## reserved_room_typeL:month5                              NA         NA      NA
    ## reserved_room_typeB:month6                      -0.0930781  0.0680266  -1.368
    ## reserved_room_typeC:month6                      -0.2466762  0.0829790  -2.973
    ## reserved_room_typeD:month6                       0.0062544  0.0176539   0.354
    ## reserved_room_typeE:month6                       0.0288913  0.0253927   1.138
    ## reserved_room_typeF:month6                      -0.0319505  0.0391492  -0.816
    ## reserved_room_typeG:month6                       0.0204166  0.0483230   0.423
    ## reserved_room_typeH:month6                       0.0124955  0.0785203   0.159
    ## reserved_room_typeL:month6                              NA         NA      NA
    ## reserved_room_typeB:month7                      -0.0258983  0.0496746  -0.521
    ## reserved_room_typeC:month7                       0.0607307  0.0774992   0.784
    ## reserved_room_typeD:month7                      -0.0019476  0.0171799  -0.113
    ## reserved_room_typeE:month7                       0.0906397  0.0233821   3.876
    ## reserved_room_typeF:month7                       0.0494126  0.0355582   1.390
    ## reserved_room_typeG:month7                       0.1918160  0.0433718   4.423
    ## reserved_room_typeH:month7                      -0.0516601  0.0748602  -0.690
    ## reserved_room_typeL:month7                              NA         NA      NA
    ## reserved_room_typeB:month8                      -0.0512972  0.0491577  -1.044
    ## reserved_room_typeC:month8                       0.0221967  0.0766418   0.290
    ## reserved_room_typeD:month8                       0.0170276  0.0169772   1.003
    ## reserved_room_typeE:month8                       0.0406735  0.0235355   1.728
    ## reserved_room_typeF:month8                       0.1068533  0.0351075   3.044
    ## reserved_room_typeG:month8                       0.1389743  0.0431073   3.224
    ## reserved_room_typeH:month8                      -0.0731681  0.0771457  -0.948
    ## reserved_room_typeL:month8                              NA         NA      NA
    ## reserved_room_typeB:month9                       0.0346453  0.0663494   0.522
    ## reserved_room_typeC:month9                      -0.3424684  0.0922315  -3.713
    ## reserved_room_typeD:month9                       0.0038754  0.0181305   0.214
    ## reserved_room_typeE:month9                      -0.0049040  0.0256192  -0.191
    ## reserved_room_typeF:month9                       0.0071728  0.0399586   0.180
    ## reserved_room_typeG:month9                      -0.0846057  0.0480238  -1.762
    ## reserved_room_typeH:month9                      -0.0906222  0.0880253  -1.030
    ## reserved_room_typeL:month9                              NA         NA      NA
    ## reserved_room_typeB:month10                      0.0397445  0.0565793   0.702
    ## reserved_room_typeC:month10                     -0.0995428  0.0985823  -1.010
    ## reserved_room_typeD:month10                      0.0031196  0.0179191   0.174
    ## reserved_room_typeE:month10                      0.0140259  0.0256656   0.546
    ## reserved_room_typeF:month10                     -0.0563067  0.0388023  -1.451
    ## reserved_room_typeG:month10                     -0.0093876  0.0493615  -0.190
    ## reserved_room_typeH:month10                      0.0216547  0.0996186   0.217
    ## reserved_room_typeL:month10                             NA         NA      NA
    ## reserved_room_typeB:month11                      0.0537887  0.0695614   0.773
    ## reserved_room_typeC:month11                      0.1395787  0.1054572   1.324
    ## reserved_room_typeD:month11                     -0.0022826  0.0194988  -0.117
    ## reserved_room_typeE:month11                      0.0092901  0.0279685   0.332
    ## reserved_room_typeF:month11                     -0.0162270  0.0434407  -0.374
    ## reserved_room_typeG:month11                     -0.0753283  0.0575739  -1.308
    ## reserved_room_typeH:month11                     -0.1050961  0.0901481  -1.166
    ## reserved_room_typeL:month11                             NA         NA      NA
    ## reserved_room_typeB:month12                      0.0055447  0.0529803   0.105
    ## reserved_room_typeC:month12                      0.1533325  0.0923634   1.660
    ## reserved_room_typeD:month12                      0.0419372  0.0193437   2.168
    ## reserved_room_typeE:month12                      0.0070763  0.0265511   0.267
    ## reserved_room_typeF:month12                      0.0665461  0.0406982   1.635
    ## reserved_room_typeG:month12                      0.1562732  0.0512745   3.048
    ## reserved_room_typeH:month12                      0.1204728  0.0844622   1.426
    ## reserved_room_typeL:month12                             NA         NA      NA
    ##                                                 Pr(>|t|)    
    ## (Intercept)                                     0.412116    
    ## market_segmentComplementary                     0.850720    
    ## market_segmentCorporate                         0.794410    
    ## market_segmentDirect                            0.526082    
    ## market_segmentGroups                            0.852790    
    ## market_segmentOffline_TA/TO                     0.990613    
    ## market_segmentOnline_TA                         0.952267    
    ## adults                                          0.437340    
    ## customer_typeGroup                              0.904683    
    ## customer_typeTransient                          1.24e-05 ***
    ## customer_typeTransient-Party                    0.526994    
    ## is_repeated_guest                               8.22e-16 ***
    ## reserved_room_typeB                              < 2e-16 ***
    ## reserved_room_typeC                              < 2e-16 ***
    ## reserved_room_typeD                             0.976414    
    ## reserved_room_typeE                             0.690476    
    ## reserved_room_typeF                              < 2e-16 ***
    ## reserved_room_typeG                              < 2e-16 ***
    ## reserved_room_typeH                              < 2e-16 ***
    ## reserved_room_typeL                             0.982291    
    ## total_of_special_requests                        < 2e-16 ***
    ## month2                                          0.015863 *  
    ## month3                                          0.722762    
    ## month4                                          0.042716 *  
    ## month5                                          0.978389    
    ## month6                                          0.402137    
    ## month7                                          2.19e-15 ***
    ## month8                                           < 2e-16 ***
    ## month9                                          0.613252    
    ## month10                                         0.882456    
    ## month11                                         0.113573    
    ## month12                                         0.547577    
    ## assigned_room_typeB                             0.000782 ***
    ## assigned_room_typeC                              < 2e-16 ***
    ## assigned_room_typeD                              < 2e-16 ***
    ## assigned_room_typeE                             6.06e-10 ***
    ## assigned_room_typeF                             4.18e-10 ***
    ## assigned_room_typeG                             3.32e-12 ***
    ## assigned_room_typeH                             0.212184    
    ## assigned_room_typeI                             0.324426    
    ## assigned_room_typeK                             0.495904    
    ## market_segmentComplementary:reserved_room_typeB 0.033561 *  
    ## market_segmentCorporate:reserved_room_typeB     0.068682 .  
    ## market_segmentDirect:reserved_room_typeB        0.985172    
    ## market_segmentGroups:reserved_room_typeB        0.285909    
    ## market_segmentOffline_TA/TO:reserved_room_typeB 0.038108 *  
    ## market_segmentOnline_TA:reserved_room_typeB           NA    
    ## market_segmentComplementary:reserved_room_typeC 3.98e-11 ***
    ## market_segmentCorporate:reserved_room_typeC     0.004505 ** 
    ## market_segmentDirect:reserved_room_typeC        0.181417    
    ## market_segmentGroups:reserved_room_typeC        0.977472    
    ## market_segmentOffline_TA/TO:reserved_room_typeC 0.173303    
    ## market_segmentOnline_TA:reserved_room_typeC           NA    
    ## market_segmentComplementary:reserved_room_typeD 0.408474    
    ## market_segmentCorporate:reserved_room_typeD     0.569172    
    ## market_segmentDirect:reserved_room_typeD        0.075626 .  
    ## market_segmentGroups:reserved_room_typeD        0.373296    
    ## market_segmentOffline_TA/TO:reserved_room_typeD 0.134643    
    ## market_segmentOnline_TA:reserved_room_typeD     0.451733    
    ## market_segmentComplementary:reserved_room_typeE 0.671262    
    ## market_segmentCorporate:reserved_room_typeE     0.964052    
    ## market_segmentDirect:reserved_room_typeE        0.637220    
    ## market_segmentGroups:reserved_room_typeE        0.945503    
    ## market_segmentOffline_TA/TO:reserved_room_typeE 0.946707    
    ## market_segmentOnline_TA:reserved_room_typeE     0.922580    
    ## market_segmentComplementary:reserved_room_typeF  < 2e-16 ***
    ## market_segmentCorporate:reserved_room_typeF     0.008923 ** 
    ## market_segmentDirect:reserved_room_typeF         < 2e-16 ***
    ## market_segmentGroups:reserved_room_typeF        0.000251 ***
    ## market_segmentOffline_TA/TO:reserved_room_typeF  < 2e-16 ***
    ## market_segmentOnline_TA:reserved_room_typeF           NA    
    ## market_segmentComplementary:reserved_room_typeG  < 2e-16 ***
    ## market_segmentCorporate:reserved_room_typeG     3.48e-11 ***
    ## market_segmentDirect:reserved_room_typeG        4.47e-10 ***
    ## market_segmentGroups:reserved_room_typeG        4.36e-15 ***
    ## market_segmentOffline_TA/TO:reserved_room_typeG 0.271722    
    ## market_segmentOnline_TA:reserved_room_typeG           NA    
    ## market_segmentComplementary:reserved_room_typeH 0.000128 ***
    ## market_segmentCorporate:reserved_room_typeH           NA    
    ## market_segmentDirect:reserved_room_typeH         < 2e-16 ***
    ## market_segmentGroups:reserved_room_typeH              NA    
    ## market_segmentOffline_TA/TO:reserved_room_typeH       NA    
    ## market_segmentOnline_TA:reserved_room_typeH           NA    
    ## market_segmentComplementary:reserved_room_typeL       NA    
    ## market_segmentCorporate:reserved_room_typeL           NA    
    ## market_segmentDirect:reserved_room_typeL              NA    
    ## market_segmentGroups:reserved_room_typeL              NA    
    ## market_segmentOffline_TA/TO:reserved_room_typeL       NA    
    ## market_segmentOnline_TA:reserved_room_typeL           NA    
    ## adults:reserved_room_typeB                       < 2e-16 ***
    ## adults:reserved_room_typeC                       < 2e-16 ***
    ## adults:reserved_room_typeD                       < 2e-16 ***
    ## adults:reserved_room_typeE                      0.236625    
    ## adults:reserved_room_typeF                      9.45e-11 ***
    ## adults:reserved_room_typeG                      5.16e-07 ***
    ## adults:reserved_room_typeH                      0.821127    
    ## adults:reserved_room_typeL                      0.841044    
    ## reserved_room_typeB:month2                      0.647094    
    ## reserved_room_typeC:month2                      0.012846 *  
    ## reserved_room_typeD:month2                      0.281309    
    ## reserved_room_typeE:month2                      0.827285    
    ## reserved_room_typeF:month2                      0.590098    
    ## reserved_room_typeG:month2                      0.124292    
    ## reserved_room_typeH:month2                      0.169813    
    ## reserved_room_typeL:month2                            NA    
    ## reserved_room_typeB:month3                      0.766036    
    ## reserved_room_typeC:month3                      0.008214 ** 
    ## reserved_room_typeD:month3                      0.196189    
    ## reserved_room_typeE:month3                      0.363949    
    ## reserved_room_typeF:month3                      0.412210    
    ## reserved_room_typeG:month3                      0.791649    
    ## reserved_room_typeH:month3                      0.652970    
    ## reserved_room_typeL:month3                            NA    
    ## reserved_room_typeB:month4                      0.531845    
    ## reserved_room_typeC:month4                      0.326605    
    ## reserved_room_typeD:month4                      0.665483    
    ## reserved_room_typeE:month4                      0.151896    
    ## reserved_room_typeF:month4                      0.156022    
    ## reserved_room_typeG:month4                      0.984292    
    ## reserved_room_typeH:month4                      0.125272    
    ## reserved_room_typeL:month4                            NA    
    ## reserved_room_typeB:month5                      0.489137    
    ## reserved_room_typeC:month5                      0.098817 .  
    ## reserved_room_typeD:month5                      0.834771    
    ## reserved_room_typeE:month5                      0.157031    
    ## reserved_room_typeF:month5                      0.021632 *  
    ## reserved_room_typeG:month5                      0.012904 *  
    ## reserved_room_typeH:month5                      0.043215 *  
    ## reserved_room_typeL:month5                            NA    
    ## reserved_room_typeB:month6                      0.171238    
    ## reserved_room_typeC:month6                      0.002953 ** 
    ## reserved_room_typeD:month6                      0.723131    
    ## reserved_room_typeE:month6                      0.255218    
    ## reserved_room_typeF:month6                      0.414434    
    ## reserved_room_typeG:month6                      0.672660    
    ## reserved_room_typeH:month6                      0.873562    
    ## reserved_room_typeL:month6                            NA    
    ## reserved_room_typeB:month7                      0.602119    
    ## reserved_room_typeC:month7                      0.433261    
    ## reserved_room_typeD:month7                      0.909742    
    ## reserved_room_typeE:month7                      0.000106 ***
    ## reserved_room_typeF:month7                      0.164650    
    ## reserved_room_typeG:month7                      9.78e-06 ***
    ## reserved_room_typeH:month7                      0.490143    
    ## reserved_room_typeL:month7                            NA    
    ## reserved_room_typeB:month8                      0.296711    
    ## reserved_room_typeC:month8                      0.772111    
    ## reserved_room_typeD:month8                      0.315884    
    ## reserved_room_typeE:month8                      0.083964 .  
    ## reserved_room_typeF:month8                      0.002339 ** 
    ## reserved_room_typeG:month8                      0.001265 ** 
    ## reserved_room_typeH:month8                      0.342910    
    ## reserved_room_typeL:month8                            NA    
    ## reserved_room_typeB:month9                      0.601559    
    ## reserved_room_typeC:month9                      0.000205 ***
    ## reserved_room_typeD:month9                      0.830741    
    ## reserved_room_typeE:month9                      0.848197    
    ## reserved_room_typeF:month9                      0.857542    
    ## reserved_room_typeG:month9                      0.078119 .  
    ## reserved_room_typeH:month9                      0.303249    
    ## reserved_room_typeL:month9                            NA    
    ## reserved_room_typeB:month10                     0.482398    
    ## reserved_room_typeC:month10                     0.312624    
    ## reserved_room_typeD:month10                     0.861791    
    ## reserved_room_typeE:month10                     0.584736    
    ## reserved_room_typeF:month10                     0.146755    
    ## reserved_room_typeG:month10                     0.849168    
    ## reserved_room_typeH:month10                     0.827917    
    ## reserved_room_typeL:month10                           NA    
    ## reserved_room_typeB:month11                     0.439375    
    ## reserved_room_typeC:month11                     0.185657    
    ## reserved_room_typeD:month11                     0.906811    
    ## reserved_room_typeE:month11                     0.739767    
    ## reserved_room_typeF:month11                     0.708746    
    ## reserved_room_typeG:month11                     0.190753    
    ## reserved_room_typeH:month11                     0.243695    
    ## reserved_room_typeL:month11                           NA    
    ## reserved_room_typeB:month12                     0.916649    
    ## reserved_room_typeC:month12                     0.096901 .  
    ## reserved_room_typeD:month12                     0.030163 *  
    ## reserved_room_typeE:month12                     0.789842    
    ## reserved_room_typeF:month12                     0.102033    
    ## reserved_room_typeG:month12                     0.002307 ** 
    ## reserved_room_typeH:month12                     0.153774    
    ## reserved_room_typeL:month12                           NA    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2274 on 44840 degrees of freedom
    ## Multiple R-squared:  0.3059, Adjusted R-squared:  0.3034 
    ## F-statistic: 124.3 on 159 and 44840 DF,  p-value: < 2.2e-16

    rmse(lm_small, hd_test)

    ## [1] 0.264067

    rmse(lm_best2, hd_test)

    ## Warning in predict.lm(model, data): prediction from a rank-deficient fit may be
    ## misleading

    ## [1] 0.2235335

##### Task 2: Model validation - step 1 (ROC)

    hv$date=ymd(hv$arrival_date)
    hv = mutate(hv,
                year=year(date)%>%factor(),
                wday=wday(date)%>%factor(),
                month=month(date)%>%factor())
    phat_test_lm = predict(lm_best2, hv, type='response')

    ## Warning in predict.lm(lm_best2, hv, type = "response"): prediction from a rank-
    ## deficient fit may be misleading

    thresh_grid = seq(0.95, 0.05, by=-0.005)
    roc_curve = foreach(thresh = thresh_grid, .combine='rbind') %do% {
      yhat_test_lm = ifelse(phat_test_lm >= thresh, 1, 0)
      # FPR, TPR for linear model
      confusion_out_linear = table(children = hv$children, yhat = yhat_test_lm)
      out_lin = data.frame(model = "linear",
                           TPR = confusion_out_linear[2,2]/sum(hv$children==1),
                           FPR = confusion_out_linear[1,2]/sum(hv$children==0))
    } %>% as.data.frame()
    ggplot(roc_curve) +
      geom_line(aes(x=FPR, y=TPR, color=model)) +
      labs(title="ROC curves: linear model") +
      theme_bw(base_size = 10)

![](DM_HW2_WeiZhou_files/figure-markdown_strict/unnamed-chunk-19-1.png)

##### Task 3: Model validation - step 2 (K folds)

    table <- cbind(pred,true_n,pred/250)
    colnames(table) <- c("predicted_n","true_prob","predict_probability")
    print(table)

    ##           predicted_n true_prob predict_probability
    ## result.1     23.00133        19          0.09200530
    ## result.2     15.56918        19          0.06227673
    ## result.3     23.15637        18          0.09262548
    ## result.4     24.03546        23          0.09614184
    ## result.5     17.38390        21          0.06953561
    ## result.6     18.23657        19          0.07294628
    ## result.7     16.97051        16          0.06788203
    ## result.8     18.97921        19          0.07591682
    ## result.9     25.81153        23          0.10324612
    ## result.10    24.46276        20          0.09785105
    ## result.11    20.30527        23          0.08122109
    ## result.12    19.84722        18          0.07938889
    ## result.13    20.40745        21          0.08162979
    ## result.14    21.28232        19          0.08512926
    ## result.15    16.56492        19          0.06625969
    ## result.16    25.47909        25          0.10191637
    ## result.17    20.93967        21          0.08375869
    ## result.18    18.17550        20          0.07270199
    ## result.19    16.99804        16          0.06799216
    ## result.20    19.00305        23          0.07601219

    mean(rmse)  

    ## [1] 0.2386704

    sd(rmse)/sqrt(K_folds)

    ## [1] 0.0040378

###### From the result table of the 20 folds, we can see the prediction is quite good. Only in the first fold, the difference is larger. Overall, it is quite good. We can also see the mean of rmse in all folds is 0.2382634.
