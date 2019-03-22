# [Oracle Cloud Infrastructure Series](https://cloud.oracle.com/en_US/cloud-infrastructure)
This repository contains a series of recipes on how to configure different services
of OCI.

> Note that this is a living repository and will be updated regularly.

Below the list of recipes available so far:

## [Quick install of Oracle Kubernetes Engine](https://github.com/luisw19/oci-series/tree/master/oke-install)
Steps to provision an OKE cluster based on the **quick create** option. It also includes some useful tips and tricks.

## [Nginx Ingress Controller Installation](https://github.com/luisw19/oci-series/tree/master/oke-ingress)
Shows how to configure an [Nginx Ingress Controller]((https://kubernetes.github.io/ingress-nginx/)) in OKE. The recipe also describes how to configure an Ingress service to support TLS using a fully qualify domain name (FQDN) with self-signed certificate.

## [Complete Istio installation in OKE](https://github.com/luisw19/oci-series/tree/master/oke-istio)
Comprehensive and complete guide on configuring [Istio version 1.1.0](https://github.com/istio/istio/releases/tag/1.1.0) in OKE. The recipe describes to configure the Istio to support:
- TLS/HTTPS configuration in an Istio Gateway using a fully qualify domain name (FQDN) with a self-signed certificate and based on  [Secret Discovery](https://preliminary.istio.io/docs/tasks/traffic-management/secure-ingress/sds/)
- The following add-ons: [Prometheus](https://istio.io/docs/tasks/telemetry/querying-metrics/),
[Jeager](https://istio.io/docs/tasks/telemetry/distributed-tracing/),
[Grafana](https://istio.io/docs/tasks/telemetry/using-istio-dashboard/),
[Kiali](https://istio.io/docs/tasks/telemetry/kiali/) and
[Service Graph](https://istio.io/docs/tasks/telemetry/servicegraph/).
- Optimisation of telemetry options and compute resources.
