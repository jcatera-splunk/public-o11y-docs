.. _otel-kubernetes-config:

*********************************************************************************
Configure the Collector for Kubernetes with Helm
*********************************************************************************

.. meta::
      :description: Optional configurations for the Splunk Distribution of OpenTelemetry Collector for Kubernetes.

After you've :ref:`installed the Collector for Kubernetes <otel-install-k8s>`, read on to learn which settings you can configure. 

Additionally, see:

* :ref:`kubernetes-config-add` 
* :ref:`discovery-mode-k8s`
* :ref:`kubernetes-config-logs`
* :ref:`otel-kubernetes-config-advanced`

For a practical example of how to configure the Collector for Kubernetes see :ref:`about-collector-configuration-tutorial-k8s`.

.. caution:: 

  The :new-page:`values.yaml <https://github.com/signalfx/splunk-otel-collector-chart/blob/main/helm-charts/splunk-otel-collector/values.yaml>` file lists and explains all supported configurable parameters for the Helm chart components. See :ref:`helm-chart-components` for more information. :strong:`Review it to understand how to configure this chart`.

  The Helm chart can also be configured to support different use cases, such as trace sampling and sending data through a proxy server. See :new-page:`Examples of chart configuration <https://github.com/signalfx/splunk-otel-collector-chart/blob/main/examples/README.md>` for more information.

.. _otel-kubernetes-config-distro:

Configure the Kubernetes distribution
============================================

If applicable, use the ``distribution`` parameter to provide information about the underlying Kubernetes deployment. This parameter allows the connector to automatically scrape additional metadata. The following options are supported:

* ``aks`` for Azure AKS
* ``eks`` for Amazon EKS
* ``eks/fargate`` for Amazon EKS with Fargate profiles
* ``gke`` for Google GKE or Standard mode
* ``gke/autopilot`` for Google GKE or Autopilot mode
* ``openshift`` for Red Hat OpenShift

Apply the following to configure your distribution:

.. code-block:: bash

  # aks deployment
  --set distribution=aks,cloudProvider=azure 

  # eks deployment
  --set distribution=eks,cloudProvider=aws 

  # eks/fargate deployment (with recommended gateway)
  --set distribution=eks/fargate,gateway.enabled=true,cloudProvider=aws 

  # gke deployment
  --set distribution=gke,cloudProvider=gcp 

  # gke/autopilot deployment
  --set distribution=gke/autopilot,cloudProvider=gcp 

  # openshift deployment (openshift can run on multiple cloud providers, so cloudProvider is excluded here)
  --set distribution=openshift   

For example:

.. code-block:: yaml

  splunkObservability:
    accessToken: xxxxxx
    realm: us0
  clusterName: my-k8s-cluster
  distribution: gke

Configure Google Kubernetes Engine 
-----------------------------------------------------------------------------

Configure GKE Autopilot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To run the Collector in GKE Autopilot mode, set the ``distribution`` option to ``gke/autopilot``:

.. code-block:: yaml

  distribution: gke/autopilot

Search for "Autopilot overview" on the :new-page:`Google Cloud documentation site <https://cloud.google.com/docs>` for more information.

.. note:: GKE Autopilot doesn't support native OpenTelemetry logs collection.

The Collector agent DaemonSet can have problems scheduling in Autopilot mode. If this happens, do the following to assign the DaemonSet a higher priority class to ensure that the DaemonSet pods are always present on each node:

1. Create a new priority class for the Collector agent:

  .. code-block:: yaml

    cat <<EOF | kubectl apply -f -
    apiVersion: scheduling.k8s.io/v1
    kind: PriorityClass
    metadata:
      name: splunk-otel-agent-priority
    value: 1000000
    globalDefault: false
    description: "Higher priority class for the Splunk Distribution of OpenTelemetry Collector pods."
    EOF

2. Use the created priority class in the helm install/upgrade command using the ``--set="priorityClassName=splunk-otel-agent-priority"`` argument, or add the following line to your custom values.yaml:

  .. code-block:: yaml


    priorityClassName: splunk-otel-agent-priority

