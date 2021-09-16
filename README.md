# Movie-Recommender-systems-and-big-data-in-Matlab

In this project we analyze movie dataset with datastore and tall tables, we use collaborative filtering recommender algorithms with sparse  matrices with algorithms [fmincg](recommender-coding/fmincg), and [cofiGrad](recommender-coding/cofiGrad), and observe their differences, and finally we compare and conclude results with both approaches.  Here, the recommender system is based on a movie set of data with a list of movies and users with rankings. Once a new user inputs a few movie rankings, the algorithm will recommend the movies to watch.



**1. INTRODUCTION** 

This project is based on Coursega Stanford Machine Learning Andrew Ng course. Most of the code has been taken from Week 9 examples, with some changes.

Recommender Systems help find the product that could probably buy a customer, or suggest product to customers, or on youTube, uses it to decide which video to play next on autoplay, or Facebook uses it to recommend pages to like and people to follow. It is based on data of users and products. 

There are three types of recommender systems:

A. Demographic Filtering- They offer generalized recommendations to every user, based on movie popularity and/or genre. The System recommends the same movies to users with similar demographic features. Since each user is different , this approach is considered to be too simple. The basic idea behind this system is that movies that are more popular and critically acclaimed will have a higher probability of being liked by the average audience.

B. Content Based Filtering- They suggest similar items based on a particular item. This system uses item metadata, such as genre, director, description, actors, etc. for movies, to make these recommendations. The general idea behind these recommender systems is that if a person liked a particular item, he or she will also like an item that is similar to it.

C. Collaborative Filtering- This system matches persons with similar interests and provides recommendations based on this matching. Collaborative filters do not require item metadata like its content-based counterparts.

