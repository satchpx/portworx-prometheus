# Monitoring Portworx on Openshift

## Before you begin
```
git clone git@github.com:satchpx/portworx-prometheus.git
cd openshift
```

## Overview
This solution uses the openshift monitoring stack for monitoring Portworx. The rationale behind this solution is that a separate Prometheus stack is not required, and minimizes management and operational overhead.

This solution works for both OCP 3.11 and OCP 4.x. OCP 4.x shall have a project and Prometheus stack/ project meant exclusively for external app integrations. We shall use the appropriate namespace/ project to create the service monitor and alert rule CRD.

## Deploy a service monitor for Portworx
```
oc create -f portworx-service-monitor.yaml
```

## Create Portworx Alert rules in prometheus
```
oc create -f portworx-prometheus-rule.yaml
```