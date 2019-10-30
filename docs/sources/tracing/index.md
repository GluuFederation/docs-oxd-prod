# Tracing in oxd
## Overview
oxd uses OpenTracing  to profile and monitor the end-points requested on oxd-server. OpenTracing is comprised of an API specification, frameworks and libraries that have implemented the specification, and documentation for the project. OpenTracing allows developers to add instrumentation to their application code using APIs that do not lock them into any one particular product or vendor.

## Enable tracing in oxd
To enable tracing in first select the tracer need to used for  
If `Jaeger` is selected as tracer for monitoring oxd


OXD server can be integrated with either [Jaeger](https://www.jaegertracing.io) or [zipkin](https://zipkin.io) tracer to monitor on UI.