In [wikipedia](https://en.wikipedia.org/wiki/Recommender_system) you can find mainly content based filtering and collaborative filtering, plus some others recommender systems.

Here, with MATLAB, we will extract and analyze ordinary data and  big movie data with datastore and tall tables, and explore two types of colaborative filtering recommender systems, one with   algorithm [fmincg](recommender-coding/fmincg) using the function [cofiCostFunc](recommender-coding/cofiCostFunc), and another with  algorithm [cofiGrad](recommender-coding/cofiGrad), and observe their differences.

**2. EXTRACT**

The extract dataset comes from grouplens page at  "http://files.grouplens.org/datasets/movielens/" and use "ml-10m.zip" for data analysis and 'ml-lastest-small' for recommender systems. With the 'ml-10m.zip', the  dataset contains 10,000,054 ratings and 95580 tags applied to 10681 movies by 71567 users of the online movie recommender service MovieLens.  Unlike previous MovieLens data sets, no demographic information is included. Each user is represented by an id, and no other information is provided. The data are contained in three files, movies.dat, ratings.dat and tags.dat. Also included are scripts for generating subsets of the data to support five-fold cross-validation of rating predictions. 

The extraction downloads data into a zip file, unzip the file to have on disk the files "movies.dat" and "ratings.dat". The "movies.dat" file is passed to a database table "moviesTbl" and the "rating.dat" file is passed to a database tall table "ratingsTbl".
##### Working with the ratings data
While the movie data was small enough to use an ordinary table variable, the ratings data is typically much larger. In this section we outline methods for importing and working with 'out-of-memory' data.
##### Create a datastore to interface with ratings file
Because of the prohibitive size of the ratings data, we will not import it directly into an ordinary in-memory variable like table or array. Instead, we will interface with the file via a datastore. A datastore provides a way to access data in a file or collection of files with the same structure without loading it all into memory. A datastore eliminates the need to write custom file I/O routines to import or export data. The type of datastore used depends on the type of data in the files and how it is organized- see the documentation for more information. For the current example, we will create a tabularTextDatastore to interface with ratings.csv.

You can find the MATLAB code for EXTRACT in [here](recommender-coding/extract).

**3. MANIPULATE A TABLE**

The "movie.dat" table is a small dataset and there is no need to make it a tall table. First, we find out there are 10681 movies. Then, we extract from title the release year of the movie and put in a year array. The distribution of the movies dataset by released year is as follows:

![image](https://user-images.githubusercontent.com/53232113/119567726-48c93780-bd72-11eb-9019-a1b00b50f99a.png)

The histogram shows the number of movies per release year in the dataset. We find that the most number of movies are created between 2003 and 2005 with aproximately 1200 movies, in 1980 there are about 200, and after 2010 there are no movies in the dataset.
Then, we create a new variable "year" as to have the released  year separately from the title.  

##### Extract the movie genres
The movie genres are contained in moviesTbl as the string variable, genre, which lists all genres that apply to each movie separated by a '|'. Since many movies belong to multiple genres, it is not possible to map this information to a single numeric or categorical value without creating a unique category for all observed combinations. Instead we will map each genre to a logical variable, then the genre variables to moviesTbl. A 1 in a given row of a genre variable will indicate that the movie in that row belongs to that genre. 

You can find the MATLAB code for "MANIPULATE A TABLE" in [here](recommender-coding/manipulate-table).
   
   

**4. MANIPULATE BIG DATA WITH DATASTORE AND TALL TABLE**

##### Working with tall variables
Unlike computations with in-memory data types, computations involving tall datatypes are not evaluated immediately.  When using gather(...) then the results are real.

With commands
```
nr = height(ratingTbl)
gather(nr)
```
we see there are 10'000.054 ratings. 
With commands
```
counts = histcounts(ratingsTbl.userId,'BinMethod','Integers')
counts = gather(counts)
```
we see there are  7.158 userIDs.
With command
```
globalMean = gather(mean(ratingsTbl.rating));
```
the global mean of dataset is 3.51.
The histogram for user ratings is:

![image](https://user-images.githubusercontent.com/53232113/119584889-31e50e00-bd8f-11eb-95ae-c2c48471accc.png)
We can see that about 2.5 millon ratings are 3.0, about 2.9 millon are 4.0, and look like the average rating is 3.5.
The time to plot this histogram is 24 seconds.  
You can find the MATLAB code for "MANIPULATE BIG DATA WITH DATASTORE AND TALL TABLE" in [here](recommender-coding/MANIPULATE-BIG-DATA).
   

**5. COMBINE TABLES AND ANALYZE BIG DATA**

##### Combine the rating and movie tables
While keeping a separate dictionary table like moviesTbl would usually save memory - as the title and genre information is not repeated for every rating of that movie - this advantage is largely negated when working with out-of-memory data. Therefore we will combine the data in ratingsTbl and moviesTbl by joining them on their common variable, movieId, using the join function. (This is different than the join method for string variables used earlier.) By joining the two tables, computations involving information from both tables will be simplified.

##### Left joins
If you haves previously worked with databases, you are probably already familiar with join operations as they are commonly used for combining and working with data stored in multiple databases. There are many different types of joins- the one discussed below is referred to as a left-join. As stated above we wish to form a single table that combines the information in ratings table and the movie dictionary table to simplify analysis that uses information from each. In the present case:
Both ratingsTbl and moviesTbl share a common or 'key' variable, movieId.
moviesTbl only contains a single row for each unique movieId value.
ratingsTbl contains multiple rows for most movieId values as most of the movies have been reviewed multiple times.
By left-joining the ratings table with the dictionary table, we will effectively of 'broadcast' a copy of each non-key row of moviesTbl to the corresponding rows of ratingsTbl that share the same movie ID. Any movies listed in moviesTbl whose ID is not contained in ratingsTbl (i.e. the movie was in the dictionary but not rated) will be ignored. The resulting table will contain the same number of rows as ratingsTbl with the additional title and genre information carried over from moviesTbl. See the documentation for more information on joining tables using join.

##### Joining a tall table
Because ratingsTbl is a tall table, the additional information from moviesTbl will be added to the ratings data automatically as chunks of data are read in from the csv file. This is different from an in-memory table where this information would be immediately added to the table variable (increasing its memory footprint). Also, no data (additional columns) will be added to the original data file, ratings.csv. 

To join ratingsTbl and moviesTbl on movieId you use the following code:
```
ratingsTbl = join(ratingsTbl,moviesTbl)
```
##### Obtain a list of unique movie ID's and update the ID's in ratingsTbl 
The ratings dataset contains movie ratings organized by user, so that the dataset contains at least one rating from each user ID from 1 to the number of unique users in the dataset, nu. However, the same is not true for movie ID's as some movies in the original dataset were not rated by any user in this subset, and thus there are 'gaps' in the set of movie ID's that appear in ratingsTbl. To simplify later analysis, we use the findgroups function below to assign a new movie ID to each movie present in ratingsTbl, such that the movie ID's then form a contiguous set of integers from 1 to the number of unique movies in the dataset, nm. Running the code  to update the ratings table with the new movie IDs we obtain 10677 unique movies in the dataset. On the movie dataset "movieTbl" we have 10681 movies, three more than the ratings dataset.

##### Identify the most reviewed movie
Run the code below to obtain the number of ratings for each movie in movieId and store it in the variable numRatings, then find the most reviewed movie in the dataset. The distribution of ratings/movie is also visualized. 
```
numRatings = gather(histcounts(ratingsTbl.movieId,'BinMethod','integers'));
[count,ind] = max(numRatings);
ttl = gather(ratingsTbl.title(find(ratingsTbl.movieId==ind,1)));
fprintf('The most reviewed movie in the dataset is ''%s'' with %d reviews',ttl, count);
```
The most reviewed movie in the dataset is 'Pulp Fiction (1994)' with 34864 reviews

Calculate and plot the mean rating of movies by release year

Grouping functions like grpstats and findgroups can be applied on tall data with the additional step that their outputs must be gathered. Running this code  calculates the mean movie rating by release year and the total number of ratings for movies released that year. The results are then plotted by year on the same axis using the yyaxis function before being plotted vs each other: 


![image](https://user-images.githubusercontent.com/53232113/119593749-1fbf9b80-bda0-11eb-8529-f49a8a80f350.png)

This plot shows that the average rating is between 3.3 to 4.0 from years 1900 to 1980, and from 1980 to 2020 it is about 3.4. Also, the number of ratings are less than 100000 between years 1900 to 1980, from 1990 to 2000 it is about 600000, and from 2000 to 2020 decreases from 600000 to zero.  

You can find the MATLAB code for "COMBINE TABLES AND ANALYZE BIG DATA" in [here](recommender-coding/combine-analize-tables).

**6. Extract small dataset**
For the 'ml-latest-small' dataset, it contains 100836 ratings and 3683 tag applications across 9742 movies. These data were created by 610 users between March 29, 1996 and September 24, 2018. This dataset was generated on September 26, 2018. Users were selected at random for inclusion. All users selected had rated at least 20 movies. The data are contained in the files links.csv, movies.csv, ratings.csv and tags.csv.
You can find the MATLAB code for "Extract small dataset" in [here](recommender-coding/extract-small).

**7. COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH 'fmincg' ALGORITHM**
The input to get the predicted matrix for my ratings is the Y sparse matrix and a matrix R which contains a zero if there is no rating, or one if there is a value. With the inputs matrix Y and matrix R,  and some variables , process fmincg algorithm and  we get the prediction matrix p, sort it and print my best predicted movies.

For that, the process is as follows:
1. get userid, movieId and ratings from table ratingsTbl.
2. create de sparse matrix Y.
3. create R matrix which contains one only in values that has ratings
4. get my_ratings from function my_ratings_action 
5. normalize into Ynorm
6. create R matrix
7. get X and Theta out of fmincg algorithm
8. get prediction matrix p by multiplying X * Theta
9. get my predictions out of the first column or p matrix
10. sort my predictions in descending order so as to see first the movies with the best predicting rating
11. print predicted movies and original ratings

You can find the MATLAB code for "COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH fmincg ALGORITHM" in [here](recommender-coding/recommender-normal). And your can check my movie ratings in [here](recommender-coding/my-ratings-action)
The result and predictions:
##### My predictions:
![image](https://user-images.githubusercontent.com/53232113/120266438-842e9f00-c267-11eb-80f8-099ca1fefd35.png)
##### Results
The recommender movies for my ratings are correct. In this list we find that most of the movies have my taste which are mainly action movies. The time to process fmincg algorithm for data up to 100000  ratings is less than one minute and, for data with 10 millon ratings on the 'ml-10m.zip' dataset from movielens  is quite slow and ends up collapsing Matlab.

**8. COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH 'cofiGrad' algorithm**

The input to get the predicted matrix for my ratings is the R sparse matrix. With the input, we process the codiGraf algorithm and  get the prediction matrix p, sort it and print my best predicted movies.

For that, the process is as follows:
1. get userid, movieId and ratings from table ratingsTbl.
2. create de sparse matrix R.
3. get my_ratings from function my_ratings_action and put the in the last row of R
4. get X and Theta out of cofiGrad algorith
5. get prediction matrix p by X * Theta
6. get my predictions out of the last row or p matrix
7. sort my predictions in descending order so as to see first the movies with the best predicting rating
8. print predicted movies and original ratings

You can find the MATLAB code for "COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH 'cofiGrad' algorithm in [here](recommender-coding/recommender-sparse). And your can check my movie ratings in [here](recommender-coding/my-ratings-action)

Results:

Below there are two pictures one with the my original movie ratings and the predicted movies.  As my original ratings are rated with 5 and 4 for action and drama movies, and 0.5 and 1 for children movies, the predicted movies are mainly movies related to my tastes: action and drama movies.  I watched some of the predicted trailer movies and most of them match with my tastes eventhough I got a couple that are child movies or comedy movies that I do not like.
##### My original rated movies
![image](https://user-images.githubusercontent.com/53232113/120251997-d6ac9300-c248-11eb-9447-dfae5df96aa8.png)

##### Predicted movies
![image](https://user-images.githubusercontent.com/53232113/120251686-f1323c80-c247-11eb-9116-31a1df463c7d.png)

**9. OBSERVATIONS**
The data to ptocess the algorithms is 'ml-latest-small'(aprox 100.000 ratings) from grouplens.  When using the 'ml-10m' dataset (aprox 10 millon ratings) there is an error in cofiGrad algorithm.  To fix this bug, we grouped the userID in table ratingsTbl with commands:  
```
[grp,inds] = findgroups(ratingsTbl.userId);
inds = gather(inds);

nu = length(inds);
fprintf('There are %d unique users in the ratings dataset',nu)

ratingsTbl.userId = grp
```
The bug is because there is a need to group the useid in orden to have all rows with some nonzero data.
Also, we found out that with the 'ml-10m' dataset from grouplens neither 'codiGrad' nor 'fmincg' algorithms work as there is too much data for them and after hours of waiting they collapsed.

**10. CONCLUSIONS**
1. for large data sets these algorithms do not work, they are just too slow(Mathworks online environment). Probably there are other algorithms or there is a need for a supercomputer to make them work.
2. for data sets with around 100.000 ratings these algorithms work perfect. It took between 30 second to 5 minutes to finish.
3. Using datastore and tall tables is the best way to work with big datasets. In this analysis we worked with two datasets, one with 100K ratings and another with 10000 k ratings.  In the small dataset there is no need for datastore and tall tables, and in big dataset(10000 k ratings) most of the processes worked in seconds or in a few minutes.
4. Using sparse matrices is the best way to work with big datasets. Sparse matrices use less memory and are faster than ordinary matrices.
5. The 'fmincg' algorithm has better recommender movies than the 'cofiGrad' algorithm.   All recommender movies in 'fmincg' are satisfactory to my preferences because all are related to action and drama.  The recommender movies with 'cofiGrad' algorithm are not all related to my preferences as some of them are comedy and child related movies.
6. The recommender movies in 'fmincg' algorithm and 'cofiGraf' algorithm do not match, and they are totally different recommendations.     
