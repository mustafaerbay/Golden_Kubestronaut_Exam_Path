# Introduction to Kubernetes Vertical Pod Autoscaler (VPA)

##  Install VPA Custom Resource Definitions (CRDs)

```
controlplane ~ ➜  cat vpa-crds.yml 
---
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  annotations:
    api-approved.kubernetes.io: https://github.com/kubernetes/kubernetes/pull/63797
    controller-gen.kubebuilder.io/version: v0.9.2
  creationTimestamp: null
  name: verticalpodautoscalercheckpoints.autoscaling.k8s.io
spec:
  group: autoscaling.k8s.io
  names:
    kind: VerticalPodAutoscalerCheckpoint
    listKind: VerticalPodAutoscalerCheckpointList
    plural: verticalpodautoscalercheckpoints
    shortNames:
    - vpacheckpoint
    singular: verticalpodautoscalercheckpoint
  scope: Namespaced
  versions:
  - name: v1
    schema:
      openAPIV3Schema:
        description: VerticalPodAutoscalerCheckpoint is the checkpoint of the internal
          state of VPA that is used for recovery after recommender's restart.
        properties:
          apiVersion:
            description: 'APIVersion defines the versioned schema of this representation
              of an object. Servers should convert recognized schemas to the latest
              internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
            type: string
          kind:
            description: 'Kind is a string value representing the REST resource this
              object represents. Servers may infer this from the endpoint the client
              submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
            type: string
          metadata:
            type: object
          spec:
            description: 'Specification of the checkpoint. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status.'
            properties:
              containerName:
                description: Name of the checkpointed container.
                type: string
              vpaObjectName:
                description: Name of the VPA object that stored VerticalPodAutoscalerCheckpoint
                  object.
                type: string
            type: object
          status:
            description: Data of the checkpoint.
            properties:
              cpuHistogram:
                description: Checkpoint of histogram for consumption of CPU.
                properties:
                  bucketWeights:
                    description: Map from bucket index to bucket weight.
                    type: object
                    x-kubernetes-preserve-unknown-fields: true
                  referenceTimestamp:
                    description: Reference timestamp for samples collected within
                      this histogram.
                    format: date-time
                    nullable: true
                    type: string
                  totalWeight:
                    description: Sum of samples to be used as denominator for weights
                      from BucketWeights.
                    type: number
                type: object
              firstSampleStart:
                description: Timestamp of the fist sample from the histograms.
                format: date-time
                nullable: true
                type: string
              lastSampleStart:
                description: Timestamp of the last sample from the histograms.
                format: date-time
                nullable: true
                type: string
              lastUpdateTime:
                description: The time when the status was last refreshed.
                format: date-time
                nullable: true
                type: string
              memoryHistogram:
                description: Checkpoint of histogram for consumption of memory.
                properties:
                  bucketWeights:
                    description: Map from bucket index to bucket weight.
                    type: object
                    x-kubernetes-preserve-unknown-fields: true
                  referenceTimestamp:
                    description: Reference timestamp for samples collected within
                      this histogram.
                    format: date-time
                    nullable: true
                    type: string
                  totalWeight:
                    description: Sum of samples to be used as denominator for weights
                      from BucketWeights.
                    type: number
                type: object
              totalSamplesCount:
                description: Total number of samples in the histograms.
                type: integer
              version:
                description: Version of the format of the stored data.
                type: string
            type: object
        type: object
    served: true
    storage: true
  - name: v1beta2
    schema:
      openAPIV3Schema:
        description: VerticalPodAutoscalerCheckpoint is the checkpoint of the internal
          state of VPA that is used for recovery after recommender's restart.
        properties:
          apiVersion:
            description: 'APIVersion defines the versioned schema of this representation
              of an object. Servers should convert recognized schemas to the latest
              internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
            type: string
          kind:
            description: 'Kind is a string value representing the REST resource this
              object represents. Servers may infer this from the endpoint the client
              submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
            type: string
          metadata:
            type: object
          spec:
            description: 'Specification of the checkpoint. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status.'
            properties:
              containerName:
                description: Name of the checkpointed container.
                type: string
              vpaObjectName:
                description: Name of the VPA object that stored VerticalPodAutoscalerCheckpoint
                  object.
                type: string
            type: object
          status:
            description: Data of the checkpoint.
            properties:
              cpuHistogram:
                description: Checkpoint of histogram for consumption of CPU.
                properties:
                  bucketWeights:
                    description: Map from bucket index to bucket weight.
                    type: object
                    x-kubernetes-preserve-unknown-fields: true
                  referenceTimestamp:
                    description: Reference timestamp for samples collected within
                      this histogram.
                    format: date-time
                    nullable: true
                    type: string
                  totalWeight:
                    description: Sum of samples to be used as denominator for weights
                      from BucketWeights.
                    type: number
                type: object
              firstSampleStart:
                description: Timestamp of the fist sample from the histograms.
                format: date-time
                nullable: true
                type: string
              lastSampleStart:
                description: Timestamp of the last sample from the histograms.
                format: date-time
                nullable: true
                type: string
              lastUpdateTime:
                description: The time when the status was last refreshed.
                format: date-time
                nullable: true
                type: string
              memoryHistogram:
                description: Checkpoint of histogram for consumption of memory.
                properties:
                  bucketWeights:
                    description: Map from bucket index to bucket weight.
                    type: object
                    x-kubernetes-preserve-unknown-fields: true
                  referenceTimestamp:
                    description: Reference timestamp for samples collected within
                      this histogram.
                    format: date-time
                    nullable: true
                    type: string
                  totalWeight:
                    description: Sum of samples to be used as denominator for weights
                      from BucketWeights.
                    type: number
                type: object
              totalSamplesCount:
                description: Total number of samples in the histograms.
                type: integer
              version:
                description: Version of the format of the stored data.
                type: string
            type: object
        type: object
    served: true
    storage: false
---
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  annotations:
    api-approved.kubernetes.io: https://github.com/kubernetes/kubernetes/pull/63797
    controller-gen.kubebuilder.io/version: v0.9.2
  creationTimestamp: null
  name: verticalpodautoscalers.autoscaling.k8s.io
spec:
  group: autoscaling.k8s.io
  names:
    kind: VerticalPodAutoscaler
    listKind: VerticalPodAutoscalerList
    plural: verticalpodautoscalers
    shortNames:
    - vpa
    singular: verticalpodautoscaler
  scope: Namespaced
  versions:
  - additionalPrinterColumns:
    - jsonPath: .spec.updatePolicy.updateMode
      name: Mode
      type: string
    - jsonPath: .status.recommendation.containerRecommendations[0].target.cpu
      name: CPU
      type: string
    - jsonPath: .status.recommendation.containerRecommendations[0].target.memory
      name: Mem
      type: string
    - jsonPath: .status.conditions[?(@.type=='RecommendationProvided')].status
      name: Provided
      type: string
    - jsonPath: .metadata.creationTimestamp
      name: Age
      type: date
    name: v1
    schema:
      openAPIV3Schema:
        description: VerticalPodAutoscaler is the configuration for a vertical pod
          autoscaler, which automatically manages pod resources based on historical
          and real time resource utilization.
        properties:
          apiVersion:
            description: 'APIVersion defines the versioned schema of this representation
              of an object. Servers should convert recognized schemas to the latest
              internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
            type: string
          kind:
            description: 'Kind is a string value representing the REST resource this
              object represents. Servers may infer this from the endpoint the client
              submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
            type: string
          metadata:
            type: object
          spec:
            description: 'Specification of the behavior of the autoscaler. More info:
              https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status.'
            properties:
              recommenders:
                description: Recommender responsible for generating recommendation
                  for this object. List should be empty (then the default recommender
                  will generate the recommendation) or contain exactly one recommender.
                items:
                  description: VerticalPodAutoscalerRecommenderSelector points to
                    a specific Vertical Pod Autoscaler recommender. In the future
                    it might pass parameters to the recommender.
                  properties:
                    name:
                      description: Name of the recommender responsible for generating
                        recommendation for this object.
                      type: string
                  required:
                  - name
                  type: object
                type: array
              resourcePolicy:
                description: Controls how the autoscaler computes recommended resources.
                  The resource policy may be used to set constraints on the recommendations
                  for individual containers. If not specified, the autoscaler computes
                  recommended resources for all containers in the pod, without additional
                  constraints.
                properties:
                  containerPolicies:
                    description: Per-container resource policies.
                    items:
                      description: ContainerResourcePolicy controls how autoscaler
                        computes the recommended resources for a specific container.
                      properties:
                        containerName:
                          description: Name of the container or DefaultContainerResourcePolicy,
                            in which case the policy is used by the containers that
                            don't have their own policy specified.
                          type: string
                        controlledResources:
                          description: Specifies the type of recommendations that
                            will be computed (and possibly applied) by VPA. If not
                            specified, the default of [ResourceCPU, ResourceMemory]
                            will be used.
                          items:
                            description: ResourceName is the name identifying various
                              resources in a ResourceList.
                            type: string
                          type: array
                        controlledValues:
                          description: Specifies which resource values should be controlled.
                            The default is "RequestsAndLimits".
                          enum:
                          - RequestsAndLimits
                          - RequestsOnly
                          type: string
                        maxAllowed:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Specifies the maximum amount of resources that
                            will be recommended for the container. The default is
                            no maximum.
                          type: object
                        minAllowed:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Specifies the minimal amount of resources that
                            will be recommended for the container. The default is
                            no minimum.
                          type: object
                        mode:
                          description: Whether autoscaler is enabled for the container.
                            The default is "Auto".
                          enum:
                          - Auto
                          - "Off"
                          type: string
                      type: object
                    type: array
                type: object
              targetRef:
                description: TargetRef points to the controller managing the set of
                  pods for the autoscaler to control - e.g. Deployment, StatefulSet.
                  VerticalPodAutoscaler can be targeted at controller implementing
                  scale subresource (the pod set is retrieved from the controller's
                  ScaleStatus) or some well known controllers (e.g. for DaemonSet
                  the pod set is read from the controller's spec). If VerticalPodAutoscaler
                  cannot use specified target it will report ConfigUnsupported condition.
                  Note that VerticalPodAutoscaler does not require full implementation
                  of scale subresource - it will not use it to modify the replica
                  count. The only thing retrieved is a label selector matching pods
                  grouped by the target resource.
                properties:
                  apiVersion:
                    description: API version of the referent
                    type: string
                  kind:
                    description: 'Kind of the referent; More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
                    type: string
                  name:
                    description: 'Name of the referent; More info: http://kubernetes.io/docs/user-guide/identifiers#names'
                    type: string
                required:
                - kind
                - name
                type: object
                x-kubernetes-map-type: atomic
              updatePolicy:
                description: Describes the rules on how changes are applied to the
                  pods. If not specified, all fields in the `PodUpdatePolicy` are
                  set to their default values.
                properties:
                  evictionRequirements:
                    description: EvictionRequirements is a list of EvictionRequirements
                      that need to evaluate to true in order for a Pod to be evicted.
                      If more than one EvictionRequirement is specified, all of them
                      need to be fulfilled to allow eviction.
                    items:
                      description: EvictionRequirement defines a single condition
                        which needs to be true in order to evict a Pod
                      properties:
                        changeRequirement:
                          description: EvictionChangeRequirement refers to the relationship
                            between the new target recommendation for a Pod and its
                            current requests, what kind of change is necessary for
                            the Pod to be evicted
                          enum:
                          - TargetHigherThanRequests
                          - TargetLowerThanRequests
                          type: string
                        resource:
                          description: Resources is a list of one or more resources
                            that the condition applies to. If more than one resource
                            is given, the EvictionRequirement is fulfilled if at least
                            one resource meets `changeRequirement`.
                          items:
                            description: ResourceName is the name identifying various
                              resources in a ResourceList.
                            type: string
                          type: array
                      required:
                      - changeRequirement
                      - resource
                      type: object
                    type: array
                  minReplicas:
                    description: Minimal number of replicas which need to be alive
                      for Updater to attempt pod eviction (pending other checks like
                      PDB). Only positive values are allowed. Overrides global '--min-replicas'
                      flag.
                    format: int32
                    type: integer
                  updateMode:
                    description: Controls when autoscaler applies changes to the pod
                      resources. The default is 'Auto'.
                    enum:
                    - "Off"
                    - Initial
                    - Recreate
                    - Auto
                    type: string
                type: object
            required:
            - targetRef
            type: object
          status:
            description: Current information about the autoscaler.
            properties:
              conditions:
                description: Conditions is the set of conditions required for this
                  autoscaler to scale its target, and indicates whether or not those
                  conditions are met.
                items:
                  description: VerticalPodAutoscalerCondition describes the state
                    of a VerticalPodAutoscaler at a certain point.
                  properties:
                    lastTransitionTime:
                      description: lastTransitionTime is the last time the condition
                        transitioned from one status to another
                      format: date-time
                      type: string
                    message:
                      description: message is a human-readable explanation containing
                        details about the transition
                      type: string
                    reason:
                      description: reason is the reason for the condition's last transition.
                      type: string
                    status:
                      description: status is the status of the condition (True, False,
                        Unknown)
                      type: string
                    type:
                      description: type describes the current condition
                      type: string
                  required:
                  - status
                  - type
                  type: object
                type: array
              recommendation:
                description: The most recently computed amount of resources recommended
                  by the autoscaler for the controlled pods.
                properties:
                  containerRecommendations:
                    description: Resources recommended by the autoscaler for each
                      container.
                    items:
                      description: RecommendedContainerResources is the recommendation
                        of resources computed by autoscaler for a specific container.
                        Respects the container resource policy if present in the spec.
                        In particular the recommendation is not produced for containers
                        with `ContainerScalingMode` set to 'Off'.
                      properties:
                        containerName:
                          description: Name of the container.
                          type: string
                        lowerBound:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Minimum recommended amount of resources. Observes
                            ContainerResourcePolicy. This amount is not guaranteed
                            to be sufficient for the application to operate in a stable
                            way, however running with less resources is likely to
                            have significant impact on performance/availability.
                          type: object
                        target:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Recommended amount of resources. Observes ContainerResourcePolicy.
                          type: object
                        uncappedTarget:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: The most recent recommended resources target
                            computed by the autoscaler for the controlled pods, based
                            only on actual resource usage, not taking into account
                            the ContainerResourcePolicy. May differ from the Recommendation
                            if the actual resource usage causes the target to violate
                            the ContainerResourcePolicy (lower than MinAllowed or
                            higher that MaxAllowed). Used only as status indication,
                            will not affect actual resource assignment.
                          type: object
                        upperBound:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Maximum recommended amount of resources. Observes
                            ContainerResourcePolicy. Any resources allocated beyond
                            this value are likely wasted. This value may be larger
                            than the maximum amount of application is actually capable
                            of consuming.
                          type: object
                      required:
                      - target
                      type: object
                    type: array
                type: object
            type: object
        required:
        - spec
        type: object
    served: true
    storage: true
    subresources:
      status: {}
  - deprecated: true
    deprecationWarning: autoscaling.k8s.io/v1beta2 API is deprecated
    name: v1beta2
    schema:
      openAPIV3Schema:
        description: VerticalPodAutoscaler is the configuration for a vertical pod
          autoscaler, which automatically manages pod resources based on historical
          and real time resource utilization.
        properties:
          apiVersion:
            description: 'APIVersion defines the versioned schema of this representation
              of an object. Servers should convert recognized schemas to the latest
              internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
            type: string
          kind:
            description: 'Kind is a string value representing the REST resource this
              object represents. Servers may infer this from the endpoint the client
              submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
            type: string
          metadata:
            type: object
          spec:
            description: 'Specification of the behavior of the autoscaler. More info:
              https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status.'
            properties:
              resourcePolicy:
                description: Controls how the autoscaler computes recommended resources.
                  The resource policy may be used to set constraints on the recommendations
                  for individual containers. If not specified, the autoscaler computes
                  recommended resources for all containers in the pod, without additional
                  constraints.
                properties:
                  containerPolicies:
                    description: Per-container resource policies.
                    items:
                      description: ContainerResourcePolicy controls how autoscaler
                        computes the recommended resources for a specific container.
                      properties:
                        containerName:
                          description: Name of the container or DefaultContainerResourcePolicy,
                            in which case the policy is used by the containers that
                            don't have their own policy specified.
                          type: string
                        maxAllowed:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Specifies the maximum amount of resources that
                            will be recommended for the container. The default is
                            no maximum.
                          type: object
                        minAllowed:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Specifies the minimal amount of resources that
                            will be recommended for the container. The default is
                            no minimum.
                          type: object
                        mode:
                          description: Whether autoscaler is enabled for the container.
                            The default is "Auto".
                          enum:
                          - Auto
                          - "Off"
                          type: string
                      type: object
                    type: array
                type: object
              targetRef:
                description: TargetRef points to the controller managing the set of
                  pods for the autoscaler to control - e.g. Deployment, StatefulSet.
                  VerticalPodAutoscaler can be targeted at controller implementing
                  scale subresource (the pod set is retrieved from the controller's
                  ScaleStatus) or some well known controllers (e.g. for DaemonSet
                  the pod set is read from the controller's spec). If VerticalPodAutoscaler
                  cannot use specified target it will report ConfigUnsupported condition.
                  Note that VerticalPodAutoscaler does not require full implementation
                  of scale subresource - it will not use it to modify the replica
                  count. The only thing retrieved is a label selector matching pods
                  grouped by the target resource.
                properties:
                  apiVersion:
                    description: API version of the referent
                    type: string
                  kind:
                    description: 'Kind of the referent; More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
                    type: string
                  name:
                    description: 'Name of the referent; More info: http://kubernetes.io/docs/user-guide/identifiers#names'
                    type: string
                required:
                - kind
                - name
                type: object
                x-kubernetes-map-type: atomic
              updatePolicy:
                description: Describes the rules on how changes are applied to the
                  pods. If not specified, all fields in the `PodUpdatePolicy` are
                  set to their default values.
                properties:
                  updateMode:
                    description: Controls when autoscaler applies changes to the pod
                      resources. The default is 'Auto'.
                    enum:
                    - "Off"
                    - Initial
                    - Recreate
                    - Auto
                    type: string
                type: object
            required:
            - targetRef
            type: object
          status:
            description: Current information about the autoscaler.
            properties:
              conditions:
                description: Conditions is the set of conditions required for this
                  autoscaler to scale its target, and indicates whether or not those
                  conditions are met.
                items:
                  description: VerticalPodAutoscalerCondition describes the state
                    of a VerticalPodAutoscaler at a certain point.
                  properties:
                    lastTransitionTime:
                      description: lastTransitionTime is the last time the condition
                        transitioned from one status to another
                      format: date-time
                      type: string
                    message:
                      description: message is a human-readable explanation containing
                        details about the transition
                      type: string
                    reason:
                      description: reason is the reason for the condition's last transition.
                      type: string
                    status:
                      description: status is the status of the condition (True, False,
                        Unknown)
                      type: string
                    type:
                      description: type describes the current condition
                      type: string
                  required:
                  - status
                  - type
                  type: object
                type: array
              recommendation:
                description: The most recently computed amount of resources recommended
                  by the autoscaler for the controlled pods.
                properties:
                  containerRecommendations:
                    description: Resources recommended by the autoscaler for each
                      container.
                    items:
                      description: RecommendedContainerResources is the recommendation
                        of resources computed by autoscaler for a specific container.
                        Respects the container resource policy if present in the spec.
                        In particular the recommendation is not produced for containers
                        with `ContainerScalingMode` set to 'Off'.
                      properties:
                        containerName:
                          description: Name of the container.
                          type: string
                        lowerBound:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Minimum recommended amount of resources. Observes
                            ContainerResourcePolicy. This amount is not guaranteed
                            to be sufficient for the application to operate in a stable
                            way, however running with less resources is likely to
                            have significant impact on performance/availability.
                          type: object
                        target:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Recommended amount of resources. Observes ContainerResourcePolicy.
                          type: object
                        uncappedTarget:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: The most recent recommended resources target
                            computed by the autoscaler for the controlled pods, based
                            only on actual resource usage, not taking into account
                            the ContainerResourcePolicy. May differ from the Recommendation
                            if the actual resource usage causes the target to violate
                            the ContainerResourcePolicy (lower than MinAllowed or
                            higher that MaxAllowed). Used only as status indication,
                            will not affect actual resource assignment.
                          type: object
                        upperBound:
                          additionalProperties:
                            anyOf:
                            - type: integer
                            - type: string
                            pattern: ^(\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))(([KMGTPE]i)|[numkMGTPE]|([eE](\+|-)?(([0-9]+(\.[0-9]*)?)|(\.[0-9]+))))?$
                            x-kubernetes-int-or-string: true
                          description: Maximum recommended amount of resources. Observes
                            ContainerResourcePolicy. Any resources allocated beyond
                            this value are likely wasted. This value may be larger
                            than the maximum amount of application is actually capable
                            of consuming.
                          type: object
                      required:
                      - target
                      type: object
                    type: array
                type: object
            type: object
        required:
        - spec
        type: object
    served: true
    storage: false
    subresources:
      status: {}
```

