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