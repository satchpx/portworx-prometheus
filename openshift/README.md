# Monitoring Portworx on Openshift

## Before you begin
```
git clone https://github.com/satchpx/portworx-prometheus.git
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

## Verify
*NOTE*: The default scrape interval for openshift-monitoring Prometheus is _*2 minutes*_. Wait for at least 2 minutes for the targets to be discovered and the metrics to be scraped.

Let's run a simple query on a Portworx cluster quorum metric
```
oc -n openshift-monitoring exec prometheus-k8s-0 -- curl -kv http://localhost:9090/api/v1/query?query=px_cluster_status_cluster_quorum && echo
```

The output should look like below:
```
* About to connect() to localhost port 9090 (#0)
*   Trying ::1...
* Connection refused
*   Trying 127.0.0.1...
{"status":"success","data":{"resultType":"vector","result":[{"metric":{"__name__":"px_cluster_status_cluster_quorum","cluster":"px-cluster-24b84494-de6a-4836-9f6d-fbd53647cfa4","endpoint":"px-api","instance":"70.0.49.104:9001","job":"portworx-service","namespace":"kube-system","node":"sb-ocp-311-4","node_id":"4a78e8fa-12db-4b38-8474-2b284546be4e","pod":"portworx-bpgtf","service":"portworx-service"},"value":[1573241455.549,"1"]},{"metric":{"__name__":"px_cluster_status_cluster_quorum","cluster":"px-cluster-24b84494-de6a-4836-9f6d-fbd53647cfa4","endpoint":"px-api","instance":"70.0.49.81:9001","job":"portworx-service","namespace":"kube-system","node":"sb-ocp-311-3","node_id":"0f0ce92f-bf03-4409-8d24-fb6a8bda493a","pod":"portworx-45zsm","service":"portworx-service"},"value":[1573241455.549,"1"]},{"metric":{"__name__":"px_cluster_status_cluster_quorum","cluster":"px-cluster-24b84494-de6a-4836-9f6d-fbd53647cfa4","endpoint":"px-api","instance":"70.0.49.99:9001","job":"portworx-service","namespace":"kube-system","node":"sb-ocp-311-2","node_id":"92f667f7-8d7f-4794-b505-78700286fb18","pod":"portworx-dlpxg","service":"portworx-service"},"value":[1573241455.549,"1"]}]}}* Connected to localhost (127.0.0.1) port 9090 (#0)
> GET /api/v1/query?query=px_cluster_status_cluster_quorum HTTP/1.1
> User-Agent: curl/7.29.0
> Host: localhost:9090
> Accept: */*
>
< HTTP/1.1 200 OK
< Access-Control-Allow-Headers: Accept, Authorization, Content-Type, Origin
< Access-Control-Allow-Methods: GET, OPTIONS
< Access-Control-Allow-Origin: *
< Access-Control-Expose-Headers: Date
< Content-Type: application/json
< Date: Fri, 08 Nov 2019 19:30:55 GMT
< Content-Length: 1176
<
{ [data not shown]
* Connection #0 to host localhost left intact
```
