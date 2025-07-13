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