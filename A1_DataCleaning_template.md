Assignment 1, Language development in Autism Spectrum Disorder (ASD) - Brushing up your code skills
===================================================================================================

In this first part of the assignment we will brush up your programming skills, and make you familiar with the data sets you will be analysing for the next parts of the assignment.

In this warm-up assignment you will: 1) Create a Github (or gitlab) account, link it to your RStudio, and create a new repository/project 2) Use small nifty lines of code to transform several data sets into just one. The final data set will contain only the variables that are needed for the analysis in the next parts of the assignment 3) Warm up your tidyverse skills (especially the sub-packages stringr and dplyr), which you will find handy for later assignments.

N.B: Usually you'll also have to doc/pdf with a text. Not for Assignment 1.

Learning objectives:
--------------------

-   Become comfortable with tidyverse (and R in general)
-   Test out the git integration with RStudio
-   Build expertise in data wrangling (which will be used in future assignments)

0. First an introduction on the data
------------------------------------

Language development in Autism Spectrum Disorder (ASD)
======================================================

Reference to the study: <https://www.ncbi.nlm.nih.gov/pubmed/30396129>

Background: Autism Spectrum Disorder (ASD) is often related to language impairment, and language impairment strongly affects the patients ability to function socially (maintaining a social network, thriving at work, etc.). It is therefore crucial to understand how language abilities develop in children with ASD, and which factors affect them (to figure out e.g. how a child will develop in the future and whether there is a need for language therapy). However, language impairment is always quantified by relying on the parent, teacher or clinician subjective judgment of the child, and measured very sparcely (e.g. at 3 years of age and again at 6).

In this study we videotaped circa 30 kids with ASD and circa 30 comparison kids (matched by linguistic performance at visit 1) for ca. 30 minutes of naturalistic interactions with a parent. We repeated the data collection 6 times per kid, with 4 months between each visit. We transcribed the data and counted: i) the amount of words that each kid uses in each video. Same for the parent. ii) the amount of unique words that each kid uses in each video. Same for the parent. iii) the amount of morphemes per utterance (Mean Length of Utterance) displayed by each child in each video. Same for the parent.

