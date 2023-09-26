# Nats Source: Built-in v.s. User-defined

In this summary, I use the numaflow nats e2e test as an example to compare the developer experience between using **built-in** and **user-defined** nats sources.

### A brief intro to the e2e test

Link to the test: https://github.com/numaproj/numaflow/tree/main/test/nats-e2e

To perform a complete nats e2e test, the following steps are taken:

* Step 1: Create a nats server.
  * Deploy a kustomization template that includes nats config, nats service and a stateful set.
* Step 2: Create a nats auth token.
  * Deploy a secret and mount it to source pods.
* Step 3: Declare a nats configuration. 
  * The configuration defines how the source reads from the nats application. It includes `url`,	  `subject`, `queue`, `auth` etc.
* Step 4: Write pipeline spec and start the pipeline
* Step 5: Use the E2E API to send messages to test nats subject and verify sink output.

### Comparison

To compare the developer experience between the two approaches, a simple test user-defined nats source is prepared and used to run the numaflow nats e2e tests. See: [numaflow-nats-source](https://github.com/KeranYang/numaflow-nats-source) 

##### The conclusion

From a numaflow developer's standpoint, there is **NO MAJOR DIFFERENCE** between using built-in and user-defined nats sources. Here is the code change that I need to make to switch our e2e test from using built-in to user-defined: [code change](https://github.com/KeranYang/numaflow/commit/5a1e8c9520df1cd1ab4cd7c3f3c8ef5e065fe4e5).

The only extra work introduced by the user-defined nats source is that the pipeline developer needs to take care of mounting secrets and config maps, which are common operations for a K8S developer.

##### Details

| Steps                                                        | Built-in nats                                                | User-defined nats                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Create a nats server                                         | use kustomization                                            | use kustomization                                            |
| Create a nats auth token                                     | use secret                                                   | use secret                                                   |
| Declare a nats configuration                                 | use the numaflow API                                         | use a JSON file that can be interpreted by the user-defined source |
| Write pipeline spec and start the pipeline                   | **DO NOT NEED TO** mount secrets and config maps to the pod because the numaflow platform takes care of it. | **NEED TO** mount secrets and config maps to the pod under a path that the user-defined source uses to access the data. |
| Use the E2E API to send messages to test nats subject and verify sink output. | use the numaflow e2e test framework                          | use the numaflow e2e test framework                          |

