# OpenShift Templates

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=Memija_openshift&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=Memija_openshift)

This repository contains a collection of OpenShift resource templates designed to streamline the deployment, configuration, and management of applications on OpenShift.

## Templates

The templates are organized by function:

### Workloads
*   **`deployment.yml`**: Defines a `DeploymentConfig` for the application. It manages the application pods, configuring replicas, resource limits, environment variables, probes (liveness/readiness), and volume mounts for configuration and secrets.
*   **`auto-scaling.yml`**: Defines a `HorizontalPodAutoscaler` to automatically scale the application based on CPU utilization.
*   **`pod-disruption-budget.yml`**: Defines a `PodDisruptionBudget` to ensure a minimum number of replicas are available during voluntary disruptions (e.g., node maintenance).

### Networking
*   **`service.yml`**: Defines a `Service` to expose the application internally. It maps ports 80, 443, and 8009 to container ports 8080, 8443, and 8009 respectively. It also supports serving certificates via annotations.
*   **`route.yml`**: Defines a `Route` to expose the application externally using Passthrough TLS termination.

### Storage & Configuration
*   **`config-map.yml`**: Defines a `ConfigMap` for externalizing application configuration (e.g., `application.properties`).
*   **`storage.yml`**: Defines a `PersistentVolumeClaim` (PVC) for persistent storage needs.
*   **`client-cert.yml`**: Defines a `Secret` for storing client certificates (`client_cert.pfx`).
*   **`server-cert.yml`**: Defines a `Secret` for storing server certificates (`tls.crt`, `tls.key`).
*   **`trust-store.yml`**: Defines a `Secret` for storing the trust store (`trust_store.jks`).
*   **`service-account.yml`**: Defines a `ServiceAccount` for the application.

### Build & Images
*   **`build.yml`**: Defines a `BuildConfig` using the Source strategy (Maven) to build the application image from a Git repository.
*   **`image-stream.yml`**: Defines an `ImageStream` to manage the application's container images.

## Parameters

The templates use parameters (syntax `${parameter_name}`) which should be substituted during processing.

### Common Parameters
*   `application_name`: The name of the application.
*   `environment_name`: The target environment (e.g., dev, test, prod).

### Specific Parameters

**Scaling & Resources**
*   `deployment.replicas_number`: Number of initial replicas.
*   `hpa_min_replicas`: Minimum number of replicas for autoscaling.
*   `hpa_max_replicas`: Maximum number of replicas for autoscaling.
*   `hpa_target_cpu_percent`: Target CPU utilization percentage for autoscaling.
*   `pdb_min_available`: Minimum available replicas for PodDisruptionBudget.
*   `cpu_limit_value`, `memory_limit_value`: Resource limits.
*   `cpu_request_value`, `memory_request_value`: Resource requests.

**Networking**
*   `route_host_name`: The hostname for the route.
*   `route.target_port`: The target port for the route.

**Storage**
*   `storage.maximum_storage_size`: Maximum storage size for PVC.
*   `storage.minimum_storage_size`: Minimum storage size for PVC.
*   `storage.storage_class_name`: Storage class name.

**Build**
*   `git_ref`: Git reference (branch, tag, commit).
*   `git_uri`: Git repository URI.
*   `source_secret_name`: Secret name for Git authentication.
*   `maven_command`: Maven command to execute.
*   `build_image_name`: Base image for the build.
*   `docker_registry_uniform_resource_locator`: Docker registry URL for the image stream.

**Certificates & Secrets**
*   `key_store.client_cert_value`: Content of the client certificate.
*   `server_certificate.server_cert_value`: Content of the server certificate.
*   `server_certificate.server_key_value`: Content of the server private key.
*   `trust_store_value`: Content of the trust store.

## Usage

You can process these templates using the `oc process` command to generate the final Kubernetes resources.

Example:

```bash
oc process -f deployment.yml \
  -p application_name=my-app \
  -p environment_name=dev \
  -p deployment.replicas_number=2 \
  ... | oc apply -f -
```

Make sure to provide all required parameters for the specific template you are using.
