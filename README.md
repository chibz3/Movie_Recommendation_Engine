# Using Collaborative Filtering to Make New Movie Recommendations

## <em>A recommendation and prediction engine to guide users to new movies</em>

Most people find out about new movies from friends [citation needed], people who are likely to be similar to themselves. While data science can't make people friends (yet) it can attempt to mimic certain aspects of friendship when it comes to discovering new movies.

Through looking at a user's movie preferences, we were able to find other users who have similar preferences, and then recommend movies our original user has never seen. As well, we were able to use the scores of similar users to predict our user's score, obtaining a rmse score around 0.88 (with ratings on a scale of 0.5 - 5)

# Data Sourcing:

This dataset describes 5-star rating and free-text tagging activity from MovieLens, a movie recommendation service. It contains 100836 ratings and 3683 tag applications across 9742 movies. These data were created by 610 users between March 29, 1996 and September 24, 2018. This dataset was generated on September 26, 2018.

To obtain the dataset to code along, [download it here](https://grouplens.org/datasets/movielens/25m/)

For more information on this dataset, visit its description [here.](http://files.grouplens.org/datasets/movielens/ml-latest-small-README.html)

# Data Cleaning, Exploratory Data Analysis, Feature Selection, and Feature Engineering

Thankfully, this dataset was very clean, however, it did require a fair amount of configuring in order to make it useful for our purposes.

In exploring our data we first looked for any trends in the genres present. The most popular genre was 'Drama' which related to 4361 movies, followed by 'Comedy' which related to 3756 movies. Also among the top 5 (though much less popular) were Thriller, Action, and Romance relating to 1894, 1828, and 1596 movies respectively.

Next, we investigated the distribution of ratings among movies. A rating of 4 was by far the most popular rating (26.8k movies), being just over twice as frequent as a rating of 5 (13.2k movies). Graphing the frequency each rating was given, it was easy to see that people generally rated movies more favorably. As this could mean that ratings for users were inconsistent we devised a way to equalize each user's ratings.

<p align='center'>
<img src='images/.png'>
</p>

To create normalized weighted ratings we computed each user's mean rating and then subtracted this average from each rating - resulting in below-average ratings being negative amounts and above-average ratings being positive amounts.

# Engines:

## Surprise

Initally we used Surprise, a scikit building and analyzing recommender systems. With Surprise we were quickly able to setup a system where we could predict a user's rating, and recommend movies for them.

However, we found fine tuning the parameters in Surprise to be cumbersome and didn't feel like we had enough insight into the processes working under the hood.

While surprise would be very useful for the data scientist who needs to make a rec engine in a hurry (and somehow has their data already in order), we decided to make our own system.

## Our Engine
