# Sample API in R
This meta project consists of sample script which can be used to create machine learning API in `R` which includes
separate template for creating preprocessing, traiing and prediction API along with logging and basic error handling facility. 

# Package Information
+ package `plumber`: for creating api
+ package `futile.logger`: for capturing the log
+ package `future`: for creating asynchronous function call for preprocessing and training

# Main Scripts
+ The `prediction_API.R` holds the actual code for prediciton module for the `/predict` api.
  + `run_prediction_API.R` is a wrapper script to start the `prediction_API.R`
+ The `training_model_API.py` holds the code for preprocessing and training module for the apis `/preprocess` and `/train`
  + `run_training_API.R` is a wrapper script to start the `training_model_API.R`
+ The `dummyAPI.R` under directory `/testdir` holds the dummy scipt for `/notify` api for implementing callback facility
+ The `start.sh` scripts can be used to start all the server at one go
+ You may need to start the dummy api separately.


# Start the PREDICTION SERVER (/predict API):
  + `Rscript run_prediction_API.R <port number>` OR
  + use the `start.sh` file, run it as follows:
   + `./start.sh`
   + Remember to update the `start.sh` for port changing
## Run the client from terminal
  + following curl command can be used:
    + ```curl http://localhost:<port number>/predict --data '{"input": [{"UID":"1","AGE":"15"},{"UID":"2","AGE":"25"},{"UID":"3","AGE":"45"},{"UID":"4","AGE":"55"}]}' -H "Content-Type: application/json"```
	+ `/predict` is the prediction API end point
	+ You may need to update the `port number` in the above curl command

# Start the Training SERVER (/preprocess and /train API):
  +  ` Rscript training_model_API.R <port number>` OR
  + use the `start.sh` file and run `./start.sh`
  + As training and preprocessing can take several minutes, they should be called asynchronously using python package `future` followed by a post call to the `callback API` for sending the status of preprocessing and training.

##Run the client from the terminal 
  + curl for preprocessing api `/preprocess`
    + ```curl http://localhost:<port number>/prepro --data '{"callbackURL":"http://localhost:<port number>/notify"}' -H "Content-Type: application/json"```
  + curl for training api `/train`
    + ```curl http://localhost:<port number>/train --data '{"callbackURL":"http://localhost:<port number>/notify"}' -H "Content-Type: application/json"```
  + The above `callbackURL` is a dummy api for testing. The dummy api script is in `/testdir` folder. Run the dummy callback API `python dummy_API.py`

# Known issues in R
  + Nested file source may lose the argument value. So use sourcing as follows
  `source(filename, local=TRUE)`
  + Rememeber sourcing a file outside a function and inside a function are two different scenario.
    + It changes the scope of the variable to the sourced scripts.
	+ global variebles are not available to a sourced script if it's sourced inside a function.
  + ...
