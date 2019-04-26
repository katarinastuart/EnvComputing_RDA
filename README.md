<div id="ecology-and-multivariate-statistics" class="section level1">
<h1>Ecology and Multivariate Statistics</h1>
</div>
<div id="redundancy-analysis-rda" class="section level1">
<h1>Redundancy Analysis (RDA)</h1>
<p>Redundancy analysis (RDA) is a dimension reduction statistical approach for multivariate data sets. Dimension reduction techniques are often used on large, noisy, data sets to strip away any unnecessary components, leaving only those part that best capture any explanatory/predictor power. Even if you have not heard of RDA, it is very possible that you have encounter one of the main linear techniques for dimensionality reduction; principle component analysis (PCA).</p>
<p>Some background reading relevant to what we will be doing:</p>
<ul>
<li><a href="https://academic.oup.com/jhered/article/108/2/207/2726867">Talbot et al. 2016</a></li>
<li><a href="https://www.nature.com/articles/439803a">Phillips et al. 2006</a></li>
<li><a href="https://onlinelibrary.wiley.com/doi/full/10.1111/mec.13184">Rollins et al. 2015</a></li>
<li><a href="https://www.ncbi.nlm.nih.gov/pubmed/18467157">Prentis et al. 2008</a></li>
<li><a href="https://onlinelibrary.wiley.com/doi/full/10.1111/mec.13162">Colautti and Lau 2015</a></li>
</ul>
<div id="a-quick-pca-and-rda-overview" class="section level2">
<h2>A quick PCA and RDA overview</h2>
<p>PCA is used to discern underlying structures (i.e. principle components) that capture variance in a data set. For more information on the fundamentals of PCA I would recommend visiting <a href="https://georgemdallas.wordpress.com/2013/10/30/principal-component-analysis-4-dummies-eigenvectors-eigenvalues-and-dimension-reduction/">Principal Component Analysis 4 Dummies: Eigenvectors, Eigenvalues and Dimension Reduction</a>.</p>
<p>RDA is similar to PCA, however it incorporates the uses of <strong>response</strong> and <strong>explanatory</strong> variables, whereas analysis such as PCA doesn’t incorporate the explanatory variables.</p>
<p>RDA is traditionally used on <strong>response</strong> variables such as:</p>
<ul>
<li>SNP (single nucleotide polymorphism) genetic data: see <a href="https://popgen.nescent.org/2018-03-27_RDA_GEA.html">example</a></li>
<li>Ecological community data: <a href="https://rgriff23.github.io/2017/05/23/mosquito-community-ecology-in-vegan.html">see example1</a> and <a href="http://cc.oulu.fi/~jarioksa/opetus/metodi/vegantutor.pdf">example 2</a></li>
</ul>
<p>For this example, I have used the vignettes available on the above two links, as well as additional notes from Natalie Hofmeister and myself.</p>
</div>
  <div id="how-can-we-use-rda-in-other-ways" class="section level2">
<h2>How can we use RDA in other ways?</h2>
<p>Below I apply the approach to a multivariate phenotypic dataset. The aim is to see if the environmental data associated with the areas from which the wild-caught toads were collected carry any explanatory power for various phenotypic measures. I.e. we want to explore the link between environment and phenotype. As listed above, RDA can help you pick out environmental correlations in genotype data, and could conceivably be used to link genotype measures to that of phenotype.</p>

![ScreenShot](/Images/AnalysisTriangle.png)
 

<p>Use of RDA for the below analysis can be either quantitative or qualitative. The direct RDA outputs may be useful for you, or they may simply serve as an exploratory tool to direct further analysis.</p>
</div>
  <div id="introducing-the-data-set-cane-toads" class="section level2">
