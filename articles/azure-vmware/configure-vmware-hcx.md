---
title: Configure VMware HCX in Azure VMware Solution
description: Configure the on-premises VMware HCX Connector for your Azure VMware Solution private cloud. 
ms.topic: tutorial
ms.date: 07/30/2021
---

# Configure on-premises VMware HCX Connector

Once you've [installed the VMware HCX add-on](install-vmware-hcx.md), you're ready to configure the on-premises VMware HCX Connector for your Azure VMware Solution private cloud.  

In this how-to, you'll:

* Pair your on-premises VMware HCX Connector with your Azure VMware Solution HCX Cloud Manager
* Configure the network profile, compute profile, and service mesh
* Check the appliance status and validate that migration is possible

After you complete these steps, you'll have a production-ready environment for creating virtual machines (VMs) and migration. 

## Prerequisites

- [VMware HCX Connector](install-vmware-hcx.md) has been installed.

- If you plan to use VMware HCX Enterprise, make sure you've enabled the [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) add-on through a [support request](https://portal.azure.com/#create/Microsoft.Support).

- [Software version requirements](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)

- Your on-premises vSphere environment (source environment) meets the [minimum requirements](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

- [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) is configured between on-premises and Azure VMware Solution private cloud ExpressRoute circuits.

- [All required ports](https://ports.vmware.com/home/VMware-HCX) are open for communication between on-premises components and Azure VMware Solution private.

- [Define VMware HCX network segments](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  The primary use cases for VMware HCX are workload migrations and disaster recovery.

- Review the [VMware HCX Documentation](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-BFD7E194-CFE5-4259-B74B-991B26A51758.html) for information on using HCX.

## Add a site pairing

In your data center, you can connect or pair the VMware HCX Cloud Manager in Azure VMware Solution with the VMware HCX Connector.

> [!IMPORTANT]
> Although the VMware Configuration Maximum tool describes site pairs maximum to be 25 between the on-premises HCX Connector and HCX Cloud Manager, licensing limits this to three for HCX Advanced and 10 for HCX Enterprise Edition.

1. Sign in to your on-premises vCenter, and under **Home**, select **HCX**.

1. Under **Infrastructure**, select **Site Pairing** and select the **Connect To Remote Site** option (in the middle of the screen).

1. Enter the Azure VMware Solution HCX Cloud Manager URL or IP address that you noted earlier `https://x.x.x.9`, the Azure VMware Solution cloudadmin\@vsphere.local username, and the password. Then select **Connect**.

   > [!NOTE]
   > To successfully establish a site pair:
   > * Your VMware HCX Connector must be able to route to your HCX Cloud Manager IP over port 443.
   >
   > * Use the same password that you used to sign in to vCenter. You defined this password on the initial deployment screen.

   You'll see a screen showing that your VMware HCX Cloud Manager in Azure VMware Solution and your on-premises VMware HCX Connector are connected (paired).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot showing the site pairing of the HCX Manager in Azure VMware Solution and the VMware HCX Connector.":::

For an end-to-end overview of this procedure, view the [Azure VMware Solution: HCX Site Pairing](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) video.

## Create network profiles

VMware HCX Connector deploys a subset of virtual appliances (automated) that require multiple IP segments. When you create your network profiles, you use the IP segments you identified during the [planning phase](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments).  You'll create four network profiles:

   - Management
   - vMotion
   - Replication
   - Uplink

1. Under **Infrastructure**, select **Interconnect** > **Multi-Site Service Mesh** > **Network Profiles** > **Create Network Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot showing where to create a network profile in the vSphere Client." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. For each network profile, select the network and port group, provide a name, and create the segment's IP pool. Then select **Create**.

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot showing the details for a new network profile." lightbox="media/tutorial-vmware-hcx/example-configurations-network-profile.png":::

For an end-to-end overview of this procedure, view the [Azure VMware Solution: HCX Network Profile](https://www.youtube.com/embed/O0rU4jtXUxc) video.

## Create a compute profile

1. Under **Infrastructure**, select **Interconnect** > **Compute Profiles** > **Create Compute Profile**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot that shows the selections for starting to create a compute profile." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Enter a name for the profile and select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot that shows the entry of a compute profile name and the Continue button." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Select the services to enable, such as migration, network extension, or disaster recovery, and then select **Continue**.

   > [!NOTE]
   > Generally, nothing changes here.

1. In **Select Service Resources**, select one or more service resources (clusters) to enable the selected VMware HCX services.

1. When you see the clusters in your on-premises datacenter, select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot that shows selected service resources and the Continue button." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. From **Select Datastore**, select the datastore storage resource for deploying the VMware HCX Interconnect appliances. Then select **Continue**.

   When multiple resources are selected, VMware HCX uses the first resource selected until its capacity is exhausted.

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot that shows a selected data storage resource and the Continue button." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::

1. From **Select Management Network Profile**, select the management network profile that you created in previous steps. Then select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot that shows the selection of a management network profile and the Continue button." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. From **Select Uplink Network Profile**, select the uplink network profile you created in the previous procedure. Then select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot that shows the selection of an uplink network profile and the Continue button." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. From **Select vMotion Network Profile**, select the vMotion network profile that you created in previous steps. Then select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot that shows the selection of a vMotion network profile and the Continue button." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. From **Select vSphere Replication Network Profile**, select the replication network profile that you created in previous steps. Then select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot that shows the selection of a replication network profile and the Continue button." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. From **Select Distributed Switches for Network Extensions**, select the switches containing the virtual machines to be migrated to Azure VMware Solution on a layer-2 extended network. Then select **Continue**.

   > [!NOTE]
   > If you are not migrating virtual machines on layer-2 (L2) extended networks, you can skip this step.

   :::image type="content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot that shows the selection of distributed virtual switches and the Continue button." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Review the connection rules and select **Continue**.

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot that shows the connection rules and the Continue button." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Select **Finish** to create the compute profile.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot that shows compute profile information." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

For an end-to-end overview of this procedure, view the [Azure VMware Solution: Compute Profile](https://www.youtube.com/embed/e02hsChI3b8) video.

## Create a service mesh

>[!IMPORTANT]
>Make sure ports UDP 500/4500 are open between your on-premises VMware HCX Connector 'uplink' network profile addresses and the Azure VMware Solution HCX Cloud 'uplink' network profile addresses.


1. Under **Infrastructure**, select **Interconnect** > **Service Mesh** > **Create Service Mesh**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot of selections to start creating a service mesh." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Review the pre-populated sites, and then select **Continue**.

   > [!NOTE]
   > If this is your first service mesh configuration, you won't need to modify this screen.

1. Select the source and remote compute profiles from the drop-down lists, and then select **Continue**.

   The selections define the resources where VMs can consume VMware HCX services.

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot that shows selecting the source compute profile." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot that shows selecting the remote compute profile." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Review services that will be enabled, and then select **Continue**.

1. In **Advanced Configuration - Override Uplink Network profiles**, select **Continue**.

   Uplink network profiles connect to the network through which the remote site's interconnect appliances can be reached.

1. In **Advanced Configuration - Network Extension Appliance Scale Out**, review and select **Continue**.

   You can have up to eight VLANs per appliance, but you can deploy another appliance to add another eight VLANs. You must also have IP space to account for the more appliances, and it's one IP per appliance.  For more information, see [VMware HCX Configuration Limits](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Screenshot that shows where to increase the VLAN count." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. In **Advanced Configuration - Traffic Engineering**, review and make any modifications that you feel are necessary, and then select **Continue**.

1. Review the topology preview and select **Continue**.

1. Enter a user-friendly name for this service mesh and select **Finish** to complete.

1. Select **View Tasks** to monitor the deployment.

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot that shows the button for viewing tasks.":::

   When the service mesh deployment finishes successfully, you'll see the services as green.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot that shows green indicators on services." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verify the service mesh's health by checking the appliance status.

1. Select **Interconnect** > **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot that shows selections for checking the status of the appliance." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

   >[!NOTE]
   >After establishing the service mesh, you may notice a new datastore and a new host in your private cloud. This is perfectly normal behavior after establishing a service mesh.
   >
   >:::image type="content" source="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png" alt-text="Screenshot showing the HCX service mesh datastore and host." lightbox="media/tutorial-vmware-hcx/hcx-service-mesh-datastore-host.png":::

For an end-to-end overview of this procedure, view the [Azure VMware Solution: Service Mesh](https://www.youtube.com/embed/COY3oIws108) video.

## Create a network extension

This is an optional step to extend any networks from your on-premises environment to Azure VMware Solution.

1. Under **Services**, select **Network Extension** > **Create a Network Extension**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot that shows selections for starting to create a network extension." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Select each of the networks you want to extend to Azure VMware Solution, and then select **Next**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot that shows the selection of a network.":::

1. Enter the on-premises gateway IP for each of the networks you're extending, and then select **Submit**.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot that shows the entry of a gateway IP address.":::

   It takes a few minutes for the network extension to finish. When it does, you see the status change to **Extension complete**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot that shows the status of Extension complete.":::

For an end-to-end overview of this procedure, view the [Azure VMware Solution: Network Extension](https://www.youtube.com/embed/gYR0nftKui0) video.

## Next steps

If the HCX interconnect tunnel status is **UP** and green, you're ready to migrate and protect Azure VMware Solution VMs using VMware HCX. Azure VMware Solution supports workload migrations (with or without a network extension). You can still migrate workloads in your vSphere environment, along with on-premises creation of networks and deployment of VMs onto those networks. For more information, see the [VMware HCX Documentation](https://docs.vmware.com/en/VMware-HCX/index.html).