Different researchers involved in the project provide you with different datasets: 1) demographic and clinical data about the children (recorded by a clinical psychologist) 2) length of utterance data (calculated by a linguist) 3) amount of unique and total words used (calculated by a fumbling jack-of-all-trade, let's call him RF)

Your job in this assignment is to double check the data and make sure that it is ready for the analysis proper (Assignment 2), in which we will try to understand how the children's language develops as they grow as a function of cognitive and social factors and which are the "cues" suggesting a likely future language impairment.

1. Let's get started on GitHub
------------------------------

In the assignments you will be asked to upload your code on Github and the GitHub repositories will be part of the portfolio, therefore all students must make an account and link it to their RStudio (you'll thank us later for this!).

Follow the link to one of the tutorials indicated in the syllabus: \* Recommended: <https://happygitwithr.com/> \* Alternative (if the previous doesn't work): <https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN> \* Alternative (if the previous doesn't work): <https://docs.google.com/document/d/1WvApy4ayQcZaLRpD6bvAqhWncUaPmmRimT016-PrLBk/mobilebasic>

N.B. Create a GitHub repository for the Assignment 1 and link it to a project on your RStudio.

2. Now let's take dirty dirty data sets and make them into a tidy one
---------------------------------------------------------------------

If you're not in a project in Rstudio, make sure to set your working directory here. If you created an RStudio project, then your working directory (the directory with your data and code for these assignments) is the project directory.

``` r
pacman::p_load(tidyverse,janitor, dplyr)
```

Load the three data sets, after downloading them from dropbox and saving them in your working directory: \* Demographic data for the participants: <https://www.dropbox.com/s/w15pou9wstgc8fe/demo_train.csv?dl=0> \* Length of utterance data: <https://www.dropbox.com/s/usyauqm37a76of6/LU_train.csv?dl=0> \* Word data: <https://www.dropbox.com/s/8ng1civpl2aux58/token_train.csv?dl=0>

``` r
# Read the data into R
utterance <- read.csv("LU_train.csv")
token<- read.csv("token_train.csv")
demographics <- read.csv("demo_train.csv")
```

Explore the 3 datasets (e.g. visualize them, summarize them, etc.). You will see that the data is messy, since the psychologist collected the demographic data, the linguist analyzed the length of utterance in May 2014 and the fumbling jack-of-all-trades analyzed the words several months later. In particular: - the same variables might have different names (e.g. participant and visit identifiers) - the same variables might report the values in different ways (e.g. participant and visit IDs) Welcome to real world of messy data :-)

Before being able to combine the data sets we need to make sure the relevant variables have the same names and the same kind of values.

So:

2a. Identify which variable names do not match (that is are spelled differently) and find a way to transform variable names. Pay particular attention to the variables indicating participant and visit.

Tip: look through the chapter on data transformation in R for data science (<http://r4ds.had.co.nz>). Alternatively you can look into the package dplyr (part of tidyverse), or google "how to rename variables in R". Or check the janitor R package. There are always multiple ways of solving any problem and no absolute best method.

``` r
# Changing names of mismatching variable names
demographics <- dplyr::rename(demographics, SUBJ=Child.ID)
demographics <- dplyr::rename(demographics, VISIT=Visit)
```

2b. Find a way to homogeneize the way "visit" is reported (visit1 vs. 1).

Tip: The stringr package is what you need. str\_extract () will allow you to extract only the digit (number) from a string, by using the regular expression \\d.

``` r
# Homogenizing the 'VISIT' collumn in utterance and token data with the other data frames
utterance$VISIT <- str_extract(utterance$VISIT, "\\d")
token$VISIT <- str_extract(token$VISIT, "\\d")
```

2c. We also need to make a small adjustment to the content of the Child.ID coloumn in the demographic data. Within this column, names that are not abbreviations do not end with "." (i.e. Adam), which is the case in the other two data sets (i.e. Adam.). If The content of the two variables isn't identical the rows will not be merged. A neat way to solve the problem is simply to remove all "." in all datasets.

Tip: stringr is helpful again. Look up str\_replace\_all Tip: You can either have one line of code for each child name that is to be changed (easier, more typing) or specify the pattern that you want to match (more complicated: look up "regular expressions", but less typing)

``` r
# Removing punctuation from names
demographics$SUBJ <- str_replace_all(demographics$SUBJ, "[[:punct:]]", "")
utterance$SUBJ <- str_replace_all(utterance$SUBJ, "[[:punct:]]", "")
token$SUBJ <- str_replace_all(token$SUBJ, "[[:punct:]]", "")
```

2d. Now that the nitty gritty details of the different data sets are fixed, we want to make a subset of each data set only containig the variables that we wish to use in the final data set. For this we use the tidyverse package dplyr, which contains the function select().

The variables we need are: \* Child.ID, \* Visit, \* Diagnosis, \* Ethnicity, \* Gender, \* Age, \* ADOS,
\* MullenRaw, \* ExpressiveLangRaw, \* Socialization \* MOT\_MLU, \* CHI\_MLU, \* types\_MOT, \* types\_CHI, \* tokens\_MOT, \* tokens\_CHI.

Most variables should make sense, here the less intuitive ones. \* ADOS (Autism Diagnostic Observation Schedule) indicates the severity of the autistic symptoms (the higher the score, the worse the symptoms). Ref: <https://link.springer.com/article/10.1023/A:1005592401947> \* MLU stands for mean length of utterance (usually a proxy for syntactic complexity) \* types stands for unique words (e.g. even if "doggie" is used 100 times it only counts for 1) \* tokens stands for overall amount of words (if "doggie" is used 100 times it counts for 100) \* MullenRaw indicates non verbal IQ, as measured by Mullen Scales of Early Learning (MSEL <https://link.springer.com/referenceworkentry/10.1007%2F978-1-4419-1698-3_596>) \* ExpressiveLangRaw indicates verbal IQ, as measured by MSEL \* Socialization indicates social interaction skills and social responsiveness, as measured by Vineland (<https://cloudfront.ualberta.ca/-/media/ualberta/faculties-and-programs/centres-institutes/community-university-partnership/resources/tools---assessment/vinelandjune-2012.pdf>)

Feel free to rename the variables into something you can remember (i.e. nonVerbalIQ, verbalIQ)

``` r
# Selecting only the relevant collumns
demographics <- select(demographics, SUBJ, VISIT, Diagnosis, Ethnicity, Age, Gender, ADOS, Socialization, ExpressiveLangRaw,MullenRaw)
utterance <- select(utterance, SUBJ, VISIT, MOT_MLU, CHI_MLU)
token <- select(token, SUBJ, VISIT, types_MOT, types_CHI, tokens_MOT, tokens_CHI)
```

2e. Finally we are ready to merge all the data sets into just one.

Some things to pay attention to: \* make sure to check that the merge has included all relevant data (e.g. by comparing the number of rows) \* make sure to understand whether (and if so why) there are NAs in the dataset (e.g. some measures were not taken at all visits, some recordings were lost or permission to use was withdrawn)

``` r
# Merging data into one dataframe
merged_initial <- merge(demographics, token, by=c("SUBJ", "VISIT"), all = T)
merged_final <- merge(merged_initial, utterance, by=c("SUBJ", "VISIT"), all = T)
```

2f. Only using clinical measures from Visit 1 In order for our models to be useful, we want to miimize the need to actually test children as they develop. In other words, we would like to be able to understand and predict the children's linguistic development after only having tested them once. Therefore we need to make sure that our ADOS, MullenRaw, ExpressiveLangRaw and Socialization variables are reporting (for all visits) only the scores from visit 1.

A possible way to do so: \* create a new dataset with only visit 1, child id and the 4 relevant clinical variables to be merged with the old dataset \* rename the clinical variables (e.g. ADOS to ADOS1) and remove the visit (so that the new clinical variables are reported for all 6 visits) \* merge the new dataset with the old

``` r
# Creating 4 new collumns only containing data from visit1
visit1 <- filter(demographics, VISIT==1) %>% select(SUBJ, ADOS, MullenRaw, ExpressiveLangRaw, Socialization) %>% dplyr::rename(ADOS1=ADOS, MullenRaw1=MullenRaw, ExpressiveLangRaw1=ExpressiveLangRaw, Socialization1=Socialization)

# Merging new collumns with final dataframe
merged_final <- merge(merged_final, visit1 , by=c("SUBJ"), all = T)
```

2g. Final touches Now we want to \* anonymize our participants (they are real children!). \* make sure the variables have sensible values. E.g. right now gender is marked 1 and 2, but in two weeks you will not be able to remember, which gender were connected to which number, so change the values from 1 and 2 to F and M in the gender variable. For the same reason, you should also change the values of Diagnosis from A and B to ASD (autism spectrum disorder) and TD (typically developing). Tip: Try taking a look at ifelse(), or google "how to rename levels in R". \* Save the data set using into a csv file. Hint: look into write.csv()

``` r
# Changing names of collumns to something more meaningful
merged_final$Gender <- as.character(merged_final$Gender)
levels(merged_final$Gender) <- c("F","M")
levels(merged_final$Diagnosis) <- c("ASD","TD")


# Anonymzing the data
merged_final$SUBJ <-  as.numeric(as.factor(merged_final$SUBJ))

# Save as csv
write.csv(merged_final, "Merged_data.csv")
```

1.  BONUS QUESTIONS The aim of this last section is to make sure you are fully fluent in the tidyverse. Here's the link to a very helpful book, which explains each function: <http://r4ds.had.co.nz/index.html>

2.  USING FILTER List all kids who:

<!-- -->

1.  have a mean length of utterance (across all visits) of more than 2.7 morphemes.
2.  have a mean length of utterance of less than 1.5 morphemes at the first visit
3.  have not completed all trials. Tip: Use pipes to solve this

``` r
# Kids with mean MLU > 2.7
merged_final %>% group_by(SUBJ) %>% filter(mean(CHI_MLU)>2.7) %>%  select(SUBJ) %>% unique()
```

    ## # A tibble: 9 x 1
    ## # Groups:   SUBJ [9]
    ##    SUBJ
    ##   <dbl>
    ## 1     3
    ## 2     4
    ## 3    13
    ## 4    19
    ## 5    27
    ## 6    44
    ## 7    53
    ## 8    57
    ## 9    64

``` r
# Kids with MLU on visit 1 < 1.5 
merged_final %>%  filter(VISIT=="1") %>% filter(CHI_MLU<1.5) %>% select(SUBJ) %>% unique()
```

    ##    SUBJ
    ## 1     1
    ## 2     5
    ## 3     6
    ## 4     7
    ## 5     9
    ## 6    10
    ## 7    12
    ## 8    13
    ## 9    15
    ## 10   16
    ## 11   18
    ## 12   20
    ## 13   21
    ## 14   22
    ## 15   24
    ## 16   25
    ## 17   26
    ## 18   29
    ## 19   30
    ## 20   31
    ## 21   32
    ## 22   35
    ## 23   36
    ## 24   37
    ## 25   38
    ## 26   39
    ## 27   40
    ## 28   41
    ## 29   42
    ## 30   43
    ## 31   44
    ## 32   46
    ## 33   47
    ## 34   49
    ## 35   51
    ## 36   52
    ## 37   53
    ## 38   54
    ## 39   55
    ## 40   56
    ## 41   58
    ## 42   59
    ## 43   60
    ## 44   61
    ## 45   62
    ## 46   63
    ## 47   65

``` r
# Kids who have not completed all 6 trials sby CHI_MLU) 
merged_final %>% group_by(SUBJ) %>% filter(is.na(CHI_MLU) | n()<6) %>% select(SUBJ) %>% unique() 
```

    ## # A tibble: 18 x 1
    ## # Groups:   SUBJ [18]
    ##     SUBJ
    ##    <dbl>
    ##  1     2
    ##  2     7
    ##  3     8
    ##  4     9
    ##  5    11
    ##  6    18
    ##  7    23
    ##  8    28
    ##  9    40
    ## 10    42
    ## 11    46
    ## 12    47
    ## 13    48
    ## 14    50
    ## 15    52
    ## 16    59
    ## 17    60
    ## 18    66

USING ARRANGE

1.  Sort kids to find the kid who produced the most words on the 6th visit
2.  Sort kids to find the kid who produced the least amount of words on the 1st visit.

``` r
# Aranging after most words on visit 6
merged_final %>% filter(VISIT=="6") %>% arrange(desc(tokens_CHI))
```

    ##    SUBJ VISIT Diagnosis        Ethnicity   Age Gender ADOS Socialization
    ## 1    59     6        TD            White 41.93      1   NA            95
    ## 2    28     6       ASD            White 51.00      1   NA            90
    ## 3    19     6        TD            White 44.07      1   NA           116
    ## 4    27     6        TD            White 42.47      1   NA           103
    ## 5    45     6       ASD            White 57.37      1   NA            77
    ## 6    39     6       ASD            White 58.77      1   NA           116
    ## 7    14     6        TD            White 39.40      1   NA            92
    ## 8    24     6        TD            White 39.23      2   NA           110
    ## 9    15     6        TD            White 39.43      1   NA            83
    ## 10    4     6       ASD     White/Latino 51.37      1   NA            74
    ## 11   44     6        TD            White 40.13      1   NA           101
    ## 12   64     6       ASD            White 54.73      1   NA            97
    ## 13   17     6        TD            White 42.93      1   NA            97
    ## 14   43     6        TD            White 39.93      1   NA           101
    ## 15    2     6       ASD            White 49.70      1   NA            81
    ## 16    1     6        TD            White 40.13      1   NA           100
    ## 17   12     6        TD            White 40.27      1   NA           101
    ## 18   63     6        TD            White 41.00      1   NA            NA
    ## 19   53     6        TD            White 43.40      1   NA           101
    ## 20   13     6        TD            White 41.50      1   NA            97
    ## 21   62     6       ASD            White 46.07      1   NA            79
    ## 22   37     6        TD            White 39.07      1   NA            97
    ## 23   36     6       ASD            White 53.77      1   NA            92
    ## 24   21     6       ASD            White 37.30      1   NA           103
    ## 25   42     6       ASD         Lebanese 46.40      1   NA            94
    ## 26   38     6        TD            White 38.53      1   NA            97
    ## 27    5     6       ASD            White 54.13      1   NA            75
    ## 28   29     6        TD            White 41.17      1   NA           106
    ## 29   30     6        TD            White 39.43      1   NA            95
    ## 30   58     6        TD            White 39.30      1   NA           101
    ## 31    3     6        TD            White 45.07      2   NA            86
    ## 32   56     6        TD            White 43.03      1   NA            97
    ## 33   33     6        TD            White 43.80      1   NA           108
    ## 34   16     6        TD            White 40.30      1   NA            94
    ## 35   55     6        TD            Asian 42.10      2   NA           108
    ## 36   57     6        TD            White 44.43      1   NA           101
    ## 37   52     6       ASD            White    NA      1   NA            63
    ## 38    6     6       ASD      Bangladeshi 46.53      2   NA            74
    ## 39   20     6       ASD            White 56.73      1   NA            72
    ## 40   25     6       ASD     White/Latino 47.50      1   NA            65
    ## 41   65     6       ASD            White 62.33      1   NA           103
    ## 42   31     6       ASD            White 55.17      1   NA            72
    ## 43    8     6        TD            White 40.17      1   NA           108
    ## 44   32     6       ASD            White 56.43      1   NA            85
    ## 45   10     6        TD            White 40.43      1   NA           103
    ## 46   26     6       ASD            White 62.40      1   NA            68
    ## 47   49     6       ASD            White 57.43      1   NA            59
    ## 48   41     6       ASD      White/Asian 53.63      1   NA            63
    ## 49   34     6       ASD            White 54.63      1   NA            66
    ## 50   35     6       ASD African American 46.17      2   NA            83
    ## 51   61     6       ASD            White 61.70      2   NA            68
    ## 52   51     6        TD            White 40.37      2   NA           101
    ## 53   18     6       ASD            White 54.43      1   NA            65
    ## 54   60     6       ASD            White    NA      1   NA            NA
    ## 55   54     6       ASD            White 62.40      1   NA            66
    ## 56   22     6       ASD African American 48.97      1   NA            65
    ## 57    7     6       ASD            White 60.33      2   NA            59
    ## 58    9     6        TD            White    NA      2   NA            NA
    ## 59   40     6        TD            White 40.23      2   NA           114
    ## 60   46     6        TD            White 39.93      1   NA           118
    ##    ExpressiveLangRaw MullenRaw types_MOT types_CHI tokens_MOT tokens_CHI
    ## 1                 38        45       367       260       1731       1294
    ## 2                 46        46       452       273       3076       1249
    ## 3                 50        50       516       235       2576       1079
    ## 4                 39        48       555       237       2895       1010
    ## 5                 45        49       374       219       2227        921
    ## 6                 48        50       429       217       2510        897
    ## 7                 41        47       388       210       1959        864
    ## 8                 40        43       478       221       2044        847
    ## 9                 37        44       359       178       1802        793
    ## 10                44        44       410       166       2171        738
    ## 11                46        46       357       219       1764        719
    ## 12                50        50       505       226       3072        713
    ## 13                46        49       491       250       2264        710
    ## 14                36        45       397       213       1741        702
    ## 15                48        48       304       245        999        698
    ## 16                44        42       595       210       2586        686
    ## 17                27        42       260       168       1138        666
    ## 18                NA        NA       303       158       1460        659
    ## 19                48        45       383       217       1701        646
    ## 20                47        44       339       201       1498        640
    ## 21                30        45       335       197       2221        618
    ## 22                34        45       411       156       2438        611
    ## 23                32        44       342       157       1540        605
    ## 24                48        43       433       155       2389        590
    ## 25                37        41       417       170       2508        571
    ## 26                31        46       324       165       1978        568
    ## 27                37        40       462       179       3182        538
    ## 28                35        43       385       154       1788        530
    ## 29                32        39       391       164       1889        521
    ## 30                41        41       437       183       2347        517
    ## 31                45        45       486       173       2564        460
    ## 32                44        47       548       163       2887        436
    ## 33                45        45       475       185       2363        418
    ## 34                40        40       327       156       1395        410
    ## 35                40        46       383       140       1866        395
    ## 36                36        45       249       102       1024        358
    ## 37                21        32       319        98       1565        306
    ## 38                26        39       158        66        536        300
    ## 39                32        30       256        55        995        274
    ## 40                18        30       372        47       1748        236
    ## 41                31        41       311        58       1481        210
    ## 42                21        39       454        52       2391        204
    ## 43                39        43       322        64       1352        197
    ## 44                22        49       425       101       2219        195
    ## 45                33        33       400        73       2271        189
    ## 46                12        30       309        62       1349        143
    ## 47                10        32       351        10       1918        137
    ## 48                29        30       330        55       1987        110
    ## 49                10        28       585        12       2202        100
    ## 50                18        33       326        14       1852         79
    ## 51                 9        32       444         2       2450         64
    ## 52                46        43       322        34       1371         61
    ## 53                11        27       281         3       1454         37
    ## 54                17        34       284         6       1390         36
    ## 55                11        24       370         4       1396          8
    ## 56                16        26       388         2       2077          2
    ## 57                27        32        NA        NA         NA         NA
    ## 58                NA        NA        NA        NA         NA         NA
    ## 59                43        49        NA        NA         NA         NA
    ## 60                42        40        NA        NA         NA         NA
    ##     MOT_MLU   CHI_MLU ADOS1 MullenRaw1 ExpressiveLangRaw1 Socialization1
    ## 1  3.957230 2.9092742     0         26                 17             96
    ## 2  4.111413 3.3643411    11         32                 33            100
    ## 3  4.013353 2.9095355     0         29                 33            106
    ## 4  4.472993 3.0614525     0         29                 22             90
    ## 5  4.250853 2.6794872    14         42                 27             65
    ## 6  4.240798 3.0774194     7         33                 26             70
    ## 7  4.847418 3.7011952     0         25                 17            102
    ## 8  4.211321 3.0911950     0         21                 19            106
    ## 9  4.664286 3.8114035     0         23                 17             92
    ## 10 3.532374 3.2781955     8         31                 27             82
    ## 11 3.391525 3.0727969     0         24                 19             96
    ## 12 4.080446 3.4415584    13         30                 30             87
    ## 13 4.445872 2.9482072     0         29                 26            102
    ## 14 4.061475 2.8526316     0         25                 17            102
    ## 15 4.588477 3.4135021    13         34                 27             85
    ## 16 4.664013 2.8651685     0         28                 14            108
    ## 17 4.287582 2.7571429     0         21                 15            106
    ## 18 4.113158 2.8480000     4         29                 22            108
    ## 19 5.247093 3.5950000     0         27                 27            108
    ## 20 4.468493 3.5049505     0         30                 16            104
    ## 21 3.320000 2.1553398    15         30                 24             88
    ## 22 4.239198 2.3815789     1         23                 21            102
    ## 23 3.370937 2.2745098    10         27                 22             82
    ## 24 5.379798 2.9027778     9         26                 14             86
    ## 25 3.926928 2.1586207    13         27                 13             86
    ## 26 4.388235 2.5614754     3         19                 13             98
    ## 27 4.587179 2.7665198     9         34                 27             82
    ## 28 4.254505 2.4800000     0         26                 18             96
    ## 29 4.239437 2.9607843     0         20                 16            102
    ## 30 4.186161 2.8921569     1         24                 22            115
    ## 31 5.229885 3.7103448     1         29                 18             88
    ## 32 5.587332 2.4292929     0         29                 22             94
    ## 33 3.603473 2.0717489     0         27                 22            100
    ## 34 4.347709 2.8690476     0         24                 15             86
    ## 35 5.153639 2.7612903     1         22                 14            102
    ## 36 3.706790 3.2439024     0         30                 30             98
    ## 37 3.370093 1.4734513    17         28                 10             82
    ## 38 2.483146 1.4967320    17         20                 17             68
    ## 39 3.156695 2.1450382    11         28                 20             88
    ## 40 3.534173 1.3052632    14         25                 19             65
    ## 41 3.514403 1.4012346    15         27                 16             79
    ## 42 4.349353 1.2883436    17         26                 14             72
    ## 43 4.366972 2.2258065     5         32                 31            102
    ## 44 4.341549 1.0843373    12         31                 13             70
    ## 45 4.235585 2.7051282     3         27                 18            104
    ## 46 3.860795 1.1680000    20         13                 11             67
    ## 47 3.460548 1.1610169    20         21                  9             75
    ## 48 4.100707 1.6447368    11         26                 19             88
    ## 49 4.003257 2.6315789    21         21                  9             69
    ## 50 3.965392 0.7536232    14         25                 11             76
    ## 51 3.241422 0.0156250    15         28                 10             66
    ## 52 4.676101 2.7600000     0         27                 20            102
    ## 53 3.650235 1.0000000    14         25                 11             74
    ## 54 3.474725 1.0588235    14         27                 11             77
    ## 55 3.886842 1.3333333    19         17                 10             64
    ## 56 3.943636 0.5000000    21         22                  8             72
    ## 57       NA        NA    18         24                 14             65
    ## 58       NA        NA     0         24                 18            100
    ## 59       NA        NA     1         29                 28            104
    ## 60       NA        NA     3         30                 20             94

``` r
#Aranging after least words on visit 1
merged_final %>% filter(VISIT=="6") %>% arrange(tokens_CHI)
```

    ##    SUBJ VISIT Diagnosis        Ethnicity   Age Gender ADOS Socialization
    ## 1    22     6       ASD African American 48.97      1   NA            65
    ## 2    54     6       ASD            White 62.40      1   NA            66
    ## 3    60     6       ASD            White    NA      1   NA            NA
    ## 4    18     6       ASD            White 54.43      1   NA            65
    ## 5    51     6        TD            White 40.37      2   NA           101
    ## 6    61     6       ASD            White 61.70      2   NA            68
    ## 7    35     6       ASD African American 46.17      2   NA            83
    ## 8    34     6       ASD            White 54.63      1   NA            66
    ## 9    41     6       ASD      White/Asian 53.63      1   NA            63
    ## 10   49     6       ASD            White 57.43      1   NA            59
    ## 11   26     6       ASD            White 62.40      1   NA            68
    ## 12   10     6        TD            White 40.43      1   NA           103
    ## 13   32     6       ASD            White 56.43      1   NA            85
    ## 14    8     6        TD            White 40.17      1   NA           108
    ## 15   31     6       ASD            White 55.17      1   NA            72
    ## 16   65     6       ASD            White 62.33      1   NA           103
    ## 17   25     6       ASD     White/Latino 47.50      1   NA            65
    ## 18   20     6       ASD            White 56.73      1   NA            72
    ## 19    6     6       ASD      Bangladeshi 46.53      2   NA            74
    ## 20   52     6       ASD            White    NA      1   NA            63
    ## 21   57     6        TD            White 44.43      1   NA           101
    ## 22   55     6        TD            Asian 42.10      2   NA           108
    ## 23   16     6        TD            White 40.30      1   NA            94
    ## 24   33     6        TD            White 43.80      1   NA           108
    ## 25   56     6        TD            White 43.03      1   NA            97
    ## 26    3     6        TD            White 45.07      2   NA            86
    ## 27   58     6        TD            White 39.30      1   NA           101
    ## 28   30     6        TD            White 39.43      1   NA            95
    ## 29   29     6        TD            White 41.17      1   NA           106
    ## 30    5     6       ASD            White 54.13      1   NA            75
    ## 31   38     6        TD            White 38.53      1   NA            97
    ## 32   42     6       ASD         Lebanese 46.40      1   NA            94
    ## 33   21     6       ASD            White 37.30      1   NA           103
    ## 34   36     6       ASD            White 53.77      1   NA            92
    ## 35   37     6        TD            White 39.07      1   NA            97
    ## 36   62     6       ASD            White 46.07      1   NA            79
    ## 37   13     6        TD            White 41.50      1   NA            97
    ## 38   53     6        TD            White 43.40      1   NA           101
    ## 39   63     6        TD            White 41.00      1   NA            NA
    ## 40   12     6        TD            White 40.27      1   NA           101
    ## 41    1     6        TD            White 40.13      1   NA           100
    ## 42    2     6       ASD            White 49.70      1   NA            81
    ## 43   43     6        TD            White 39.93      1   NA           101
    ## 44   17     6        TD            White 42.93      1   NA            97
    ## 45   64     6       ASD            White 54.73      1   NA            97
    ## 46   44     6        TD            White 40.13      1   NA           101
    ## 47    4     6       ASD     White/Latino 51.37      1   NA            74
    ## 48   15     6        TD            White 39.43      1   NA            83
    ## 49   24     6        TD            White 39.23      2   NA           110
    ## 50   14     6        TD            White 39.40      1   NA            92
    ## 51   39     6       ASD            White 58.77      1   NA           116
    ## 52   45     6       ASD            White 57.37      1   NA            77
    ## 53   27     6        TD            White 42.47      1   NA           103
    ## 54   19     6        TD            White 44.07      1   NA           116
    ## 55   28     6       ASD            White 51.00      1   NA            90
    ## 56   59     6        TD            White 41.93      1   NA            95
    ## 57    7     6       ASD            White 60.33      2   NA            59
    ## 58    9     6        TD            White    NA      2   NA            NA
    ## 59   40     6        TD            White 40.23      2   NA           114
    ## 60   46     6        TD            White 39.93      1   NA           118
    ##    ExpressiveLangRaw MullenRaw types_MOT types_CHI tokens_MOT tokens_CHI
    ## 1                 16        26       388         2       2077          2
    ## 2                 11        24       370         4       1396          8
    ## 3                 17        34       284         6       1390         36
    ## 4                 11        27       281         3       1454         37
    ## 5                 46        43       322        34       1371         61
    ## 6                  9        32       444         2       2450         64
    ## 7                 18        33       326        14       1852         79
    ## 8                 10        28       585        12       2202        100
    ## 9                 29        30       330        55       1987        110
    ## 10                10        32       351        10       1918        137
    ## 11                12        30       309        62       1349        143
    ## 12                33        33       400        73       2271        189
    ## 13                22        49       425       101       2219        195
    ## 14                39        43       322        64       1352        197
    ## 15                21        39       454        52       2391        204
    ## 16                31        41       311        58       1481        210
    ## 17                18        30       372        47       1748        236
    ## 18                32        30       256        55        995        274
    ## 19                26        39       158        66        536        300
    ## 20                21        32       319        98       1565        306
    ## 21                36        45       249       102       1024        358
    ## 22                40        46       383       140       1866        395
    ## 23                40        40       327       156       1395        410
    ## 24                45        45       475       185       2363        418
    ## 25                44        47       548       163       2887        436
    ## 26                45        45       486       173       2564        460
    ## 27                41        41       437       183       2347        517
    ## 28                32        39       391       164       1889        521
    ## 29                35        43       385       154       1788        530
    ## 30                37        40       462       179       3182        538
    ## 31                31        46       324       165       1978        568
    ## 32                37        41       417       170       2508        571
    ## 33                48        43       433       155       2389        590
    ## 34                32        44       342       157       1540        605
    ## 35                34        45       411       156       2438        611
    ## 36                30        45       335       197       2221        618
    ## 37                47        44       339       201       1498        640
    ## 38                48        45       383       217       1701        646
    ## 39                NA        NA       303       158       1460        659
    ## 40                27        42       260       168       1138        666
    ## 41                44        42       595       210       2586        686
    ## 42                48        48       304       245        999        698
    ## 43                36        45       397       213       1741        702
    ## 44                46        49       491       250       2264        710
    ## 45                50        50       505       226       3072        713
    ## 46                46        46       357       219       1764        719
    ## 47                44        44       410       166       2171        738
    ## 48                37        44       359       178       1802        793
    ## 49                40        43       478       221       2044        847
    ## 50                41        47       388       210       1959        864
    ## 51                48        50       429       217       2510        897
    ## 52                45        49       374       219       2227        921
    ## 53                39        48       555       237       2895       1010
    ## 54                50        50       516       235       2576       1079
    ## 55                46        46       452       273       3076       1249
    ## 56                38        45       367       260       1731       1294
    ## 57                27        32        NA        NA         NA         NA
    ## 58                NA        NA        NA        NA         NA         NA
    ## 59                43        49        NA        NA         NA         NA
    ## 60                42        40        NA        NA         NA         NA
    ##     MOT_MLU   CHI_MLU ADOS1 MullenRaw1 ExpressiveLangRaw1 Socialization1
    ## 1  3.943636 0.5000000    21         22                  8             72
    ## 2  3.886842 1.3333333    19         17                 10             64
    ## 3  3.474725 1.0588235    14         27                 11             77
    ## 4  3.650235 1.0000000    14         25                 11             74
    ## 5  4.676101 2.7600000     0         27                 20            102
    ## 6  3.241422 0.0156250    15         28                 10             66
    ## 7  3.965392 0.7536232    14         25                 11             76
    ## 8  4.003257 2.6315789    21         21                  9             69
    ## 9  4.100707 1.6447368    11         26                 19             88
    ## 10 3.460548 1.1610169    20         21                  9             75
    ## 11 3.860795 1.1680000    20         13                 11             67
    ## 12 4.235585 2.7051282     3         27                 18            104
    ## 13 4.341549 1.0843373    12         31                 13             70
    ## 14 4.366972 2.2258065     5         32                 31            102
    ## 15 4.349353 1.2883436    17         26                 14             72
    ## 16 3.514403 1.4012346    15         27                 16             79
    ## 17 3.534173 1.3052632    14         25                 19             65
    ## 18 3.156695 2.1450382    11         28                 20             88
    ## 19 2.483146 1.4967320    17         20                 17             68
    ## 20 3.370093 1.4734513    17         28                 10             82
    ## 21 3.706790 3.2439024     0         30                 30             98
    ## 22 5.153639 2.7612903     1         22                 14            102
    ## 23 4.347709 2.8690476     0         24                 15             86
    ## 24 3.603473 2.0717489     0         27                 22            100
    ## 25 5.587332 2.4292929     0         29                 22             94
    ## 26 5.229885 3.7103448     1         29                 18             88
    ## 27 4.186161 2.8921569     1         24                 22            115
    ## 28 4.239437 2.9607843     0         20                 16            102
    ## 29 4.254505 2.4800000     0         26                 18             96
    ## 30 4.587179 2.7665198     9         34                 27             82
    ## 31 4.388235 2.5614754     3         19                 13             98
    ## 32 3.926928 2.1586207    13         27                 13             86
    ## 33 5.379798 2.9027778     9         26                 14             86
    ## 34 3.370937 2.2745098    10         27                 22             82
    ## 35 4.239198 2.3815789     1         23                 21            102
    ## 36 3.320000 2.1553398    15         30                 24             88
    ## 37 4.468493 3.5049505     0         30                 16            104
    ## 38 5.247093 3.5950000     0         27                 27            108
    ## 39 4.113158 2.8480000     4         29                 22            108
    ## 40 4.287582 2.7571429     0         21                 15            106
    ## 41 4.664013 2.8651685     0         28                 14            108
    ## 42 4.588477 3.4135021    13         34                 27             85
    ## 43 4.061475 2.8526316     0         25                 17            102
    ## 44 4.445872 2.9482072     0         29                 26            102
    ## 45 4.080446 3.4415584    13         30                 30             87
    ## 46 3.391525 3.0727969     0         24                 19             96
    ## 47 3.532374 3.2781955     8         31                 27             82
    ## 48 4.664286 3.8114035     0         23                 17             92
    ## 49 4.211321 3.0911950     0         21                 19            106
    ## 50 4.847418 3.7011952     0         25                 17            102
    ## 51 4.240798 3.0774194     7         33                 26             70
    ## 52 4.250853 2.6794872    14         42                 27             65
    ## 53 4.472993 3.0614525     0         29                 22             90
    ## 54 4.013353 2.9095355     0         29                 33            106
    ## 55 4.111413 3.3643411    11         32                 33            100
    ## 56 3.957230 2.9092742     0         26                 17             96
    ## 57       NA        NA    18         24                 14             65
    ## 58       NA        NA     0         24                 18            100
    ## 59       NA        NA     1         29                 28            104
    ## 60       NA        NA     3         30                 20             94

USING SELECT

1.  Make a subset of the data including only kids with ASD, mlu and word tokens
2.  What happens if you include the name of a variable multiple times in a select() call?

``` r
ads_mlu_tokens <- merged_final %>% filter(Diagnosis=="ASD") %>% select(SUBJ, VISIT, Diagnosis, CHI_MLU, tokens_CHI)
```

USING MUTATE, SUMMARISE and PIPES 1. Add a column to the data set that represents the mean number of words spoken during all visits. 2. Use the summarise function and pipes to add an column in the data set containing the mean amount of words produced by each trial across all visits. HINT: group by Child.ID 3. The solution to task above enables us to assess the average amount of words produced by each child. Why don't we just use these average values to describe the language production of the children? What is the advantage of keeping all the data?

``` r
# Adding collumn with mean MLU (with NAs marked as O) per visit
mean_tokens_CHI <- aggregate(merged_final[, 14], list(merged_final$SUBJ), mean, na.rm=TRUE)
mean_tokens_CHI <- dplyr::rename(mean_tokens_CHI, SUBJ=Group.1, mean_tokens_CHI=x)
merged_final_2 <- merge(merged_final, mean_tokens_CHI , by=c("SUBJ"), all = T)

# We are throwing away valuable data regagrding the variance in the data
```
