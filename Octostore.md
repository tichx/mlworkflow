
# Scenario 1
The Git Experience but for Data

**Problem Statement**: --- (any time there’s a file in git that’s >25MB, the git process grinds to a halt)

- Developer chooses a repo they want to work with.
    - The repo has code in it but no data
    - The data is hosted on an S3 blob
- The repo contains data, so they download the ‘barcelona_data’ that make data a first class experience in git.
![picture 1](images/ca390f14ee8956099d8059a3bbfed324247be2be9f8601219e2b8d4e28551a0f.png)  




- Developer choses to clone the repo.
- At the same time the repo is cloned, it also gives an option to copy the data to a new bucket.
- The developer wants to work with the data locally, so she explicitly pulls down the data, updates any pointers (via shims) so that the scripts still work, but now point at local data
Git could be extended via ‘add-ons’ using a mechanism like this -https://github.com/stevemao/awesome-git-addons
![picture 2](images/79f0c78e9ade4d2c41a59a69056310d8a19309085922f80465bce793627460e3.png)  


- Developer checks out a new branch, and the tool locks and changes the data
- Data is now pointing to branch specific data
![picture 3](images/7731fcd2a8d9552f0acaabca256ef0ccf84cfb736d2eab68232f5a9536fff14d.png)  

- Developer wants to visualize data locally
- Uses a built-in library to do so
    - Something like facets (https://pair-code.github.io/facets/ ) would be great
    - Could also generate widgets that appear in the Jupyter Notebook

![picture 4](images/1b61d90e58f6a6c762b0944df854b0d881913518407ccba414f9fcbbe2fc7d5e.png)  

- Developer wants to add an additional file and changes her data.
- Data is automatically ignored by git and pushed to datastore for you as if though you are storing data in GitHub, with the same commands git add and git push.
- All pointers are correctly updated 
    - Would the pointers to the data change? E.g. should it point at a historical commit of the data, instead of just to a file?

![picture 5](images/7cad62e997b7e8d2bab5e53a09100458c1f688b55b5f75b4cb8a42cbe129bf18.png)  

# Scenario 2
A developer wants to get started with an open data set.
#### Pre-req:
- User already has an account with a cloud that provides an S3-compatible blob store
- User has already installed github_data

#### Steps:
- Developer chooses to start working using an open data set such as satellite data.
- The data set is very large, too large to work with locally.
She doesn’t care what cloud she uses, but she’ll need to make a copy of the dataset – the source files are both remote (meaning training will be slow) and unusable (they’re in tarball format)
- She clones the repo, and it prompts her to select a cloud (or choose her own)

![picture 6](images/031cb86cd7a44892f47cb2afddab3f81efb9c8dec4268f3f535c26308ae50d00.png)  

#### Steps:
- During the copy, the developer has an option to create multiple types of objects are created:
    - A “sampled” and “full” data set
    - Initialization of a data version
    - Collaboration scaffold (e.g. PR structure)
    - Statistics, schemas and examples
    - DataValidation rules (based on contents)
- Using compute already associated with the project, she runs schema, statistics and example generators (free for <10 GB?)
- Examples of how this works comes from TFX 
    - SchemaGen - https://www.tensorflow.org/tfx/guide/schemagen
    - Schema Gen also auto-grenerates DataValidation rules - https://www.tensorflow.org/tfx/guide/tfdv
    - StatisticsGen - https://www.tensorflow.org/tfx/guide/statsgen
    - ExampleGen - https://www.tensorflow.org/tfx/guide/examplegen
- This generates files that are small enough to use locally for training and improves visualization. It does not require downloading very large data sets.
- The statistics, schemas and examples are all added to the project’s repo automatically. 
![picture 7](images/2d762a27257fd82b6d582ace03292bc4a68e3ebad3776b9dce8f53af19844ba8.png)  

#### Steps:
- Developer wants to add a second large data set to the existing repo.
She goes to the National Weather Service site (nws) and chooses a snapshot to grab.
- She then adds it to her existing data sources for her project
![picture 8](images/2c8abd9717be085c1093af69aedc8725cb714cf1f1ec2a996d5cb248d121404f.png)  

#### Steps:
- Both data sets have been added to the repo, but they’re still too large to download. The developer wants to analyze them, so she issues a command to download the necessary statistics & schemas to her local machine.
- She is able to browse the information and choose relevant features

# Senario 3.0
#### Pre-req:
- Developer wants to use local Jupyter notebook to write and run her training code.
- User has already installed mlflow & github_data
Locally, she’ll use simple APIs to track the experiment. In her current folder

#### Steps:
- Developer starts nteract (a Jupyter UI)
- She imports ‘octostore’, an open source library designed to make interacting with data easy
- She uses octostore to import the data from the source – at this point octostore begins a session and starts recording metadata about her run
- Octostore also has the ability to split data according to common techniques (in this case, 70% training and 30% hold back for testing)
![picture 10](images/3c16fe8f6f82e2d4fff22e5461efcf2309a2f4c15f7c07d0cbf5f8f7704e4a93.png)  

#### Steps:
- She’s now ready to do a full training run
- She’s already used MLFlow in the past, and octostore understands the API – so she doesn’t have to change any code.
- She pastes in the code she used from MLFlow and tweaks it as she needs it.
    - Though MLFlow  will “start its own run”, this takes place inside of the larger “octostore” run already kicked off
- The code is now ready to run
- Octostore also can run the file (in the current python environment) and automatically convert (if necessary) the python notebook.
- All information about the run is stored locally in:
<PROJECT_NAME>/    runs/       <EXPERIMENT_NAME>/         <RUN_ID>/ 	    [ALL_RUN_FILES]
![picture 11](images/7c9abf410719bcd720648055ea35393c3e71eddfbb39bf76623bd81316941d6b.png)  

![picture 12](images/84a4cedf0ce4e9b0029a72b5d05a6c200a7fea34599a3c228c20ab4b6119f2c0.png)  

#### Steps:
- Developer now wants to evaluate local runs and compare against each other
- The files are written in an MLFlow compliant format so they can be evaluated as well via the MLFlow UI
![picture 13](images/87070531e340ccb23f36185e1a96771b39bea9d378f37a607a4241301fd768eb.png)  

# Scenario 4
A data scientist finishes her local experiments and wants to continue working on another device, or share her experiments with others

#### Pre-req:
- Developer does not have a git repo for the ml project yet, but has already authorized with GitHub
- Developer HAS an environment file, but DOES NOT have a Pipfile (she has been installing python packages using ‘pip’ 
#### Steps:
- To accomplish smooth transition from one working machine to another, a data scientist can easily package his current environment or dependencies
‘octostore create’ command to auto-detect and generate structure (according to cookie-cutter-datascience)
- Additional commands capture the environment and use git to push to the remote repository.

![picture 14](images/3a369f9919a262129ae154cc5fc38aceafc07ebb11ec8b0607a4079c424a1426.png)  

# Scenario 5
A second data scientist wants to reproduce the repo locally
#### Pre-req:
- A second developer wants to reproduce the environment cleanly
The repo has already been set up, and she just needs to download and replicate
#### Steps:
- The developer clones the repo locally as normal
- Once inside the repo, she uses ‘octostore’ up to execute the following commands:
    - Detect Pipfile, requirements.txt or other python environment file and create a virtual python environment
    - Detect an environment.yml file and create a conda environment
![picture 15](images/3b123c9e5073665f7b7fce56ecbd321cd39839a82a8e31f547d580dcc87414c4.png)  

- The environment is cloned, but the developer is warned the requirements of the model do not match the local environment.
- The developer decides to move to codespaces.
![picture 16](images/56c3457bc5527cfc69e0536e1015bd94aa5d670a7e64150f74add04f012e094d.png)  

- The environment is cloned, but the developer is warned the requirements of the model do not match the local environment.
- The developer decides to move to codespaces.
    - QUESTION: How do we do this in a ‘cloud neutral’ way? Other clouds need to be able to participate or we will have limited uptake.

![picture 17](images/6d6d5ff54eccc8b92950188fa71547426c144f8ce416ce58dd72b07149489bbb.png)  
