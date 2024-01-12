## How to test Java sdk changes

After you make certain changes to the java sdk implementation, you would like to use it to build a brand new UDF image and run it on a pipeline to verify the image continues working well with the numaflow platform. Below I use reducer sdk as an example to demonstrate the steps to follow:

### Step 1 - build the changes and save them to the local Maven repository

* Make the changes.
* Make sure the changes are built and saved in your local Maven repository.
  * In the pom.xml file, update the version [here](https://github.com/numaproj/numaflow-java/blob/main/pom.xml#L9), I suggest increasing the PATCH version by 1.
  * Run `mvn clean install` at the root of the project.
  * Go to `~/.m2/repository/io/numaproj/numaflow/numaflow-java` and you should be able to see the new version folder being created.

### Step 2 - build the test UDF image and push it to a remote repository

Let's say we want to build one of the example UDFs, reducer sum.

* Go to the numaflow-java examples folder.
* In the pom.xml file, update the [dependency version](https://github.com/numaproj/numaflow-java/blob/main/examples/pom.xml#L19) of `numaflow-java` to the one we built above.
* Under the example folder, run `mvn clean install`, which will build the new image using the latest sdk changes.
* Tag and Push the new image.
  * Use `test` as the tag to distinguish it from the real versions.
  * `docker tag numaflow-java-examples/reduce-sum:latest quay.io/numaio/numaflow-java/reduce-sum:test`
  * `docker push quay.io/numaio/numaflow-java/reduce-sum:test`

### Step 3 - run a pipeline using the image

We can use numaflow e2e test cases to verify the change.

* Check out the latest numaflow repository.

* Find the test case and update the image path in the test pipeline .yaml file.
* Run `make Test*`.

### Step 4 - clean up and publish the PR

Once we finish the testing, we should roll back the version updates before publishing the PR. The real version updates can be raised in a separate PR whe n we decide to update at a later time.

* Roll back all the pom.xml changes.
* Clean up testing artifacts, e.g. images that are no longer used etc.



