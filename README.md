# Survey.Weight.Example
load(url("http://knutur.at/wsmt/R/RData/small.RData"))
# https://www.r-bloggers.com/survey-computing-your-own-post-stratification-weights-in-r/
install.packages("survey")
library(survey)
small
small.svy.unweighted <- svydesign(ids=~1, data=small)

# Below has the population percentages for the variables in the real world
sex.dist <- data.frame(sex = c("M", "F"),
                       Freq = nrow(small) * c(0.45, 0.55))
edu.dist <- data.frame(edu = c("Lo", "Mid", "Hi"),
                       Freq = nrow(small) * c(0.30, 0.50, 0.20))

# This combines the weights from the population with the sample distribution from the survey 
small.svy.rake <- rake(design = small.svy.unweighted,
                       sample.margins = list(~sex, ~edu),
                       population.margins = list(sex.dist, edu.dist))

summary(weights(small.svy.rake))
# This trims the weights so they don't go below .3 or above 3
small.svy.rake.trim <- trimWeights(small.svy.rake, lower=0.3, upper=3,
                                   strict=TRUE)
# Below get weighted sample statistics for the variables
# There is more here: http://www.ats.ucla.edu/stat/r/faq/svy_r_post.htm

svymean(~emo, small.svy.rake)