GKE ARM support
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The default configuration of the Helm chart supports ARM workloads on GKE. Make sure to set the distribution value to ``gke``:

.. code-block:: yaml


  distribution: gke

.. _config-eks-fargate:

Configure Amazon Elastic Kubernetes Service Fargate
-----------------------------------------------------------------------------

To run the Collector in the Amazon EKS with Fargate profiles, set the required ``distribution`` value to ``eks/fargate``, as shown in the following example:

.. code-block:: yaml

  distribution: eks/fargate

.. note:: Fluentd and native OpenTelemetry logs collection are not automatically configured in EKS with Fargate profiles.

This distribution operates similarly to the ``eks`` distribution, but with the following distinctions:

* The Collector agent DaemonSet is not applied since Fargate does not support DaemonSets. Any desired Collector instances running as agents must be configured manually as sidecar containers in your custom deployments. This includes any application logging services like Fluentd. Set ``gateway.enabled`` to ``true`` and configure your instrumented applications to report metrics, traces, and logs to the gateway ``<installed-chart-name>-splunk-otel-collector`` service address. Any desired agent instances that would run as a DaemonSet should instead run as sidecar containers in your pods.
* Since Fargate nodes use a VM boundary to prevent access to host-based resources used by other pods, pods are not able to reach their own kubelet. The cluster receiver for the Fargate distribution has two primary differences between regular ``eks`` to work around this limitation:
   * The configured cluster receiver is deployed as a two-replica StatefulSet instead of a Deployment, and uses a Kubernetes Observer extension that discovers the cluster's nodes and, on the second replica, its pods for user-configurable receiver creator additions.Using this observer dynamically creates the Kubelet Stats receiver instances that report kubelet metrics for all observed Fargate nodes. The first replica monitors the cluster with a ``k8s_cluster`` receiver, and the second cluster monitors all kubelets except its own (due to an EKS/Fargate networking restriction).
   * The first replica's Collector monitors the second's kubelet. This is made possible by a Fargate-specific ``splunk-otel-eks-fargate-kubeletstats-receiver-node`` node label. The Collector ClusterRole for ``eks/fargate`` allows the ``patch`` verb on ``nodes`` resources for the default API groups to allow the cluster receiver's init container to add this node label for designated self monitoring.

.. _otel-kubernetes-config-clustername:

Configure the cluster name
============================================

Use the ``clusterName`` parameter to specify the name of the Kubernetes cluster. This parameter is optional for the ``eks``, ``eks/fargate``, ``gke``, and ``gke/autopilot`` distributions, but required for all of others.

Apply the following to configure your cluster name:

.. code-block:: bash

  --set clusterName=my-k8s-cluster

For example:

.. code-block:: yaml

  clusterName: my-k8s-cluster

.. _otel-kubernetes-config-environment:

Configure the deployment environment
===========================================

If applicable, use the ``environment`` parameter to specify an additional ``deployment.environment`` attribute to be added to all telemetry data. This attribute helps Splunk Observability Cloud users investigate data coming from different sources separately. Example values include ``development``, ``staging``, and ``production``.

.. code-block:: yaml

  splunkObservability:
    accessToken: xxxxxx
    realm: us0
  environment: production

.. _otel-kubernetes-config-cloud:

Configure a cloud provider
=================================

If applicable, use the ``cloudProvider`` parameter to provide information about your cloud provider. The following options are supported:

* ``aws`` for Amazon Web Services
* ``gcp`` for Google Cloud Platform
* ``azure`` for Microsoft Azure

To set your cloud provider and configure ``cloud.platform`` for the resource detection processor, use: 

.. code-block:: bash

  --set cloudProvider={azure|gcp|eks|openshift} 

For example:

.. code-block:: yaml

  splunkObservability:
    accessToken: xxxxxx
    realm: us0
  clusterName: my-k8s-cluster
  cloudProvider: aws

.. _otel-kubernetes-config-hostnetwork:

