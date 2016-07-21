#docker-compose for actionml/Prediction.IO

## Overview

This is an updated **docker-compose** for having a working stack of **Prediction.IO**.  Based on **ActionML** / **Prediction.IO** branch.

## How to setup
After cloning the repo, make sure you run:

```git submodule init```

```git submodule update```

This will download the git submodule for the **Universal Recommender Template** that will be used for testing.

## Components
* pio
* elasticsearch
* hbase
* spark

Optionally, after building, for running test to make sure the stack is working, you'll need the following after building the images:

* pip (in user space inside pio image), and additionally:
  * virtualenv 
  * preditionio 
  * datetime 
* Universal Recommender Template (https://github.com/actionml/template-scala-parallel-universal-recommendation.git)


## How to Build

For building the stack, just run:

```docker-compose build```

or if you want a named stack:

```docker-compose -p NameForStack build```

Grab a cup of coffee or your favorite beverage, since this might take a while.

## How to Run
To start the stack just use:

```docker-compose up -d```

or, if building with an specific name for the stack:

```docker-compose -p NameForStack up -d```

## How to see logs
for debugging purposes, if you want to see the logs, you can use:

```docker-compose logs -f```

or if using named stack:

```docker-compose -p NameForStack logs -f```

You can also specify a specific component stack to see, for example:

```docker-compose logs -f pio```

```docker-compose logs -f hbase```

```docker-compose logs -f elasticsearch```


## How to test

See <a>https://d.evops.pw/dockerized-prediction-io/</a>  for info on how to test and detailed explanation.


