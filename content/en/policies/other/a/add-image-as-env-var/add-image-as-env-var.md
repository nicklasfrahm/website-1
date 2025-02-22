---
title: "Add Image as Environment Variable"
category: Other
version: 
subject: Pod
policyType: "mutate"
description: >
    The Kubernetes downward API only has the ability to express so many options as environment variables. The image consumed in a Pod is commonly needed to make the application aware of some logic it must take. This policy takes the value of the `image` field and adds it as an environment variable to Pods.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/a/add-image-as-env-var/add-image-as-env-var.yaml" target="-blank">/other/a/add-image-as-env-var/add-image-as-env-var.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-image-as-env-var
  annotations:
    pod-policies.kyverno.io/autogen-controllers: none
    policies.kyverno.io/title: Add Image as Environment Variable
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.10.0-alpha.2
    kyverno.io/kubernetes-version: "1.26"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      The Kubernetes downward API only has the ability to express so many
      options as environment variables. The image consumed in a Pod is commonly
      needed to make the application aware of some logic it must take. This policy
      takes the value of the `image` field and adds it as an environment variable
      to Pods.
spec:
  rules:
  - name: pod-containers-inject-image
    match:
      any:
      - resources:
          kinds:
          - Pod
    mutate:
      foreach:
      - list: request.object.spec.containers[]
        patchesJson6902: |-
          - op: add
            path: /spec/containers/{{elementIndex}}/env/-
            value:
              name: K8S_IMAGE
              value: "{{ element.image }}"
```
