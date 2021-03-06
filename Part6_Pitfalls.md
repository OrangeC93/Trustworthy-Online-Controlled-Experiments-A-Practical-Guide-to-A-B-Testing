## [Pitfalls of AB testing (HBR with LinkedIn and Netflix)](https://hbr.org/2020/03/avoid-the-pitfalls-of-a-b-testing)
- (1) Not looking beyond average: 
  - Use metrics and approaches that reflect the value of different customer segments
    - Netfilx recommendation to heavy and light users: they alternates each users experience between treatment and control based on days. Also they developed a metric that balance the efforts on lightly and heavily engaged members to ensure that product changes don't benefit on e segment at the expense of another
  - Measure the impact across different levels of digital access
    - Netflix and LinkedIn track the upper, middle, and low percentiles of technical metrics(app loading time, delays before playback commences, and crash rates), Has the treatment slowed app loading relative to the control for both users in 5th of loading times and users in 95th, or has the treatment benefited users in the 5th while harming those in 95the, They user this approach to test innovations aimed at improving the quality of streaming video playback for different devices and network connectivity conditions
  - Always account for group specific behavior
    - LinkedIn’s A/B testing platform automatically computes the effects of experiments by group. for instance, LinkedIn found that instantly notifying job seekers of new job listings disproportionately increased the likelihood that sparsely connected individuals would apply for positions, because they’re less likely to hear of openings through other means than well-connected people are.
    - Finally, LinkedIn tracks the impact of changes on inequality itself by checking whether an innovation is increasing or decreasing the share of revenue, page views, and other top-line metrics contributed by the top 1% of users. This ensures that LinkedIn is not overoptimizing for the most active members at the expense of the less engaged.
  - Segment key markets
    - LinkedIn developed the LinkedIn Lite version of its main application, which has a lower image quality and a modified user interface, reducing the amount of data the app has to process. 
    - At Netflix, market-specific research on device usage has led the firm to experiment with and ultimately release a mobile-only membership plan for India.