## Install VPA Role-Based Access Control (RBAC)


```
controlplane ~ ➜  cat vpa-rbac.yml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:metrics-reader
rules:
  - apiGroups:
      - "metrics.k8s.io"
    resources:
      - pods
    verbs:
      - get
      - list
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-actor
rules:
  - apiGroups:
      - ""
    resources:
      - pods
      - nodes
      - limitranges
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - ""
    resources:
      - events
    verbs:
      - get
      - list
      - watch
      - create
  - apiGroups:
      - "poc.autoscaling.k8s.io"
    resources:
      - verticalpodautoscalers
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "autoscaling.k8s.io"
    resources:
      - verticalpodautoscalers
    verbs:
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-status-actor
rules:
  - apiGroups:
      - "autoscaling.k8s.io"
    resources:
      - verticalpodautoscalers/status
    verbs:
      - get
      - patch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-checkpoint-actor
rules:
  - apiGroups:
      - "poc.autoscaling.k8s.io"
    resources:
      - verticalpodautoscalercheckpoints
    verbs:
      - get
      - list
      - watch
      - create
      - patch
      - delete
  - apiGroups:
      - "autoscaling.k8s.io"
    resources:
      - verticalpodautoscalercheckpoints
    verbs:
      - get
      - list
      - watch
      - create
      - patch
      - delete
  - apiGroups:
      - ""
    resources:
      - namespaces
    verbs:
      - get
      - list
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:evictioner
rules:
  - apiGroups:
      - "apps"
      - "extensions"
    resources:
      - replicasets
    verbs:
      - get
  - apiGroups:
      - ""
    resources:
      - pods/eviction
    verbs:
      - create
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:metrics-reader
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:metrics-reader
subjects:
  - kind: ServiceAccount
    name: vpa-recommender
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-actor
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-actor
subjects:
  - kind: ServiceAccount
    name: vpa-recommender
    namespace: kube-system
  - kind: ServiceAccount
    name: vpa-updater
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-status-actor
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-status-actor
subjects:
  - kind: ServiceAccount
    name: vpa-recommender
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-checkpoint-actor
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-checkpoint-actor
subjects:
  - kind: ServiceAccount
    name: vpa-recommender
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-target-reader
rules:
  - apiGroups:
    - '*'
    resources:
    - '*/scale'
    verbs:
    - get
    - watch
  - apiGroups:
      - ""
    resources:
      - replicationcontrollers
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - apps
    resources:
      - daemonsets
      - deployments
      - replicasets
      - statefulsets
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - batch
    resources:
      - jobs
      - cronjobs
    verbs:
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-target-reader-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-target-reader
subjects:
  - kind: ServiceAccount
    name: vpa-recommender
    namespace: kube-system
  - kind: ServiceAccount
    name: vpa-admission-controller
    namespace: kube-system
  - kind: ServiceAccount
    name: vpa-updater
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-evictioner-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:evictioner
subjects:
  - kind: ServiceAccount
    name: vpa-updater
    namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: vpa-admission-controller
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: vpa-recommender
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: vpa-updater
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-admission-controller
rules:
  - apiGroups:
      - ""
    resources:
      - pods
      - configmaps
      - nodes
      - limitranges
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "admissionregistration.k8s.io"
    resources:
      - mutatingwebhookconfigurations
    verbs:
      - create
      - delete
      - get
      - list
  - apiGroups:
      - "poc.autoscaling.k8s.io"
    resources:
      - verticalpodautoscalers
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "autoscaling.k8s.io"
    resources:
      - verticalpodautoscalers
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - "coordination.k8s.io"
    resources:
      - leases
    verbs:
      - create
      - update
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-admission-controller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-admission-controller
subjects:
  - kind: ServiceAccount
    name: vpa-admission-controller
    namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:vpa-status-reader
rules:
  - apiGroups:
      - "coordination.k8s.io"
    resources:
      - leases
    verbs:
      - get
      - list
      - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:vpa-status-reader-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:vpa-status-reader
subjects:
  - kind: ServiceAccount
    name: vpa-updater
    namespace: kube-system
```

