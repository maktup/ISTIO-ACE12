IMPLEMENTATION OF: “ISTIO” (MESH SERVICES) & “IBM APP CONNECT12” CONTAINERIZED.
------------------------------------------------------------------------------ 
This document focuses on the PROCEDURE for the implementation of SERVICE MESH with ISTIO, applied to IBM APP CONNECT v12. 
The proposed INDEX will be the following:

I. DEFINITIONS.
II. INSTALLATION & CONFIGURATION.
    ? INSTANCE CREATION: "SERVICE MESH".
    ? INSTANCE CREATION: "IBM APP CONNECT v12".
III. MANAGEMENT OF: DASHBOARDS.
IV. DEPLOYMENT STRATEGIES & VERSION CONTROL.
    ? DEMO #1: (CANARY RELEASE).
    ? DEMO #2: (BLUE & GREEN DEPLOYMENT).
    ? DEMO #3: (TRAFFIC CONTROL: “INGRESS GATEWAY”).
    ? DEMO #4: (UNIFYING EVERYTHING).

The entire procedure in the PDF.

---------------

I. DEFINITIONS:
WHAT IS SERVICE MESH?:
It is a dedicated INFRASTRUCTURE layer which can be added to our APPLICATIONS, with the objective of achieving: OBSERVABILITY, MANAGEMENT TRAFFIC, SECURITY.

WHAT IS ISTIO?:
Currently, there are several IMPLEMENTATIONS of SERVICE MESH, of which the most important currently is: ISTIO. It is an OpenSource MESH SERVICE that is transparently overlaid on DISTRIBUTED APPLICATIONS. Likewise, ISTIO provides:
▪ TRACEABILITY of MICROSERVICES & possible DEPENDENCIES between them.
▪ Secure communication between MICROSERVICES in a cluster with TLS encryption, authenticating & authorizing.
▪ Load balancing for TRAFFIC: HTTP, TCP, WebSocket, etc.
▪ Detailed TRAFFIC control.
▪ Metrics, records & TRAFFIC monitoring: (entry & exit).
▪ Configuration of PROXYS (SIDECARs), NOT embedded in the MICROSERVICES (OpenTracing).
IMPORTANT: “Know that the idea is that the MICROSERVICES manage only BUSINESS LOGIC & that everything ADDITIONAL such as: treatment of security protocols, etc., is managed by the same: SERVICE MESH.”

WHAT IS THE ISTIO ARCHITECTURE LIKE:
The COMPONENTS that ISTIO manages are:
✓ CONTROL PLANE: This is responsible for managing & configuring the PROXYS for TRAFFIC ROUTING based on the defined RULES.
✓ DATA PLANE: Composed of a set of intelligent PROXYS (ENVOY) created in CONTAINERs & deployed as SIDECARs in the PODs, these control all COMMUNICATION between the MICROSERVICES & collect TELEMETRY of all the TRAFFIC of the SERVICE MESH.

Said TELEMETRY is an OBSERVABILITY feature provided by ISTIO, to OBSERVE the behavior of MICROSERVICES:
✓ Metrics (LATENCY, ERRORS, SATURATION).
✓ Distributed Traces (MICROSERVICES TRAFFIC FLOW).

II. INSTALLATION & SETUP:
Initially you want to VALIDATE the version of OPENSHIFT that you have installed.

In this case, it is DISPLAYED:
✓ VERSION: 4.12.
✓ CHANNEL: stable- 412

IMPORTANT: “You must consider that INSTIO supports OPENSHIFT
with the latest versions”.

Then, we proceed to create the 2 NAMESPACES called:
$oc create ns istio-system
$oc create ns ace-istio

Then, we proceed to create the associated OPERATORs
to the NAMESPACE: istio-system.

First the OPERATOR called:
✓ NAME: OpenShift Service Mesh
✓ VERSION: 2.4.5- 0
✓ INSTALLATION MODE: All Namespaces on the cluster.

IMPORTANT: “The INSTALLATION can be accessed from all the NAMESPACES, but where it will mainly be installed will be in the NAMESPACE: openshift-operators”.

Then, within the NAMESPACE: istio-system, we proceed to install the OPERATOR called:
✓ NAME: Kiali Operator
✓ VERSION: 1.65.
✓ INSTALLATION MODE: All Namespaces on the cluster.

IMPORTANT: “The INSTALLATION can be accessed from all
the NAMESPACES, but where it will mainly be installed will be in the NAMESPACE: openshift-operators”. Then, within the NAMESPACE: istio-system, we proceed to install the OPERATOR called:

✓ NAME: Red Hat Openshift distributed tracing platform
✓ VERSION: 1.47.1- 5
✓ INSTALLATION MODE: All Namespaces on the cluster.

IMPORTANT: “The INSTALLATION can be accessed from all the NAMESPACES, but where it will mainly be installed will be in the NAMESPACE: openshift-distributed-tracing”. Then, we proceed to validate the OPERATORs installed within the NAMESPACE: istio-system:

▪ OpenShift Service Mesh.
▪ Kiali Operator.
▪ Red Hat Openshift distributed tracing platform.

Then, we proceed to create the OPERATORs associated with the NAMESPACE: ace-istio.

First the OPERATOR called:
✓ NAME: IBM App Connect
✓ VERSION: 10.0. 1

A. CREATION OF INSTANCES: “SERVICE MESH”:
We proceed to enter the NAMESPACE: istio-system & the OPERATOR: Red Hat Openshift Service Mesh & on the tab: Istio Service Mesh Control Plane, we proceed to create an INSTANCE of the RESOURCE: ServiceMeshControlPlane:

apiVersion: maistra.io/v
kind: ServiceMeshControlPlane
metadata:
name: basic
namespace: istio-system
spec:
addons:
grafana:
enabled: true
jaeger:
install:
storage:
type: Memory
Kiali:
enabled: true
prometheus:
enabled: true
policies:
type: Istiod
profiles:
default
telemetry:
type: Istiod
tracing:
samples: 10000
type: Jaeger
version: v2.
