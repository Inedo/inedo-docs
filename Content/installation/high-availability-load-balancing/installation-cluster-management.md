---
title: "Managing your Cluster"
order: 2
---

The Cluster Management Page (found under Admin > Cluster Overview) allows you to monitor your instance server components that are configured for [load balancing and automatic failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing). 

::: (INFO)
Cluster Management is available from ProGet 5.3.16, Otter v3, and BuildMaster v7. 
:::

## Cluster Configuration 

The Cluster Configuration pane displays the settings of your current cluster and your primary and backup service components.

- How many servers are in the cluster.
- If load balancing is enabled.
- If automatic failover is enabled

You can also configure your the heart beat frequency and stale timeout.

*On Inedo products versioned 2022 and earlier, a "primary server" and "backup server" were also displayed. This was removed in 2023, as work is now distributed across servers and this distinction is no longer meaningful.*

## Servers in Cluster 

The Servers in Cluster section lists which servers and components are included in the server cluster.

The server table displays:
- The server name.
- The operating system and version.
- The IP address configured on the server.
- The application components installed on the server. (Web & Service)
- The status of each component and its verification.
  - The current heartbeat status.
  - If the software version is in sync.
  - If the encryption key is in sync.
  
You can also manually remove a component from the cluster if it is no longer in the cluster.

You will also see a warning or error icon next to the server and the component if the component has reached an outdated state. The timeout for the web component is based on a 24-hour window. The timeout for the service component is based on the configuration setting that appears to the right of `automatic failover`.  

## Kubernetes Support

Although we support clustering with Docker, we do not provide instructions for Kubernetes.  Managing Kubernetes configurations requires a certain amount of expertise, that expertise should be able to create a deployment by following our [Docker installation guide](/docs/installation/linux/docker-guide).  However, if you use Kubernetes, here are some tips specific to a Kubernetes cluster with the Inedo products.

- **Ensure the `PROGET_ENCRYPTION_KEY` environment variable is set and consistent across all pods.**  This is required for cross-pod sessions and removes the requirement for sticky sessions in your load balancer.
- Do not set up your cluster with a "dynamic workload".  Typically, traffic increases and decreases too quickly for any schedule to keep up.  Instead, set up a fixed number of pods.
- Do not run two pods on the same physical server.  This defeats the purpose of load balancing, since the bottleneck is the physical network card.
- Minimize pod eviction as much as possible.  
    - Set appropriate resource requests and limits for your pods (with Guaranteed QoS for critical workloads) 
    - Use PriorityClasses to ensure important pods are evicted last. 
    - Monitor node resource usage and ensure you have enough capacity through proper sizing of each node.
- Do not auto-restart containers when the `/health` API reports an error.  Restarting will likely not fix the problem and this tends to make things worse.  Instead identify the root cause of the issue and submit a support ticket if necessary.

::: (Info) (Important Note:)
Internet access is required for automatic activation in Inedo Products.  Moving pods around will often deactivate the product and require reactivation.  If internet access is blocked, each node will need to be manually activated upon creation and after each move.
:::

## Troubleshooting

#### Why am I seeing "Cannot connect to service message"?
The service messenger is a nonessential component that improves the user experience of ProGet by displaying real-time information from service nodes. When it's not available, information in the Web UI may be delayed by a few seconds to a minute.

To get the messenger working, verify that the configured port (default is 33237) is accessible on the server node you clicked on. Then, restart the ProGet service and click the "reconnect" button in the UI.

#### Why is my Encryption showing a description failure?

This will occur when the encryption key is not synchronized across your web servers. The encryption key is stored in your [configuration file](/docs/installation/configuration-files) and needs to be the same on all servers.

#### Why is my web component showing as stale when it is up and running?

The web component heartbeat is updated only when web requests are received on the web server. If there is an interruption of more than 24 hours, such as over a weekend or a sticky session within a load balancer where traffic is not routed to the web server, the web component may display an outdated status.

#### Why is my machine key showing a decryption failure?

In ProGet 5.3 and earlier, this will occur when the machine key is not synchronized across your web servers. See the [Load Balancing & Automatic Failover](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) guide to configure this setting.

#### Why am I constantly prompted to log in?

Ensure your encryption key is set and consistent across all servers.  The encryption key is used to encrypt and decrypt the authentication cookie.  If the encryption key is not set, it will fall back to the machine key, which is unique to each server.  This will cause authentication cookies created on one server to be invalid on another server, resulting in repeated login prompts when requests are routed between servers.  See the [Configure the Encryption Key section in the Configuring Load Balancing & Automatic Failover guide](/docs/installation/high-availability-load-balancing/high-availability-load-balancing#step-2-configure-the-encryptionkey) for more information.