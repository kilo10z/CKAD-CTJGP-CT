## Kubernetes Network Policy
A Kubernetes Network Policy is used to control network traffic to and from Kubernetes pods. It defines rules for ingress (incoming) and egress (outgoing) traffic and allows you to specify which traffic is allowed or denied between pods, namespaces, or external services.

### Key Concepts:
* Pod Selector: Determines which pods the policy applies to.
* Ingress: Controls incoming traffic to the pods.
* Egress: Controls outgoing traffic from the pods.
* Default Behavior: All traffic (ingress and egress) is allowed by default unless a policy explicitly restricts it.


### Policy Types in Kubernetes:
#### Ingress Policy (Traffic Allowed Into the Pods):
Controls incoming traffic to the selected pods and is defined using the ingress field in the policy.
Effect: If an ingress policy is specified, the selected pods will only accept traffic from sources allowed by the policy.
If no ingress policy is defined, all incoming traffic is allowed by default.

#### Egress Policy (Traffic Allowed Out of the Pods):
Controls outgoing traffic from the selected pods and is defined using the egress field in the policy.
Effect: If an egress policy is specified, the selected pods will only be able to send traffic to destinations allowed by the policy.
If no egress policy is defined, all outgoing traffic is allowed by default.
Combined Ingress and Egress Policy (Controls Both):

#### Combines both ingress and egress rules to control both incoming and outgoing traffic.
If neither ingress nor egress rules are specified, all traffic is allowed by default for the selected pods.
What Happens If You Do Not Mention a Policy?

#### Example Network Policies
##### Deny-All Ingress Traffic:

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  ingress: []
```

Effect: No incoming traffic is allowed to the selected pods.

##### Allow-All Ingress Traffic:
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
spec:
  podSelector: {}
  ingress:
  - {}
```

Effect: All incoming traffic is allowed to the selected pods.

##### Deny-All Egress Traffic:
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-egress
spec:
  podSelector: {}
  egress: []
```
Effect: No outgoing traffic is allowed from the selected pods.

##### Allow-All Egress Traffic:
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-egress
spec:
  podSelector: {}
  egress:
  - {}
```
Effect: All outgoing traffic is allowed from the selected pods.
