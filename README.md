[![Build Status](https://travis-ci.org/aquasecurity/kubernetes-bench-security.svg?branch=master)](https://travis-ci.org/aquasecurity/kubernetes-bench-security)

# kubernetes-bench-security

The Kubernetes Bench for Security is a Go application that checks whether Kubernetes is deployed securely by running the checks documented in the CIS Kubernetes 1.6 Benchmark v1.0.0.

Tests are configured with YAML files, making this tool easy to update as test specifications evolve. 

## Installation

You will need to run this application on the target machines that you want to test. 

If Go is installed on the target machines, you can simply clone this repository and as follows: 

```go build -o cis_kubernetes .```

## Usage
```./cis_kubernetes [command]```

```
Available Commands:
  master      Checks for Kubernetes master node
  node        Checks for Kubernetes node
  federated   Checks for Kubernetes federated deployment
  help        Help information

Flags:
  -c, --check string   A comma-delimited list of checks to run as specified in CIS document. Example --check="1.1.1,1.1.2"
  -g, --group string   Run all the checks under this comma-delimited list of groups. Example --group="1.1"
  -h, --help           help for cis_kubernetes
  --json               Output results as JSON
```

## Test config YAML representation
The tests are represented as YAML documents (installed by default into ./cfg).

An example is as listed below:
```
---
controls:
id: 1
text: "Master Checks"
type: "master"
groups:
- id: 1.1
  text: "Kube-apiserver"
  checks:
    - id: 1.1.1
      text: "Ensure that the --allow-privileged argument is set (Scored)"
      audit: "ps -ef | grep kube-apiserver | grep -v grep"
      tests:
      - flag: "--allow-privileged"
        set: true
      remediation: "Edit the /etc/kubernetes/config file on the master node and set the KUBE_ALLOW_PRIV parameter to '--allow-privileged=false'"
      scored: true
```

Recommendations (called `checks` in this document) can run on Kubernetes Master, Node or Federated API Servers.
Checks are organized into `groups` which share similar controls (things to check for) and are grouped together in the section of the CIS Kubernetes document.
These groups are further organized under `controls` which can be of the type `master`, `node` or `federated apiserver` to reflect the various Kubernetes node types.

## Tests
Tests are the items we actually look for to determine if a check is successful or not. Checks can have multiple tests, which must all be successful for the check to pass.

The syntax for tests:
```
tests:
- flag:
  set:
  compare:
    op:
    value:
...
```
Tests have various `operations` which are used to compare the output of audit commands for success.
These operations are:

- `eq`: tests if the flag value is equal to the compared value.
- `noteq`: tests if the flag value is unequal to the compared value.
- `gt`: tests if the flag value is greater than the compared value.
- `gte`: tests if the flag value is greater than or equal to the compared value.
- `lt`: tests if the flag value is less than the compared value.
- `lte`: tests if the flag value is less than or equal to the compared value.
- `has`: tests if the flag value contains the compared value.
- `nothave`: tests if the flag value does not contain the compared value.




