## CH01. Introducing the Istio service mesh

- Service Mesh
    - term describes a decentralized application networking infrastructure that allows applications to be secure, reslient, observable, and controllable
- Istio
    - open source implementation of service mesh built around Envoy Proxy
- Problems that Istio solves
    - security policies
    - retries
    - failutre detection
    - load balancing
    - circuit breakings
    - observability (metrics, tracing, logging, …)
    - …
- Requirement: the system needs to be technology (or language) agnostic without requiring changes to application code
- Using an application level proxy as a solution
    - this proxy handles inbound and outbound traffics
    - common issues are handled by proxies (not by applications)
- Application + Sidecar Proxy = Service Mesh

## CH02. First Step with Istio

- default setup (control plane)
    - istio-egressgateway
    - istio-ingressgateway
    - istiod (controller)
- functions of control plane
    - APIs for the data plane to consume their configuration
    - Service discovery abstraction for the data plane
    - Service-proxy sidcar injection
    - …
- istiod
    - is responsible for taking higher level Istio configurations and turning them into proxy-specific configurations for each data-plane service proxy
- ingress & egress gateway
    - controll traffics into and out of the cluster
    - independent of any application workload

## CH03. Istio’s data plane: The Envoy proxy

- Envoy is application level (L7) proxy
    - it understands HTTP1.1, HTTP2, gRPC, …
- Inter-service features
    - Listeners: expose a port to outside world
    - Routes: routing rules how to handle the traffic
    - Clusters
- Service discovery
    - uses discovery API implemented by control plane
- Distributed Tracing
    - traceId, spanId, …
- Dynamic configurations
    - Listener discovery service, Route discovery service, Cluster discovery service, Endpoint discovery service, Secret discovery service, Aggregate discovery service

## CH04. Istio gateways: Getting traffic into a cluster

- Ingress: a traffic that originates outside the network
- How to get to ingress point?
    - Client queries externally exposed api
    - it resolves domain name to an IP address via DNS
    - this IP indicates the address of reverse proxy
    - reverse proxy forwards traffic to actual service instances (virtual hosting)
- Istio gateway plays the role of network ingress point, load balancing and virtual host routing
    - Istio uses a single Envoy proxy as the ingress gateway

## CH05. Traffic control: Fine-grained traffic routing

- Deployment vs Release
    - Deployment ⇒ the code is installed into production (no traffic)
    - Release ⇒ expose the code to users
- Istio can help to control the traffic during release
    - header matching
    - weight (for canary)
    - we can automate canary task with using Flagger