## Clone the VPA Repository and Set Up the Vertical Pod Autoscaler

```
Steps:
Clone the repository:

First, navigate to the /root directory and clone the repository:

  git clone https://github.com/kubernetes/autoscaler.git

Navigate to the Vertical Pod Autoscaler directory:

After cloning, move into the vertical-pod-autoscaler directory:

   cd autoscaler/vertical-pod-autoscaler

Run the setup script:

Execute the provided script to deploy the Vertical Pod Autoscaler:

   ./hack/vpa-up.sh

By following these steps, the Vertical Pod Autoscaler will be installed and ready to manage pod resources in your Kubernetes cluster.
```


```
controlplane ~ ✖    cd autoscaler/vertical-pod-autoscaler

controlplane autoscaler/vertical-pod-autoscaler on  master via 🐹 ➜     ./hack/vpa-up.sh
HEAD is now at a8ca31655 Merge pull request #8612 from adrianmoisey/vpa-release-1.5
customresourcedefinition.apiextensions.k8s.io/verticalpodautoscalercheckpoints.autoscaling.k8s.io configured
customresourcedefinition.apiextensions.k8s.io/verticalpodautoscalers.autoscaling.k8s.io configured
clusterrole.rbac.authorization.k8s.io/system:metrics-reader unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-actor configured
clusterrole.rbac.authorization.k8s.io/system:vpa-status-actor unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-checkpoint-actor unchanged
clusterrole.rbac.authorization.k8s.io/system:evictioner unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-updater-in-place created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-updater-in-place-binding created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-reader unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-actor unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-status-actor unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-checkpoint-actor unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-target-reader unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-target-reader-binding unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-evictioner-binding unchanged
serviceaccount/vpa-admission-controller unchanged
serviceaccount/vpa-recommender unchanged
serviceaccount/vpa-updater unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-admission-controller configured
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-admission-controller unchanged
clusterrole.rbac.authorization.k8s.io/system:vpa-status-reader unchanged
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-status-reader-binding unchanged
role.rbac.authorization.k8s.io/system:leader-locking-vpa-updater created
rolebinding.rbac.authorization.k8s.io/system:leader-locking-vpa-updater created
role.rbac.authorization.k8s.io/system:leader-locking-vpa-recommender created
rolebinding.rbac.authorization.k8s.io/system:leader-locking-vpa-recommender created
deployment.apps/vpa-updater created
deployment.apps/vpa-recommender created
Generating certs for the VPA Admission Controller in /tmp/vpa-certs.
Certificate request self-signature ok
subject=CN = vpa-webhook.kube-system.svc
Uploading certs to the cluster.
secret/vpa-tls-certs created
Deleting /tmp/vpa-certs.
service/vpa-webhook created
deployment.apps/vpa-admission-controller created
service/vpa-webhook unchanged

```

