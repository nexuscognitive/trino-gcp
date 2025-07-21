# Trino

## Overview

Trino is a fast distributed SQL query engine for big data analytics that helps you explore your data universe. This solution provides a streamlined deployment of Trino on Kubernetes using Google Cloud Marketplace.

For more information on Trino, see the [Trino official documentation](https://trino.io/docs/current/).

## About Google Click to Deploy

Popular open stacks on Kubernetes, packaged by Google.

## Architecture

![Architecture diagram](resources/trino-k8s-app-architecture.png)

The application offers Trino as a single-node deployment.

This deployment includes:
- A Trino coordinator and worker (single-node setup)
- ConfigMap for Trino configuration
- Secret for authentication credentials
- Service for internal communication


## Installation

### Quick install with Google Cloud Marketplace

Get up and running with a few clicks! Install this Trino app to a Google Kubernetes Engine cluster using Google Cloud Marketplace. Follow the [on-screen instructions](https://console.cloud.google.com/marketplace/details/google/trino).

### Command-line instructions

You can use [Google Cloud Shell](https://cloud.google.com/shell/) or a local workstation to complete the following steps.

[![Open in Cloud Shell](http://gstatic.com/cloudssh/images/open-btn.svg)](https://console.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/GoogleCloudPlatform/click-to-deploy&cloudshell_open_in_editor=README.md&cloudshell_working_dir=k8s/trino)

#### Prerequisites

- Set up a Google Kubernetes Engine cluster
- Install and configure kubectl
- Install Application CRD

#### Setting up the environment

```bash
export APP_INSTANCE_NAME=trino-1
export NAMESPACE=default
export TRINO_USER=admin
export TRINO_PASSWORD="$(openssl rand -base64 12)"
```

#### Installing the application

Navigate to the `trino` directory:

```bash
cd k8s/trino
```

##### Configure the application with environment variables

Choose the password for the Trino user:

```bash
export TRINO_PASSWORD="your-password-here"
```

(Optional) Enable public IP access:

```bash
export ENABLE_PUBLIC_SERVICE_AND_INGRESS=true
```

(Optional) Enable Stackdriver metrics:

```bash
export ENABLE_STACKDRIVER_METRICS=true
```

##### Deploy the application

```bash
make app/install
```

### View the application

To get the status of the cluster, run the following command:

```bash
kubectl get pods -l app.kubernetes.io/name=$APP_INSTANCE_NAME
```

### Access Trino

#### Access Trino via port forwarding

To access the Trino web interface, forward the port:

```bash
kubectl port-forward svc/$APP_INSTANCE_NAME 8080:8080
```

Then open [http://localhost:8080](http://localhost:8080) in your browser.

#### Access Trino via Ingress

If you enabled public access during installation, you can access Trino via the Ingress endpoint:

```bash
kubectl get ingress $APP_INSTANCE_NAME-ingress
```

### Using the Trino CLI

You can connect to Trino using the official CLI:

```bash
kubectl exec -it deployment/$APP_INSTANCE_NAME -- trino --server localhost:8080 --user $TRINO_USER
```

## Configuration

### Scaling

This deployment is configured as a single-node setup. For production environments, consider:

1. **Horizontal scaling**: Deploy separate coordinator and worker pods
2. **Resource allocation**: Adjust CPU and memory limits in the deployment
3. **Storage**: Configure persistent storage for data processing

### Authentication

The application uses file-based authentication. The username and password are stored in the `$APP_INSTANCE_NAME-auth` secret.

To retrieve the password:

```bash
kubectl get secret $APP_INSTANCE_NAME-auth -o jsonpath='{.data.password}' | base64 -d
```

### Monitoring

If you enabled Stackdriver metrics during installation, metrics are automatically exported to Google Cloud Monitoring.

## Backup and Restore

Trino is a query engine and doesn't store data itself. However, you may want to backup:

1. **Configuration**: Export ConfigMaps and Secrets
2. **Custom catalogs**: If you've added custom catalog configurations

```bash
# Backup configurations
kubectl get configmap $APP_INSTANCE_NAME-config -o yaml > trino-config-backup.yaml
kubectl get secret $APP_INSTANCE_NAME-auth -o yaml > trino-auth-backup.yaml
```

## Upgrading

### Upgrade from marketplace

The upgrade process may vary depending on your environment and the specific version being upgraded to.

### Upgrade using the command line

1. Update the application image:

```bash
export NEW_IMAGE_TAG=439  # New Trino version
make app/upgrade
```

2. Verify the upgrade:

```bash
kubectl get pods -l app.kubernetes.io/name=$APP_INSTANCE_NAME
```

## Uninstalling

### Using the marketplace

1. In the Google Cloud Console, open [Applications](https://console.cloud.google.com/kubernetes/application).
2. Click the name of your application.
3. In the **Application details** page, click **Delete**.

### Using the command line

```bash
make app/uninstall
```

Or manually:

```bash
kubectl delete application $APP_INSTANCE_NAME
```

## Testing

### Running application tests

The application includes basic connectivity tests:

```bash
make app/verify
```

### Manual testing

1. **Web interface**: Access the Trino web UI and verify the cluster is active
2. **Query execution**: Run a simple query to test functionality:

```sql
SELECT 'Hello, Trino!' as message;
```

3. **Catalog access**: Verify that catalogs are properly configured:

```sql
SHOW CATALOGS;
```

## Troubleshooting

### Pod is not running

Check the pod status and logs:

```bash
kubectl get pods -l app.kubernetes.io/name=$APP_INSTANCE_NAME
kubectl logs deployment/$APP_INSTANCE_NAME
```

### Cannot access Trino web interface

1. Verify the service is running:

```bash
kubectl get svc $APP_INSTANCE_NAME
```

2. Check if port forwarding is active:

```bash
kubectl port-forward svc/$APP_INSTANCE_NAME 8080:8080
```

### Authentication issues

Verify the credentials:

```bash
kubectl get secret $APP_INSTANCE_NAME-auth -o jsonpath='{.data.username}' | base64 -d
kubectl get secret $APP_INSTANCE_NAME-auth -o jsonpath='{.data.password}' | base64 -d
```

### Performance issues

1. **Check resource usage**:

```bash
kubectl top pods -l app.kubernetes.io/name=$APP_INSTANCE_NAME
```

2. **Review memory settings**: Adjust JVM heap size in the ConfigMap
3. **Scale resources**: Increase CPU and memory limits

## Additional Resources

- [Trino Documentation](https://trino.io/docs/current/)
- [Trino Community](https://trino.io/community.html)
- [Trino GitHub Repository](https://github.com/trinodb/trino)
- [Google Kubernetes Engine Documentation](https://cloud.google.com/kubernetes-engine/docs/)

## Support

Google does not offer support for this solution. However, community support is available through:

- [Trino Community Forums](https://stackoverflow.com/questions/tagged/trino)
- [Trino Slack Community](https://trino.io/slack.html)
- [Trino GitHub Issues](https://github.com/trinodb/trino/issues)

## License

This solution is provided under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
