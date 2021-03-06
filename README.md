# Swivel Spark prep [![Build Status](https://travis-ci.org/src-d/swivel-spark-prep.svg?branch=master)](https://travis-ci.org/src-d/swivel-spark-prep)
> Distributed data preparation for the Swivel model

Distributed equivalent of `prep.py` and `fastprep` from [Swivel](https://github.com/tensorflow/models/blob/master/swivel/) using Apache Spark.


# Development
```
./gradlew idea # if using InteliJ
./gradlew build
./gradlew test
```

# Run

On a single machine, Apache Spark in Local mode
```
./gradlew shadowJar
./sparkprep --help
```

On an Apache Spark standalone cluster
```
./gradlew build

# https://github.com/tensorflow/ecosystem/tree/master/hadoop#build-and-install
cp <path-to-ecosystem-hadoop>/target/tensorflow-hadoop-1.0-SNAPSHOT-shaded-protobuf.jar .
# or use un-official build from .m2 or .gradle cache, after ./gradlew shadowJar
cp <path-to>/tensorflow-hadoop-1.0-01232017-SNAPSHOT-shaded-protobuf.jar .


MASTER="<master-url>" ./sparkprep-cluster --help
```

# Algorithm

Pre-processing consist of 3 jobs:
 1. reading or creating a vocabulary
 2. coocurence matrix
  - vectorizing input: token->int using the vocaulary
  - build full dense coocurence matrix, for given window size
  - shard coocurence matrix to N pices (over each dimention)
     * encode each shard in a single ProtoBuff
     * save N^2 files
 3. coocurence matrix: count marginal summs