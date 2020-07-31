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

In order to compare the ratings of users for each movie we created a pivot table. From here we applied the normalization described earlier and set all cells without ratings to 0 (which is the user's average rating).

leveraging scipy we turned our pivot table into a sparse matrix. Finally, we fit our sparse matrix to a KNN model with which to use in finding similar users.

The core of our engine is around 3 actions. Given a user and model that has been trained, our engine uses the suggest_movie funtion, which itself calls the get_new_movies and score_movie functions, in order to return a rated list of movies the user who has never seen, sorted by highest predicted rating.

get_new_movies uses the model to find at least 5 neighbors to compare movies to. The function will return at least 10 movies that the user has not rated. Should the initial 5 neighbors not have at least 10 new movies for our use, the process is repeated with more neighbors until 10 titles are found.

score_movie takes in a userId and movie title. It creates a sparse matrix for only users who have seen the movie; this takes care of the cases where you don't know how many neighbors you need to choose for a user in order to get the sufficient amount who have seen the specific movie in order to score it. It will use this matrix to find the 5 users who have seen the movie closest to the given user, take the mean of their weighted rating, and then add our user's average rating back to the mean to give an unweighted prediction.

After enacting get_new_movies and score_movie, suggest_movies sorts all of the new movies by predicted rating and outputs the top 5.

# Testing

In order to set a baseline so that we can test future improvements we wanted to create a testing metric for our ratings. We thought a good way to begin would be to compare predicted ratings for a movie against what the actual ratings were. As a metric, we chose to use RMSE.

out get_rmse function takes in a movie title. It creates a data from of only users who have seen the title, then takes their rating of the movie. It then creates predictive scores using the score_movie function (note: this function takes care to NOT include the user's score if the user has already seen the movie). Finally, we compare our predictions with the actual ratings by using Scikit Learn's mean_squared_error function and taking the square root of the result.

# Conculsion

Our engine can give movie suggestions for a user that they have never seen before. As well, because the functions are loosely coupled they are able to be used modularly and are easily able to be scored.

By mimicking how new information is discovered in real life, we were able to create our own engine to model that process. Our engine can empoyer people to discover new movies on their own, without having to go through the burden of making friends.