```
controlplane ~ ➜  k get deployments -A
NAMESPACE     NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
default       flask-app                  1/1     1            1           72s
kube-system   coredns                    2/2     2            2           73m
kube-system   vpa-admission-controller   1/1     1            1           4m12s
kube-system   vpa-recommender            1/1     1            1           4m12s
kube-system   vpa-updater                1/1     1            1           4m12s
```

------------------

You have recently deployed a Flask application to your Kubernetes cluster. However, the Vertical Pod Autoscaler (VPA) vpa-updater-XXXX pod indicates that there may be an issue with the newly deployed flask-app pods.

Inspect the logs of the vpa-updater-XXXX pod and observe the following message:

Check the logs of the vpa-updater-XXXX pod to identify any potential issues with the flask-app deployment.
When checking the logs, you see the following error message:

>pods_eviction_restriction.go:226] **too few replicas** for **ReplicaSet** default/**flask-app-b6c9c4f78**

Problem Analysis:

Flask application is running with only 1 replica pod.
The Vertical Pod Autoscaler (VPA) needs to evict (remove) the existing pod to create a new one with updated resource settings.
Kubernetes has a safety feature that prevents removing the last pod of a deployment to avoid service downtime.
When you have only 1 replica and VPA tries to evict it, Kubernetes blocks this action with the error message: "too few replicas".
VPA wants to optimize your pod's resources but cannot because Kubernetes is protecting your service availability.
As a result, VPA cannot apply its resource recommendations, and application cannot benefit from automatic resource optimization.
Approach to Resolve the Issue:
1. Increase the replica count:

```
kubectl scale deployment flask-app --replicas=2
```

2. Verify the Deployment:

```
kubectl get deployment flask-app -o wide
```


Ensure that the DESIRED column shows the updated replica count, and the CURRENT column matches the desired number.

3. Check the Pod Status:

```
kubectl get pods -l app=flask-app
```


Wait until all pods show Running status.
You should see two pods (or more) in a Running state.

4. Verify VPA operation:

```
controlplane ~ ➜  k get vpa
NAME        MODE       CPU    MEM     PROVIDED   AGE
flask-app   Recreate   100m   250Mi   True       4m10s

```


```
controlplane ~ ➜  kubectl describe vpa flask-app
Name:         flask-app
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  autoscaling.k8s.io/v1
Kind:         VerticalPodAutoscaler
Metadata:
  Creation Timestamp:  2026-02-06T07:05:23Z
  Generation:          1
  Resource Version:    6838
  UID:                 9655eb13-b47e-42e5-9d98-f2961cc3d46a
Spec:
  Resource Policy:
    Container Policies:
      Container Name:  *
      Controlled Resources:
        cpu
        memory
      Max Allowed:
        Cpu:     1
        Memory:  500Mi
      Min Allowed:
        Cpu:     100m
        Memory:  100Mi
  Target Ref:
    API Version:  apps/v1
    Kind:         Deployment
    Name:         flask-app
  Update Policy:
    Eviction Requirements:
      Change Requirement:  TargetHigherThanRequests
      Resources:
        cpu
        memory
    Update Mode:  Recreate
Status:
  Conditions:
    Last Transition Time:  2026-02-06T07:06:27Z
    Status:                True
    Type:                  RecommendationProvided
  Recommendation:
    Container Recommendations:
      Container Name:  flask-app
      Lower Bound:
        Cpu:     100m
        Memory:  250Mi
      Target:
        Cpu:     100m
        Memory:  250Mi
      Uncapped Target:
        Cpu:     25m
        Memory:  250Mi
      Upper Bound:
        Cpu:     100m
        Memory:  250Mi
Events:
  Type    Reason      Age   From         Message
  ----    ------      ----  ----         -------
  Normal  EvictedPod  26s   vpa-updater  VPA Updater evicted Pod flask-app-84d5df7d4c-mvf7r to apply resource recommendation.
```

This will show the current state of the VPA and any recommendations it has made. If it's working properly, you should see resource recommendations (for CPU and memory) in the output.

With 2 replicas, Kubernetes can safely remove one pod while keeping your application running, allowing VPA to work properly.