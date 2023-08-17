### This document is about the sdkclient pkg under the Numaflow platform repo.

1. Basically, this pkg creates SDK clients by declaring the interface and creating a client to implement the interface.
2. The client uses a gRPC controller which is provided by numaflow-go SDK to send gRPC requests to the gRPC server.
3. Regarding unit tests, because we can't locally send a gRPC call, the mock client created by mockgen in numaflow-go is used to unit test the sdkclient.

