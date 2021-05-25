# Movie-Recommender-systems-and-big-data-in-Matlab

In this project we analyze movie dataset with datastore and tall tables, we use collaborative filtering recommender algorithms with sparse and no sparse matrix and finally we compare and conclude results with both approaches.

Recommender Systems help find the product that could probably buy a customer, or suggest product to customers, or on youTube, uses it to decide which video to play next on autoplay, or Facebook uses it to recommend pages to like and people to follow. It is based on data of users and products. Here, the recommender system is based on a movie set of data with a list of movies and users with rankings. Once a new user inputs a few movie rankings, the algorithm will recommend the movies to watch.

INTRODUCTION 

This project is based on Coursega Stanford Machine Learning Andrew Ng course. Most of the code has been taken from Week 9 examples, with some changes.

There are three types of recommender systems:

A. Demographic Filtering- They offer generalized recommendations to every user, based on movie popularity and/or genre. The System recommends the same movies to users with similar demographic features. Since each user is different , this approach is considered to be too simple. The basic idea behind this system is that movies that are more popular and critically acclaimed will have a higher probability of being liked by the average audience.

B. Content Based Filtering- They suggest similar items based on a particular item. This system uses item metadata, such as genre, director, description, actors, etc. for movies, to make these recommendations. The general idea behind these recommender systems is that if a person liked a particular item, he or she will also like an item that is similar to it.

C. Collaborative Filtering- This system matches persons with similar interests and provides recommendations based on this matching. Collaborative filters do not require item metadata like its content-based counterparts.
In [wikipedia](https://en.wikipedia.org/wiki/Recommender_system) you can find mainly content based filtering and collaborative filtering, plus some others recommender systems.

Here, with MATLAB, we will extract and analyze big movie data with datastore and tall tables, and explore two types of colaborative filtering recommender systems, one with normal matrices and algorithm "fmincg", and another with sparse matrices and algorithm "codigraf", and observe their differences.

EXTRACT

The extract dataset comes from grouplens page at  "http://files.grouplens.org/datasets/movielens/" and use "ml-10m.zip". This data set contains 10000054 ratings and 95580 tags applied to 10681 movies by 71567 users of the online movie recommender service MovieLens. Users were selected at random for inclusion. All users selected had rated at least 20 movies. Unlike previous MovieLens data sets, no demographic information is included. Each user is represented by an id, and no other information is provided. The data are contained in three files, movies.dat, ratings.dat and tags.dat. Also included are scripts for generating subsets of the data to support five-fold cross-validation of rating predictions. 
The extraction downloads data into a zip file, unzip the file to have on disk the files "movies.dat" and "ratings.dat". The "movies.dat" file is passed to a database table "moviesTbl" and the "rating.dat" file is passed to a database tall table "ratingsTbl". You can find the MATLAB code for EXTRACT in [EXTRACT DATA FROM GROUPLENS INTO TABLES](recommender-coding/extract).

MANIPULATE A TABLE

The "movie.dat" table is a small dataset and there is no need to make it a tall table. First, we find out there are 10681 movies. Then, we extract from title the year of the movie and put in a year array. The distribution of the movies dataset by year is as follows:

![image](https://user-images.githubusercontent.com/53232113/119567726-48c93780-bd72-11eb-9019-a1b00b50f99a.png)

The histogram shows the number of movies per year of creation in the dataset. We find that the most number of movies are created between 2003 and 2005 with aproximately 1200 movies, in 1980 there are about 200, and after 2010 there are no movies in the dataset.
Then, we create a new field "year" as to have the creation year separately from the title.  

Extract the movie genres
The movie genres are contained in moviesTbl as the string variable, genre, which lists all genres that apply to each movie separated by a '|'. Since many movies belong to multiple genres, it is not possible to map this information to a single numeric or categorical value without creating a unique category for all observed combinations. Instead we will map each genre to a logical variable, then the genre variables to moviesTbl. A 1 in a given row of a genre variable will indicate that the movie in that row belongs to that genre. 

You can find the MATLAB code for "MANIPULATE A TABLE" in [MANIPULATE MOVIE TABLE](recommender-coding/manipulate-table).
   
   

MANIPULATE BIG DATA WITH DATASTORE AND TALL TABLE

COMBINE TABLES AND ANALYZE BIG DATA

COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH NORMAL MATRIX

COLLABORATIVE FILTERING MOVIE RECOMMENDER SYSTEM WITH SPARSE MATRIX

OBSERVATIONS

CONCLUSIONS
