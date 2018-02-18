# Natural Language Understanding
## Goal of this project
To make it brief, this program will analyze twitter accounts and will show its areas of interests with a summary of sentiment analysis. At a larger scale -and with performance improvements ! :-) -  this program could be used to help a twitter user select best interest-matching twittos accounts.

### Output sample with NASA tweets categorized by Watson with 90% of confidence :
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20input.png)

Categories (clockwise by order of occurrence in user Tweets):
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20categories.png)

Sentiments (clockwise by order of occurrence and detection percentage):
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20sentiments.png)


### Output sample with Paolo Coelho tweets categorized by Watson with 90% of confidence :
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20input%20Paolo.png)

Categories (clockwise by order of occurrence in user Tweets):
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20categories%20Paolo.png)

Sentiments (clockwise by order of occurrence and detection percentage):
![alt](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/Graph%20sentiments%20paolo.png)

This Natural Language Processing program was created to show Master 2 French students of Université Pierre et Marie Currie ( Universités Sorbonne ) how to achieve sentiment analysis and natural language processing of Tweets with Watson cloud SDK for free. It uses Docker to help students understand basic concepts and parameters behind containers.

## Architecture
![alt text](https://raw.githubusercontent.com/Rodrigues-Alexandre/Natural-Language-Understanding/master/IMG/architecture.png)
- The left column shows all the different interactions between this program and the outside
- Tweepy ; IBM Cloud Watson and Matplotlib are the Python libraries used to interact with what is outside this program. On the bottom of each logo you can see the API requests limits for a free usage (last check was in February 2018)
- TweETL, Tweetory and TweeToGraph are different Docker containers with jupyter Notebooks
- MongoDB is the NOSQL database solution used for this project. JSON tweets from Twitter and the pymongo library made it perfect for this POC.

## How does it works
This program consists of several Jupyter notebooks each performing a different step of Natural Language Processing:
- TweETL retrieve tweets for a set of twitter users using tweepy library and store them on a MongoDB collection (one collection per Twitter user)
- Tweetory retrieve new tweets stored in MongoDB and will request Watson to classify them and perform sentiment analysis. The second part of the script will select most confident Watson answers to prepare for user view
- TweeToGraph shows 5 preferred areas of interest and 5 most important sentiments of a Twitter account selected by user to help him understand the mindset of the targeted twitter account.

## How to use this project
- You need to create a Twitter account and a new Twitter application to get application credentials and access twitter REST APIs:
https://apps.twitter.com/
https://apps.twitter.com/app/new

- Now you need an IBM Cloud Lite account and service app credentials for IBM Cloud Watson Natural Language Understanding:
https://www.ibm.com/cloud/lite-account
https://console.bluemix.net/docs/services/natural-language-understanding/getting-started.html#getting-started-tutorial

#### How to reuse this project with Docker containers:
- Understand basic concepts of hosting container and setting up network NAT & internal networking.
- Download Docker installation package and  Kitematic to retrieve container logs output or find the container logs on your system :-)
https://www.docker.com/get-docker https://kitematic.com/
- Create 4 containers (One with MongoDB official container from Docker Hub) and three using the dockerfiles provided in this repository
- Attach all containers to the same network and make sure they all have access to internet (particularly to Twitter and IBM Cloud) and start the containers
- with a web browser open the Jupyter notebooks using the container IP you configured and the notebook token provided on the container logs or the Kitematic console. You will need one browser window or tab per container.
- open the notebook file you can see on each notebook's tab or window
- update **auth, watsonUnderstanding and MongoHost** variables with the appropriate ones of your setup (see how to use this project section and retrieve MongoDB container IP / port)
- update **twittos** variable to retrieve the twitter accounts you want

#### How to reuse this project without containers:
- Install Python 3.6.x
- Install pip and the libraries : notebook pymongo tweepy numpy matplotlib requests watson-developer-cloud
- Install MongoDB and retrieve it's IP / port. Do not use Authentication for a simple POC. If you want to use authentication you will have to update the connection variables in the notebooks
- start MongoDB
- download the **ipynb** files of this repository, change the directory ( cd ) of your console to the directory where you stored the files and start Jupyter: **python -m notebook --ip=\* --NotebookApp.allow_origin=****
- open all **ipynb** files and update **auth, watsonUnderstanding and MongoHost** variables with the appropriate ones of your setup (see how to use this project section and retrieve MongoDB IP / port. It could be 127.0.0.1 / 27017 if you installed it on you own desktop)

#### Why there is already outputs on the Notebooks
- to help you check easily what kind of outputs you can expect from the scripts and you implemented them well
- the dots "." during Watson calls loop make sure you can see the program is doing something without taking all the space available when you process thousands of tweets.

## How to improve performance
The main execution delays are due to serial process during twitter and IBM cloud API calls. If you are willing to improve it you can start by :
- multiprocessing Tweetory during the loop **for tweet in tweets:**
- multiprocessing TweETL during the loop **for twitto in twittos:** .

Make sure you don't start hundreds or thousands of process at the same time (potentially your multiprocessing implementation can start one process per twitter user you retrieve or, even worst,/!\\ one process per tweet to analyze /!\\). The use of a pool (20 or 50 process max) looks a good idea ;-)

For multiprocessing usage understand that its support is a little bit tricky in Python (you will have to specify what is the main program and what will have to be executed during multiprocess) and that I did not find an implementation working with Jupyter (you will have to make your own Python scripts out of Jupyter and change the the dockerfiles accordingly)

If you feel you can improve the process time to organize categories and sentiments when you have tens of thousands tweets you are right: check **if CategoriesCollection.find({'label' : WatsonCategoryLabel}).count() > 0:** and avoid (be careful doing this !) requesting this find on MongoDB each time the program process a new tweet :-)

## Disclaimers
This project, files and tutorial are provided as-is with no guarantee to work on your own system or setup. Also note that I built this project for teaching purpose with no intention of production usage particularly regarding the security and the performance.

## Free APIs Limits
Make sure you don't reach the call limits on Twitter (reset every 15 minutes) since I did not implement the error handling (one call per twitter account) and use the IBM cloud Watson APIs keeping in mind that you have 30,000 enrichments free per month (processing one tweet for categories and sentiments use two enrichments. So you can fully process 15,000 tweets per month). The program will handle the errors if you reach IBM cloud Lite calls limit.