<h2>Introducing the data set: Cane Toads!</h2>
<p>The following data is one generated from my honours research. It contains phenotype variables for a number of cane toads sourced from one of three states: QLD, NT, or WA.</p>
<p>Let’s import the data and have a quick look at it</p>
<pre class="r"><code>setwd(&quot;C:/Users/z5188231/Desktop/Coding/Files/EnvironmentalComputing&quot;)
ToadData &lt;- read.csv(&quot;CaneToad_Phenotypes.csv&quot;,stringsAsFactors=TRUE,sep=&quot;,&quot;)
str(ToadData)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  19 variables:
##  $ Exercise                               : int  1 0 1 1 1 1 1 1 1 0 ...
##  $ Diet                                   : int  0 1 1 1 1 0 1 0 0 0 ...
##  $ Source.Location                        : Factor w/ 3 levels &quot;NT&quot;,&quot;QLD&quot;,&quot;WA&quot;: 1 1 1 1 1 1 1 1 1 1 ...
##  $ Clutch                                 : Factor w/ 8 levels &quot;Innisfail 1&quot;,..: 4 4 4 5 5 5 5 5 5 5 ...
##  $ Individual.number                      : int  442 445 453 402 403 404 405 406 407 409 ...
##  $ Body.Length                            : num  28.7 25.4 29.3 28.9 32 32.1 31.9 54.3 24.1 37.5 ...
##  $ Leg.length..mm.                        : num  0.376 0.343 0.334 0.36 0.347 ...
##  $ Arm.length..mm.                        : num  0.254 0.22 0.242 0.235 0.234 ...
##  $ Head.Width..mm.                        : num  0.345 0.331 0.341 0.356 0.338 ...
##  $ Heart.Mass..g.                         : num  0.000627 0.000276 0.000614 0.000519 0.000812 ...
##  $ Liver.Mass..g.                         : num  0.003066 0.000906 0.002628 0.001419 0.002875 ...
##  $ Struggle..number.of.kicks.             : int  8 3 9 3 0 2 3 3 2 4 ...
##  $ Righting.time..s.                      : int  1 1 1 1 3 1 3 1 1 1 ...
##  $ Distance.traveled.in.1.min..cm.        : int  573 496 721 775 534 890 365 1140 450 512 ...
##  $ Spontaneous.Acitivty...95.quantile..cm.: num  14.4 10.3 31.1 62.6 32.8 ...
##  $ exhaustion.score                       : num  4.33 4.96 3.89 4.42 4.45 ...
##  $ Stamina.score                          : num  -0.263 0.301 -0.316 -0.132 -0.135 ...
##  $ Recovery.score                         : num  0.1553 -0.2401 0.1488 -0.0193 0.2098 ...
##  $ ln.RadioTelemetry.distance             : num  NA NA NA NA 6.8 NA 7.1 10.2 NA NA ...</code></pre>
<p>We see there is a total of 121 toads in this data set. The first 5 columns of these data denote information about the treatment levels (exercise/diet), source location, clutch, and and individual ID for each toad. The remaining 14 columns the contain phenotypic data.</p>
</div>
<div id="prepare-response-and-explanatory-data-sets" class="section level2">
<h2>Prepare Response and Explanatory data sets</h2>
<div id="step-1.1-preparing-the-response-dataset-phenotype-data" class="section level3">
<h3>Step 1.1 Preparing the response dataset (phenotype data)</h3>
<p>First we check the data for any missingness, and make sure our matrix does not have any NA’s.</p>
<pre class="r"><code>sum(is.na(ToadData)) # we see there is 91 missing data</code></pre>
<pre><code>## [1] 91</code></pre>
<pre class="r"><code>colnames(ToadData)[colSums(is.na(ToadData)) &gt; 0] #explore data to identify where they are located</code></pre>
<pre><code>## [1] &quot;ln.RadioTelemetry.distance&quot;</code></pre>
<pre class="r"><code>ToadData &lt;- ToadData[c(1:18)] #remove offending data
sum(is.na(ToadData)) #0 missing data</code></pre>
<pre><code>## [1] 0</code></pre>
<p>In the above chunk we discovered 91 pieces of missing information. This is because some variables were not measured across all toads. It appears all the NA’s were in the final variable column - <em>ln.RadioTelemetry.distance</em>. So to prepare our data for the RDA, we need to remove this offending variable. Make sure to find the most appropriate way to resolve this should your data set have missing values. You may be able to remove rows, remove columns, generate an average etc. but you don’t want to reduce your data set drastically or remove too many variables/individuals.</p>
<p>Next we retain only the phenotype data columns, as these are the ones we are interested on conducting the RDA on.</p>
<pre class="r"><code>ToadVars &lt;- ToadData[c(6:18)]
str(ToadVars)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  13 variables:
##  $ Body.Length                            : num  28.7 25.4 29.3 28.9 32 32.1 31.9 54.3 24.1 37.5 ...
##  $ Leg.length..mm.                        : num  0.376 0.343 0.334 0.36 0.347 ...
##  $ Arm.length..mm.                        : num  0.254 0.22 0.242 0.235 0.234 ...
##  $ Head.Width..mm.                        : num  0.345 0.331 0.341 0.356 0.338 ...
##  $ Heart.Mass..g.                         : num  0.000627 0.000276 0.000614 0.000519 0.000812 ...
##  $ Liver.Mass..g.                         : num  0.003066 0.000906 0.002628 0.001419 0.002875 ...
##  $ Struggle..number.of.kicks.             : int  8 3 9 3 0 2 3 3 2 4 ...
##  $ Righting.time..s.                      : int  1 1 1 1 3 1 3 1 1 1 ...
##  $ Distance.traveled.in.1.min..cm.        : int  573 496 721 775 534 890 365 1140 450 512 ...
##  $ Spontaneous.Acitivty...95.quantile..cm.: num  14.4 10.3 31.1 62.6 32.8 ...
##  $ exhaustion.score                       : num  4.33 4.96 3.89 4.42 4.45 ...
##  $ Stamina.score                          : num  -0.263 0.301 -0.316 -0.132 -0.135 ...
##  $ Recovery.score                         : num  0.1553 -0.2401 0.1488 -0.0193 0.2098 ...</code></pre>
<p>We have now finished preparing the phenotype data (the <strong>response variables</strong>) for our RDA analysis.</p>
<p><strong>Some points to consider:</strong></p>
<ul>
<li>Make sure that your response variables are independent. For instance, with this toad data set, I originally had measurements of Body Length, Arm Length, Leg Length etc. These measures would presumably be largely correlated with one another.
<ul>
<li><em>Solution: To deal with this, either omit redundant response data, or compute it in such a way that it represents a more independent aspect of the phenology (in this case I calculated limb/head lengths as a ratio of total body length).</em></li>
</ul></li>
<li>The data should be normally distributed.
<ul>
<li><em>Solution: Transform variables that do not comply. The transformation type needed depends on the data, so make sure to do your research. A good place to start is the <a href="http://www.biostathandbook.com/transformation.html">Handbook of Biological Statistics</a>. </em></li>
</ul></li>
</ul>
</div>
<div id="step-2.1-preparing-the-explanatory-dataset-environmental-data" class="section level3">
<h3>Step 2.1 Preparing the explanatory dataset (environmental data)</h3>
<p>The first step here is to grab global climate data from <a href="http://worldclim.org/version2">WorldClim</a> using the raster package.</p>
<pre class="r"><code>library(raster)
climdata &lt;- getData('worldclim',download=TRUE,var='bio',res=5)</code></pre>
<p>This contains 19 different climate variables, measured globally. From this we want to select only the locations that are relevant for our study. Your metadata file should be just two columns: latitude and longitude for each individual (121), written in the format as below (and in the same order as your response data set).</p>
<pre class="r"><code>setwd(&quot;C:/Users/z5188231/Desktop/Coding/Files/EnvironmentalComputing&quot;)
popcoords &lt;- read.csv(&quot;metadata.csv&quot;,stringsAsFactors=TRUE,sep=&quot;,&quot;)
head(popcoords)</code></pre>
<pre><code>##       long      lat
## 1 131.3253 -12.5648
## 2 131.3253 -12.5648
## 3 131.3253 -12.5648
## 4 131.3253 -12.5648
## 5 131.3253 -12.5648
## 6 131.3253 -12.5648</code></pre>
<pre class="r"><code>str(popcoords)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  2 variables:
##  $ long: num  131 131 131 131 131 ...
##  $ lat : num  -12.6 -12.6 -12.6 -12.6 -12.6 ...</code></pre>
<p>How we have all the sample sites, we can pull out the climate data from global dataset for each individual coordinate, and label them in a human readable format (variable names obtained from the <a href="http://worldclim.org/version2">WorldClim</a> website.</p>
<pre class="r"><code>points &lt;- SpatialPoints(popcoords, proj4string=climdata@crs)
values &lt;- extract(climdata,points)
envdata &lt;- cbind.data.frame(popcoords,values)
str(envdata)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  21 variables:
##  $ long : num  131 131 131 131 131 ...
##  $ lat  : num  -12.6 -12.6 -12.6 -12.6 -12.6 ...
##  $ bio1 : num  273 273 273 273 273 273 273 273 273 273 ...
##  $ bio2 : num  111 111 111 111 111 111 111 111 111 111 ...
##  $ bio3 : num  59 59 59 59 59 59 59 59 59 59 ...
##  $ bio4 : num  1851 1851 1851 1851 1851 ...
##  $ bio5 : num  352 352 352 352 352 352 352 352 352 352 ...
##  $ bio6 : num  164 164 164 164 164 164 164 164 164 164 ...
##  $ bio7 : num  188 188 188 188 188 188 188 188 188 188 ...
##  $ bio8 : num  281 281 281 281 281 281 281 281 281 281 ...
##  $ bio9 : num  244 244 244 244 244 244 244 244 244 244 ...
##  $ bio10: num  292 292 292 292 292 292 292 292 292 292 ...
##  $ bio11: num  244 244 244 244 244 244 244 244 244 244 ...
##  $ bio12: num  1395 1395 1395 1395 1395 ...
##  $ bio13: num  334 334 334 334 334 334 334 334 334 334 ...
##  $ bio14: num  1 1 1 1 1 1 1 1 1 1 ...
##  $ bio15: num  103 103 103 103 103 103 103 103 103 103 ...
##  $ bio16: num  887 887 887 887 887 887 887 887 887 887 ...
##  $ bio17: num  4 4 4 4 4 4 4 4 4 4 ...
##  $ bio18: num  391 391 391 391 391 391 391 391 391 391 ...
##  $ bio19: num  4 4 4 4 4 4 4 4 4 4 ...</code></pre>
<pre class="r"><code>colnames(envdata) &lt;- c(&quot;long&quot;,&quot;lat&quot;,
      &quot;AnnualMeanTemp&quot;,&quot;MeanDiurnalRange&quot;,
      &quot;Isothermality&quot;,&quot;TempSeasonality&quot;,
      &quot;MaxTempofWarmestMonth&quot;,&quot;MinTempofColdestMonth&quot;,
      &quot;TempAnnualRange&quot;,&quot;MeanTempofWettestQuarter&quot;,
      &quot;MeanTempofDriestQuarter&quot;,&quot;MeanTempofWarmestQuarter&quot;,
      &quot;MeanTempofColdestQuarter&quot;,&quot;AnnualPrecipitation&quot;,
      &quot;PrecipitationofWettestMonth&quot;,&quot;PrecipitationofDriestMonth&quot;,
      &quot;PrecipitationSeasonality&quot;,&quot;PrecipitationofWettestQuarter&quot;,
      &quot;PrecipitationofDriestQuarter&quot;,&quot;PrecipitationofWarmestQuarter&quot;,
      &quot;PrecipitationofColdestQuarter&quot;)
str(envdata)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  21 variables:
##  $ long                         : num  131 131 131 131 131 ...
##  $ lat                          : num  -12.6 -12.6 -12.6 -12.6 -12.6 ...
##  $ AnnualMeanTemp               : num  273 273 273 273 273 273 273 273 273 273 ...
##  $ MeanDiurnalRange             : num  111 111 111 111 111 111 111 111 111 111 ...
##  $ Isothermality                : num  59 59 59 59 59 59 59 59 59 59 ...
##  $ TempSeasonality              : num  1851 1851 1851 1851 1851 ...
##  $ MaxTempofWarmestMonth        : num  352 352 352 352 352 352 352 352 352 352 ...
##  $ MinTempofColdestMonth        : num  164 164 164 164 164 164 164 164 164 164 ...
##  $ TempAnnualRange              : num  188 188 188 188 188 188 188 188 188 188 ...
##  $ MeanTempofWettestQuarter     : num  281 281 281 281 281 281 281 281 281 281 ...
##  $ MeanTempofDriestQuarter      : num  244 244 244 244 244 244 244 244 244 244 ...
##  $ MeanTempofWarmestQuarter     : num  292 292 292 292 292 292 292 292 292 292 ...
##  $ MeanTempofColdestQuarter     : num  244 244 244 244 244 244 244 244 244 244 ...
##  $ AnnualPrecipitation          : num  1395 1395 1395 1395 1395 ...
##  $ PrecipitationofWettestMonth  : num  334 334 334 334 334 334 334 334 334 334 ...
##  $ PrecipitationofDriestMonth   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ PrecipitationSeasonality     : num  103 103 103 103 103 103 103 103 103 103 ...
##  $ PrecipitationofWettestQuarter: num  887 887 887 887 887 887 887 887 887 887 ...
##  $ PrecipitationofDriestQuarter : num  4 4 4 4 4 4 4 4 4 4 ...
##  $ PrecipitationofWarmestQuarter: num  391 391 391 391 391 391 391 391 391 391 ...
##  $ PrecipitationofColdestQuarter: num  4 4 4 4 4 4 4 4 4 4 ...</code></pre>
</div>
<div id="step-2.2-check-which-variables-are-correlated" class="section level3">
<h3>Step 2.2 Check which variables are correlated</h3>
<p>We mentioned earlier on that we wanted to remove non-independent measures from our response data set. A similar process must be done for the explanatory dataset. Some of our environmental measurement are likely to be highly correlated across our sampling sites. There are several ways of doing this, which will also be informed by your experimental model/questions. If you are particularly interested in specific environmental measures, you may want to leave those in only, and run the RDA. You could also conceivably make the decision based on the ecology and climate of the ecosystems. If you are interested in a more exploratory approach (like we are doing now), then I would recommend using one of the below methods:</p>
<p><em>Note: Where possible it is probably best to use method two, however for studies where the sample environments are more similar method one is more appropriate. </em></p>
<div id="method-one-pca-to-identify-correlated-environmental-predictors" class="section level4">
<h4>Method one: PCA to identify correlated environmental predictors</h4>
<p>For this method we use a PCA to determine which variables are colinear. We will run the analysis, then go through and remove any unnecessary environmental variables whose variance is represented elsewhere in another measure.</p>
<pre class="r"><code>env.PCA &lt;- princomp(envdata)
summary(env.PCA)</code></pre>
<pre><code>## Importance of components:
##                             Comp.1     Comp.2       Comp.3       Comp.4
## Standard deviation     1393.425566 468.663169 84.353531286 1.215425e+01
## Proportion of Variance    0.895337   0.101284  0.003281146 6.812006e-05
## Cumulative Proportion     0.895337   0.996621  0.999902097 9.999702e-01
##                              Comp.5       Comp.6       Comp.7       Comp.8
## Standard deviation     7.843926e+00 1.517241e+00 8.709465e-01 7.591008e-06
## Proportion of Variance 2.837174e-05 1.061520e-06 3.497856e-07 2.657161e-17
## Cumulative Proportion  9.999986e-01 9.999997e-01 1.000000e+00 1.000000e+00
##                              Comp.9      Comp.10      Comp.11      Comp.12
## Standard deviation     5.004443e-06 2.258427e-06 1.877824e-06 1.708714e-06
## Proportion of Variance 1.154863e-17 2.351966e-18 1.626031e-18 1.346350e-18
## Cumulative Proportion  1.000000e+00 1.000000e+00 1.000000e+00 1.000000e+00
##                             Comp.13      Comp.14      Comp.15 Comp.16
## Standard deviation     9.439264e-07 8.605142e-07 8.118830e-07       0
## Proportion of Variance 4.108613e-19 3.414562e-19 3.039526e-19       0
## Cumulative Proportion  1.000000e+00 1.000000e+00 1.000000e+00       1
##                        Comp.17 Comp.18 Comp.19 Comp.20 Comp.21
## Standard deviation           0       0       0       0       0
## Proportion of Variance       0       0       0       0       0
## Cumulative Proportion        1       1       1       1       1</code></pre>
<p>It appears the first two PCA components capture nearly all the data variability. However, I will grab the variable with the highest loading from the first 4 principle components (so it can be compared to the below method two)</p>
<pre class="r"><code>env.PCA$loadings[,1:4]</code></pre>
<pre><code>##                                      Comp.1        Comp.2       Comp.3
## long                           0.0048404979  4.761069e-03  0.048835380
## lat                           -0.0007300049 -3.850558e-03 -0.009170369
## AnnualMeanTemp                -0.0141177073 -6.303686e-03 -0.127762783
## MeanDiurnalRange              -0.0132345224 -9.097271e-04 -0.073499737
## Isothermality                 -0.0013768578 -3.828411e-03 -0.009191644
## TempSeasonality               -0.0448161066  9.820835e-01 -0.114544286
## MaxTempofWarmestMonth         -0.0223526615  1.505883e-05 -0.190967918
## MinTempofColdestMonth         -0.0028274595 -1.037636e-02 -0.094961542
## TempAnnualRange               -0.0195252020  1.039142e-02 -0.096006376
## MeanTempofWettestQuarter      -0.0106398206  7.489046e-03 -0.098423045
## MeanTempofDriestQuarter       -0.0059839507 -1.281836e-02 -0.117094478
## MeanTempofWarmestQuarter      -0.0136925588  4.429645e-03 -0.126836652
## MeanTempofColdestQuarter      -0.0126194600 -1.932741e-02 -0.123305899
## AnnualPrecipitation            0.8276645875  5.084426e-05 -0.361742741
## PrecipitationofWettestMonth    0.1272388027 -2.860341e-02  0.023180736
## PrecipitationofDriestMonth     0.0269402527  1.618841e-02 -0.023552139
## PrecipitationSeasonality      -0.0136866807 -4.736583e-04 -0.013179815
## PrecipitationofWettestQuarter  0.3797219678 -5.242919e-02  0.002339598
## PrecipitationofDriestQuarter   0.0818568962  5.225970e-02 -0.070568217
## PrecipitationofWarmestQuarter  0.3572417953  1.481986e-01  0.835899853
## PrecipitationofColdestQuarter  0.1247560261  7.789082e-02 -0.131792843
##                                     Comp.4
## long                           0.031557960
## lat                           -0.008248749
## AnnualMeanTemp                -0.076010988
## MeanDiurnalRange              -0.113298480
## Isothermality                 -0.011999364
## TempSeasonality               -0.089897185
## MaxTempofWarmestMonth         -0.137352232
## MinTempofColdestMonth          0.012207835
## TempAnnualRange               -0.149560066
## MeanTempofWettestQuarter      -0.040115110
## MeanTempofDriestQuarter       -0.038223218
## MeanTempofWarmestQuarter      -0.062890539
## MeanTempofColdestQuarter      -0.061909046
## AnnualPrecipitation            0.034152563
## PrecipitationofWettestMonth    0.196372533
## PrecipitationofDriestMonth    -0.009702084
## PrecipitationSeasonality      -0.047680531
## PrecipitationofWettestQuarter -0.479111735
## PrecipitationofDriestQuarter   0.037799171
## PrecipitationofWarmestQuarter  0.027677406
## PrecipitationofColdestQuarter  0.803751378</code></pre>
<p>Looks like TempSeasonality, Annual Precipitation, PrecipitationofWarmestQuarter, and PrecipitationofWarmestQuarter are the most important.</p>
<pre class="r"><code>PCApredictions &lt;- envdata[c(6,14,18:19)]
str(PCApredictions)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  4 variables:
##  $ TempSeasonality              : num  1851 1851 1851 1851 1851 ...
##  $ AnnualPrecipitation          : num  1395 1395 1395 1395 1395 ...
##  $ PrecipitationofWettestQuarter: num  887 887 887 887 887 887 887 887 887 887 ...
##  $ PrecipitationofDriestQuarter : num  4 4 4 4 4 4 4 4 4 4 ...</code></pre>
</div>
<div id="method-two-r-squared-to-identify-correlated-environmental-predictors" class="section level4">
<h4>Method two: R squared to identify correlated environmental predictors</h4>
<p>For more information on using this approach, use this <a href="https://popgen.nescent.org/2018-03-27_RDA_GEA.html">RDA vignette</a>. The r squared values generated by the below approach should guide your variable choice. For variable pairs that are highly colinear, remove one, preferencing the exclusion of variables that are colinear with multiple other measures (r squared value of &gt; 0.7 is considered “high”). As noted above though, when the sampled environments are more similar, as is the case with this cane toad dataset, we will see more collinearity.</p>
<p><em>Note: given we are using so many environmental variables, some may be cut off on your plot. Either adjust the plot or progressively remove variables.</em></p>
<pre class="r"><code>library(psych)
pairs.panels(envdata[,3:19], scale=T)</code></pre>
<p>

![ScreenShot](/Images/Paired_panel.png)
   
<p>You may end up with something like this (though note the strong relationship annual mean temp has too nearly all the environmental variables):</p>
<pre class="r"><code>VIFpredictions &lt;- envdata[c(3,6,8,14)]
str(VIFpredictions)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  4 variables:
##  $ AnnualMeanTemp       : num  273 273 273 273 273 273 273 273 273 273 ...
##  $ TempSeasonality      : num  1851 1851 1851 1851 1851 ...
##  $ MinTempofColdestMonth: num  164 164 164 164 164 164 164 164 164 164 ...
##  $ AnnualPrecipitation  : num  1395 1395 1395 1395 1395 ...</code></pre>
<pre class="r"><code>pairs.panels(VIFpredictions, scale=T)</code></pre>
<p>![ScreenShot](/Images/Paired_panel2.png)</p>
</div>
<div id="method-three-variance-inflation-factors-to-identify-correlated-environmental-predictors" class="section level4">
<h4>Method three: Variance Inflation Factors to identify correlated environmental predictors</h4>
<p>I would recommend using one of the above two methods to remove variables that are supper collinear, then use this last method to refine predictor choice. Simple use the <strong>vif.cca</strong> function to check variance inflation factors for each predictor, and sequentially remove the highest one until you have an appropriate number of predictors.</p>
<p>HOWEVER, this needs to be run after the RDA has been ordinated (notice how it calls <em>ToadVars.rda</em>). So jump back up here once you have finished off step 3.2.</p>
<pre class="r"><code>vif.cca(ToadVars.rda) #this code will reappear below in step 3.2</code></pre>
<p>We have now finished preparing the environmental data (the <strong>explanatory variables</strong>) for our RDA analysis. Depending on the method used, we either have the environmental dataset PCApredictions, or VIFpredictions.</p>
</div>
</div>
</div>
<div id="conducting-the-rda" class="section level2">
<h2>Conducting the RDA</h2>
<div id="step-3.1-ordinating-the-datado-the-rda" class="section level3">
<h3>Step 3.1 Ordinating the data/do the RDA</h3>
<pre class="r"><code>library(vegan)
ToadVars.rda &lt;- rda(ToadVars ~ AnnualMeanTemp + TempSeasonality + MinTempofColdestMonth + AnnualPrecipitation, data=VIFpredictions, scale=T)
ToadVars.rda</code></pre>
<pre><code>## Call: rda(formula = ToadVars ~ AnnualMeanTemp + TempSeasonality +
## MinTempofColdestMonth + AnnualPrecipitation, data =
## VIFpredictions, scale = T)
## 
##                Inertia Proportion Rank
## Total         13.00000    1.00000     
## Constrained    0.89205    0.06862    4
## Unconstrained 12.10795    0.93138   13
## Inertia is correlations 
## 
## Eigenvalues for constrained axes:
##   RDA1   RDA2   RDA3   RDA4 
## 0.5232 0.1982 0.1340 0.0366 
## 
## Eigenvalues for unconstrained axes:
##    PC1    PC2    PC3    PC4    PC5    PC6    PC7    PC8    PC9   PC10 
## 2.7133 1.9616 1.3353 1.1553 0.9783 0.9078 0.7673 0.7193 0.5474 0.3590 
##   PC11   PC12   PC13 
## 0.2879 0.2192 0.1561</code></pre>
</div>
<div id="step-3.2-check-the-rda" class="section level3">
<h3>Step 3.2 Check the RDA</h3>
<p>We can now check how much variation is explained by out RDA.</p>
<pre class="r"><code>RsquareAdj(ToadVars.rda)</code></pre>
<pre><code>## $r.squared
## [1] 0.06861938
## 
## $adj.r.squared
## [1] 0.03650281</code></pre>
<p>3.6% is not large, but then consider that phenotype is the end result of a large number of factors (genetics, epigenetics etc…), of which environmental effects are only one! And our environmental data set does not even use all environment data.</p>
<pre class="r"><code>screeplot(ToadVars.rda) #to determine how many RDA axes there are / are important if there are many</code></pre>
<p>
 ![ScreenShot](/Images/plot1.png)
  </p>
<pre class="r"><code>signif.full &lt;- anova.cca(ToadVars.rda, parallel=getOption(&quot;mc.cores&quot;))
signif.full # test for model significance</code></pre>
<pre><code>## Permutation test for rda under reduced model
## Permutation: free
## Number of permutations: 999
## 
## Model: rda(formula = ToadVars ~ AnnualMeanTemp + TempSeasonality + MinTempofColdestMonth + AnnualPrecipitation, data = VIFpredictions, scale = T)
##           Df Variance      F Pr(&gt;F)    
## Model      4   0.8921 2.1366  0.001 ***
## Residual 116  12.1079                  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1</code></pre>
<pre class="r"><code>vif.cca(ToadVars.rda) # HERE IT IS! Get variance inflation factors for each predictor (refer to step 2.2, method three) </code></pre>
<pre><code>##        AnnualMeanTemp       TempSeasonality MinTempofColdestMonth 
##             46.660123              2.297345             17.553396 
##   AnnualPrecipitation 
##             19.625697</code></pre>
<p>The last quality check repots back some high VIF values. This is not entirely unexpected as our VIFpredictions dataset had high collinearity for AnnualMeanTemp. If we were to redo this with PCApredictions we may have lower numbers, indicating it would be better to use the variables generated by the PCA.</p>
</div>
<div id="step-3.3-visualise-the-rda" class="section level3">
<h3>Step 3.3 Visualise the RDA</h3>
<p>We will proceed with graphing our RDA.</p>
<pre class="r"><code>plot(ToadVars.rda, scaling=3)</code></pre>
<p>![ScreenShot](/Images/rda1.png)</p>
<p>RDA may be interpreted similarly to PCA, with each ordination axis capturing some of the data variance. The blue arrows indicate the magnitude and direction of the environmental predictors. The arrangement of the circles (the toads), the red crosses (our phenotypic measurements) and the environmental predictors against the different axis informs us what is loaded on each of said axis.</p>
<p>Now we can format it to make it more intuitive.</p>
<pre class="r"><code>location &lt;- c((rep(&quot;NT&quot;,30)),(rep(&quot;QLD&quot;,57)),(rep(&quot;WA&quot;,34)))

VIFpredictions &lt;- cbind(VIFpredictions,location)
str(VIFpredictions)</code></pre>
<pre><code>## 'data.frame':    121 obs. of  5 variables:
##  $ AnnualMeanTemp       : num  273 273 273 273 273 273 273 273 273 273 ...
##  $ TempSeasonality      : num  1851 1851 1851 1851 1851 ...
##  $ MinTempofColdestMonth: num  164 164 164 164 164 164 164 164 164 164 ...
##  $ AnnualPrecipitation  : num  1395 1395 1395 1395 1395 ...
##  $ location             : Factor w/ 3 levels &quot;NT&quot;,&quot;QLD&quot;,&quot;WA&quot;: 1 1 1 1 1 1 1 1 1 1 ...</code></pre>
<pre class="r"><code>levels(VIFpredictions$location) &lt;- c(&quot;NT&quot;,&quot;QLD&quot;,&quot;WA&quot;)
eco &lt;- VIFpredictions$location
bg &lt;- c(&quot;#2ACBB3&quot;,&quot;#602ACB&quot;,&quot;#A2C61C&quot;)

plot(ToadVars.rda, type=&quot;n&quot;, scaling=3)
points(ToadVars.rda, display=&quot;species&quot;, pch=20, cex=0.7, col=&quot;gray32&quot;, scaling=3)           # the phenotypes
points(ToadVars.rda, display=&quot;sites&quot;, pch=21, cex=1.3, col=&quot;gray32&quot;, scaling=3, bg=bg[eco]) # the toads
text(ToadVars.rda, scaling=3, display=&quot;bp&quot;, col=&quot;#0868ac&quot;, cex=1)                           # the environmental predictors
legend(&quot;bottomright&quot;, legend=levels(eco), bty=&quot;n&quot;, col=&quot;gray32&quot;, pch=21, cex=1, pt.bg=bg)</code></pre>
<p>
![ScreenShot](/Images/rda2.png)
</p>
<p>Trying to label the phenotypic variables</p>
<pre class="r"><code>plot(ToadVars.rda, type=&quot;n&quot;, scaling=3, choices=c(1,3))
points(ToadVars.rda, display=&quot;species&quot;, pch=20, cex=0.7, col=&quot;gray32&quot;, scaling=3)           # the phenotypes
#text (ToadVars.rda, display=&quot;species&quot;, cex=0.65, pos=3,col=&quot;red&quot;)
points(ToadVars.rda, display=&quot;species&quot;, pch=21, cex=1.3, col=&quot;gray32&quot;, scaling=3) # the toads
points(ToadVars.rda, display=&quot;sites&quot;, pch=21, cex=1.3, col=&quot;gray32&quot;, scaling=3, bg=bg[eco]) # the toads
text(ToadVars.rda, scaling=3, display=&quot;bp&quot;, col=&quot;#0868ac&quot;, cex=1)                           # the environmental predictors
legend(&quot;bottomright&quot;, legend=levels(eco), bty=&quot;n&quot;, col=&quot;gray32&quot;, pch=21, cex=1, pt.bg=bg)</code></pre>
<p>
![ScreenShot](/Images/rda3.png)
</p>
<pre class="r"><code>load.rda &lt;- scores(ToadVars.rda, choices=c(1:3), display=&quot;species&quot;)
outliers &lt;- function(x,z){
  lims &lt;- mean(x) + c(-1, 1) * z * sd(x)     # find loadings +/-z sd from mean loading     
  x[x &lt; lims[1] | x &gt; lims[2]]               # locus names in these tails
}

# check for normal distribution
hist(load.rda[,1], main=&quot;Loadings on RDA1&quot;)</code></pre>
<p>
  ![ScreenShot](/Images/loading1.png)
</p>
<pre class="r"><code>hist(load.rda[,2], main=&quot;Loadings on RDA2&quot;)</code></pre>
<p>
![ScreenShot](/Images/loading2.png)
</p>
<pre class="r"><code>hist(load.rda[,3], main=&quot;Loadings on RDA3&quot;)</code></pre>
<p>
 ![ScreenShot](/Images/loading3.png)
</p>
<pre class="r"><code># get number of candidates &gt; 1.5 SDs from mean loading
cand1 &lt;- outliers(load.rda[,1],1.5) 
cand2 &lt;- outliers(load.rda[,2],1.5) 
cand3 &lt;- outliers(load.rda[,2],1.5)
cand1</code></pre>
<pre><code>## Leg.length..mm.  Recovery.score 
##      -0.7828865       0.3592628</code></pre>
<pre class="r"><code>cand2</code></pre>
<pre><code>## Distance.traveled.in.1.min..cm.                exhaustion.score 
##                       0.3529830                      -0.4115757</code></pre>
<pre class="r"><code>cand3</code></pre>
<pre><code>## Distance.traveled.in.1.min..cm.                exhaustion.score 
##                       0.3529830                      -0.4115757</code></pre>
</div>
</div>
</div>




</div>