Configure the agent's use of the host network
======================================================

By default, ``agent.hostNetwork`` is set to ``true``. This grants DaemonSet pods of the agent access to the node's host network, allowing them to monitor specific elements. Enable this setting to monitor certain control plane components and integrations that require host network access.

Set ``agent.hostNetwork`` to ``false`` to turn off host network access. This might be necessary to comply with certain organization security policies. If host network access is disabled, the agent's monitoring capabilities might be limited.

This value is disregarded for Windows.

Activate AlwaysOn Profiling
=================================

AlwaysOn Profiling in Splunk APM continuously captures stack traces, helping you identify performance bottlenecks or issues in your code. Activating profiling lets your Kubernetes applications produce and forward this data to Splunk Observability Cloud for visualization. 

The Collector ingests profiling data using the ``logs`` pipeline.

Learn more at :ref:`discovery_mode` and :ref:`profiling-intro`.

Set up profiling 
-------------------------------------------

You can activate profiling while installing the Collector for Kubernetes using the UI wizard, or by modifying your configuration files.

Profiling uses two main components: the Collector, responsible for receiving and exporting the profiling data to Splunk Observability Cloud, and the Operator, which auto-instruments applications so they can generate and emit traces along with profiling data. 

There are two main scenarios:

* Profiling using both Collector and Operator: The Operator auto-instruments your applications, which then send the profiling data to the Collector.
* Profiling using only the Collector: You manually instrument your applications to generate profiling data, which is then sent directly to the Collector.

Activate profiling with the Collector and the Operator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To activate profiling with the Collector and the Operator, activate the :guilabel:`Profiling` option in the UI, or deploy the Helm chart with the following configuration:

For the Collector:

.. code-block:: yaml

  splunkObservability:
    accessToken: CHANGEME
    realm: us0
    logsEnabled: true
    profilingEnabled: true

For the Operator:

.. code-block:: yaml

  operatorcrds:
    install: true
  operator:
    enabled: true

With the above configuration:

* The Collector is set up to receive profiling data.
* The Operator is deployed and auto-instruments applications based on target pod annotations, allowing these applications to generate profiling data.

Activate profiling only with the Collector
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to only use the Collector and have manually instrumented applications, ensure that ``splunkObservability.logsEnabled=true`` and ``splunkObservability.profilingEnabled=true`` is set in your configuration.

.. caution:: With this option, you need to manually set up instrumented applications to send profiling data directly to the Collector.

Provide tokens as a secret
=================================

Instead of having the tokens as clear text in the config file, you can provide them as a secret created before deploying the chart. See :new-page:`secret-splunk.yaml <https://github.com/signalfx/splunk-otel-collector-chart/blob/main/helm-charts/splunk-otel-collector/templates/secret-splunk.yaml>` for the required fields.

.. code-block:: yaml

  secret:
    create: false
    name: your-secret

Configure Windows worker nodes
===============================================

The Splunk Distribution of OpenTelemetry Collector for Kubernetes supports collecting metrics, traces, and logs (using OpenTelemetry native logs collection only) from Windows nodes. All Windows images are available in the ``quay.io/signalfx/splunk-otel-collector-windows`` repository.

Use the following configuration to install the Helm chart on Windows worker nodes:

.. code-block:: yaml

  isWindows: true
  image:
    otelcol:
      repository: quay.io/signalfx/splunk-otel-collector-windows
  logsEngine: otel
  readinessProbe:
    initialDelaySeconds: 60
  livenessProbe:
    initialDelaySeconds: 60

If you have both Windows and Linux worker nodes in your Kubernetes cluster, you need to install the Helm chart twice. One of the installations with the default configuration set to ``isWindows: false`` is applied on Linux nodes. The second installation with the values.yaml configuration (shown in the previous example) is applied on Windows nodes.

Deactivate the ``clusterReceiver`` on one of the installations to avoid cluster-wide metrics duplication. To do this, add the following lines to the configuration of one of the installations:

.. code-block:: yaml

  clusterReceiver:
    enabled: false