- (2) Forgetting customers are connected
  - Use **network AB**: testing: LinkedIn content-recommendation algorithm that shows more long-form text content, such as news articles, and fewer images. Typically, images generate a lot of likes and a few comments, and news articles generate fewer likes but more comments. hile a standard A/B test will show that the new algorithm is generating fewer likes, network A/B testing will capture both the likes and the positive downstream impact initiated by more comments from the exposed users. 
  - Use **time series experiments**:
    - For example, imagine that LinkedIn develops a new algorithm for matching job seekers with job openings. To measure its effectiveness, LinkedIn would simultaneously expose all job postings and seekers in a given market to the new algorithm for 30 minutes. In the next 30-minute period, it would randomly decide to either switch to the old algorithm or stay with the new one. It would continue this process for at least two weeks to ensure that it sees all types of job search pattern. 
    - [Doordash switchback test](https://medium.com/@DoorDash/switchback-tests-and-randomized-experimentation-under-network-effects-at-doordash-f1d938ab7c2a)
- (3) Focusing predominantly on short term
  - Get the length of experiments right: week 
  - Run holdout experiment: Imagine you’re testing a feature that highlights professional milestones (such as getting a new job) achieved by network connections in a social media feed. This feature would likely be triggered intermittently, perhaps only once or twice a week, depending on who is in a member’s network. In such cases an experimental period of several weeks or months may be needed to ensure that members of the treatment group are exposed to enough updates to test the feature’s effect on feed quality, or how relevant users perceive the content to be.

## [Summit](https://alexdeng.github.io/public/files/ExpediaTestSummit.pdf)
#### Assumptions underneath large scale A/B tests
1. Randomization is performed on a fixed unit, e.g. user, page-view, document, game-session
2. Independence (i.i.d.)
3. Normal approximation by central limit theorem

#### Variance of metrics are hugely underestimated 
- Reasons: analysis units might not be independent, metrics are typiclly defined as average over the analysis unit
- Solution: Delta Method
  - A ratio of two metrics, both are average of i.i.d. obervations the ratio metric converge to a normal distribution and formula for hte variance exists.
  - ctr -> (clicks/users)/(pageviews/users)

#### Complex Randomization
- client experiment: client only receive new assignment when connected (randomized assignment are pushed to client every hour), apply changes at the next refresh window, app open or wake from background.
- social sharing

#### Insight Mining(chasing noises)
- Finding unexpected interactions with other experiments
- Look at time series of metric difference to reason about novelty effect, trend, weekend effect
- Slice and dice using different dimensions and attributes to finally find a subpopulation that the treatment performs well/badly
#### Normal Approximation
- CLT
- rule of thumb 355 * sknewness^2
- Some metrics like Revenue/uu have large skewness (long tail, 0 inflated)

![image](/img/normal_approximation.png)

Solution:  Balanced Design

#### Objective Bayesian Hypothesis Testing:
- P-value assumes the null is true and then computes the False Positive Rate
- With P(Null) and P(Alternative), we can truly compute P(Alternative|Data)
- Note 1 – P(Alternative|Data) = P(Null|Data) is also known as the False Discover Rate (FDR)
- FDR allows continuous monitoring: can stop the experiments once FDR is below a threshold
- FDR also adjusts for (most) multiple testing. We can compute FDR for a scorecard of thousands of metrics and make decision



#### New experiment 
Traditional experimentation:
- A fixed set of questions
- Focus heavily on a good design to enable answering all the relevant questions

Modern experimentation with big data:
- A set of primary questions, but more after seeing the results
- Focus on iteration. The goal of one experiment is to figure out what to do next and to test in the next iteration

Hypothesis Testing -> Knowledge Discovery/Machine Learning


## [12 pitfall](https://exp-platform.com/Documents/2017-08%20KDDMetricInterpretationPitfalls.pdf)
#### Metric Sample Ratio Mismatch
Example: users click a link on msn.com which opened a new browser tab, resutls shows 8.32% increase in PLT(page loading time), why one line js change cause a large performance degradation.

Reason: using browser back buttion causing a homepage reload. In the treatment, there was no back buttion option after opening a link in the new tab, but the homepage remianed open in the old tab so users could come back to it without a reload. The back button page reloads in the control were generally faster than the first page load in a session due to browser caching. With the faster back button page loads omitted in the treatment, treatment’s average PLT was substantially worse.

What causes MSRM happen:
- A change in user behaviour
- Different loss rates in telemetry between control and treatment, in correct instrumentation of new features

How to deal with:
- Understand what parts of the metric differ
- Isolate the parts that are not affected by mismatch and can still be trusted
- A good start is to look separately at the numeratorand the denominator of the metric

For example: PTL 
- 1.Average homepage PTL per user
  - a.Average homepage PLT per user with 1 homepage visit
  - b.Average homepage PLT per user with 2+ homepage visit
- 2.Number of homepage loads per user
  -  a.Number of back button loads
  -  b.Number of non back button loads
    -  i.PLT for non-back button loads

#### Misinterpretation of Ratio Metrics
Example: In one experiment a module located close to the bottom of the main page was moved to a higher position which requires users to scroll less to reach it. The result showed a 40% decrease in CTR of the module.
- CTR: avg CTR/users

Reason: 
- There is no SRM.
- When the numerator, the number of clicks on the module, and denominator, the number of times that the module was displayed to the users, of the metric were checked separately, it turned out that both were improved significantly but the denominator  moved  relatively  more  than  the  numerator; 200% and 74%, respectively. This interpretation does not hold when there is a change in the number of times the module was displayed to the users or when there is a change in the population of users who saw the module. 


There're 2 ways to compute ratio metrics:
- A: Average of ratios
- B: Ratio of averages

A has serveral pratical advantages over method B
- High sensitivity
- More resilient to outliers
- Less likely to suffer from having a metric level SRM
- Compute variance more easy
  - Since A commupte an average over users, which is typically the unit of randomization in an experiment
- Disadvantage: ratio metrics can be misleading or potentially invalid if the denominator of the metric changed

Solution: to detect denomiator mismatch in ratio metrics, we recommend to define count metris for the numerator and denominator and provide those in the result alongside the ratio metric.

#### Segment Interpretation

#### Outlier
Outliers can skew metric values and increase the variance of the metric making it more difficult to obtain statistically significant results. 

Common outlier handling techniques include 
- trimming (excluding the outlier values)
- capping (replacing the outlier values  with  a  fixed  threshold  value)
- windsorizing (replacing  the  outlier  values  with  the  value  at  specified percentile)

For all these approaches, we always recommend having a metric that counts the number of values affected by the outlier handling logic in the Data Quality section, to be able to detect situations (bots labeling) like the one in our example. 

Changing the metric to compute a percentile (25th, median, 75th, 95th are common) instead of the average is another way to handle outliers, though percentile metric are often less sensitive than averages, and are more expensive to compute. 

Applying a transformation, e.g. taking a log, also helps to reduce outlier impact, but makes the metric values difficult to interpret.

#### Claming sucess with a borderline pvalue
When a metric comes with a borderline p-value, it can be a sign of a false positive, and there are many such cases in the world of online A/B testing due to a large number of metrics computed for an experiment. We recommended for experimenters to evaluate experiment results by placing emphasis on strongly statistically significant metrics and (1)rerunning with larger traffic when metrics, in particular the OEC metrics, have borderline p-values. In cases where repeated reruns provide borderline p-values either due to small treatment effects or (2)when we cannot increase the traffic we can use Fishers Method to obtain more reliable conclusion

#### Continuous monitoring and early stoppping
(1) Making experiment owners aware of the pitfall, and establishing the guidelines that require making ship decision only at the pre-defined time point, is one approach to avoid this pitfall. 

(2) Another approach is to adjust the p-values, to account for extra checking. 

(3) Finally, in a Bayesian framework is proposed that, unlike NHST, naturally allows for continuous monitoring and early stopping.

#### Assuming the metric movement is homogeneous
Example: an experiment in Bing that evaluated  a  new  ad  auction  and  placement  algorithm, attempting to increase revenue by improving ad quality and keeping  the  number  of  ads  shown  roughly  the  same.  The experiment seemed to be very successful, increasing revenue by 2.3%, while at the same time decreasing the number of ads shown per page by 0.6%.  

Reason: heterogeneous treatment effects over original pages and dup pages, while on dup pages the number of ads per page decreased dramatically, by 2.3%, the number of ads per page on the original pages actually increased by 0.3%.


#### Segment interpretation
Both groups of users, those who saw a deeplink (U1) and those who did not (U2), showed a statistically significant increase in Sessions per User, the key Bing metric. However, the combination (U1 + U2) did not show a statistically significant change in the metric. 

Simpson’s paradox:
- A common way to run into a Simpson’s paradox is to keep recursively  segmenting  the  users  until  a  statistically significant  difference  is  found. Trying  to  see  if  the metric improved at least for some subgroup of users, they keep recursively segmenting the user population until they see the desired effect.
- This can be tested by conducting an SRM test (see Section 4) for each segment group.

#### Novelty and Primacy effects
For funnel based scenarios it is crucial to measure all parts of the process. At every step of the funnel we need to ensure that the success rates are compared and not just the raw clicks or user counts. In addition,  we should  measure both conditional and unconditional success rate metrics. Conditional success rates are defined as proportion of users that complete the given step among users that attempted to start the step. Unconditional  success rates compute the success rate taking into consideration all users who started at the top of the funnel. The combination of these two types of metrics along with detailed breakdowns at each step is the best guiding light towards improving our main goal while maintaining at least the current success rate standards.  
