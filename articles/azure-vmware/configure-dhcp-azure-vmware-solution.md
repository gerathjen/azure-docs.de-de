---
title: Konfigurieren von DHCP für Azure VMware Solution
description: Erfahren Sie, wie Sie DHCP konfigurieren, indem Sie entweder NSX-T Manager zum Hosten eines DHCP-Servers oder einen externen DHCP-Server eines Drittanbieters verwenden.
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 09/13/2021
ms.openlocfilehash: 10234147303ab9959fa1a907b0c558be9e3fe0f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600955"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>Konfigurieren von DHCP für Azure VMware Solution

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

In dieser Schrittanleitung verwenden Sie NSX-T Manager, um DHCP für Azure VMware Solution auf eine der folgenden Arten zu konfigurieren: 


- [Verwenden des Azure-Portals zum Erstellen eines DHCP-Servers oder Relays](#use-the-azure-portal-to-create-a-dhcp-server-or-relay)

- [Verwenden von NSX-T zum Hosten Ihres DHCP-Servers](#use-nsx-t-to-host-your-dhcp-server)

- [Verwenden eines externen DHCP-Servers eines Drittanbieters](#use-a-third-party-external-dhcp-server)

>[!TIP]
>Wenn Sie DHCP unter Verwendung einer vereinfachten Ansicht der NSX-T-Vorgänge konfigurieren möchten, lesen Sie [Konfigurieren von DHCP für Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).


>[!IMPORTANT]
>Für Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, muss die vereinfachte Ansicht der NSX-T-Vorgänge verwendet werden, um DHCP auf dem Standardgateway der Ebene 1 in Ihrer Umgebung zu konfigurieren.
>
>DHCP kann für VMs in einem gestreckten VMware HCX L2-Netzwerk nicht verwendet werden, wenn der DHCP-Server sich in einem lokalen Rechenzentrum befindet.  NSX blockiert standardmäßig den Durchlauf aller DHCP-Anforderungen durch das gestreckte L2-Netzwerk. Die Lösung finden Sie in der Vorgehensweise zum [Konfigurieren von DHCP in gestreckten VMware HCX L2-Netzwerken](configure-l2-stretched-vmware-hcx-networks.md).

## <a name="use-the-azure-portal-to-create-a-dhcp-server-or-relay"></a>Verwenden des Azure-Portals zum Erstellen eines DHCP-Servers oder Relays

DHCP-Server oder -Relays können direkt mit der Azure VMware Solution im Azure-Portal erstellt werden. Der DHCP-Server oder das DHCP-Relay stellt eine Verbindung mit dem Tier-1-Gateway her, das beim Bereitstellen von Azure VMware Solution erstellt wurde. Alle Segmente, in denen Sie DHCP-Bereiche vergeben haben, werden Teil dieses DHCPs. Nachdem Sie einen DHCP-Server oder ein DHCP-Relay erstellt haben, müssen Sie zu dessen Verwendung ein Subnetz oder einen Bereich auf Segmentebene definieren.

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Workloadnetzwerk** die Option **DHCP** > **Hinzufügen** aus.

2. Wählen Sie entweder **DHCP-Server** oder **DHCP-Relay** aus, und geben Sie dann einen Namen für den Server oder das Relay und drei IP-Adressen an. 

   >[!NOTE]
   >Bei einem DHCP-Relay ist für eine erfolgreiche Konfiguration nur eine IP-Adresse erforderlich.

   :::image type="content" source="media/networking/add-dhcp-server-relay.png" alt-text="Screenshot, der das Hinzufügen eines DHCP-Servers oder -Relays in Azure VMware Solution zeigt.":::

4. Vervollständigen Sie die DHCP-Konfiguration, indem Sie [DHCP-Bereiche in den logischen Segmenten bereitstellen](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment), und wählen Sie dann **OK**.



## <a name="use-nsx-t-to-host-your-dhcp-server"></a>Verwenden von NSX-T zum Hosten Ihres DHCP-Servers
Wenn Sie NSX-T zum Hosten Ihres DHCP-Servers verwenden möchten, erstellen Sie einen DHCP-Server und einen Relaydienst. Fügen Sie dann ein Netzwerksegment hinzu, und geben Sie den DHCP-IP-Adressbereich an.

### <a name="create-a-dhcp-server"></a>Erstellen eines DHCP-Servers

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **DHCP** (Netzwerk > DHCP) und dann **Add Server** (Server hinzufügen) aus.

1. Wählen Sie **DHCP** unter **Server Type** (Servertyp) aus, geben Sie den Servernamen und die IP-Adresse an, und wählen Sie **Save** (Speichern) aus.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Screenshot: Hinzufügen eines DHCP-Servers in NSX-T Manager" border="true":::

1. Wählen Sie **Tier-1 Gateways** (Gateways der Ebene 1), dann die vertikalen Auslassungspunkte für das Tier-1 Gateway (Gateway der Ebene 1) und anschließend **Edit** (Bearbeiten) aus.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Screenshot: Bearbeiten des Gateways der Ebene 1 für die Verwendung eines DHCP-Servers" border="true":::

1. Wählen Sie **No IP Allocation Set** (Kein IP-Zuteilungssatz) aus, um ein Subnetz hinzuzufügen.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Screenshot: Hinzufügen eines Subnetzes zum Gateway der Ebene 1 für die Verwendung eines DHCP-Servers" border="true":::

1. Wählen Sie unter **Type** (Typ) die Option **DHCP Local Server** (Lokaler DHCP-Server) aus. 
   
1. Wählen Sie **Default DHCP** (Standard-DHCP) für **DHCP Server** und dann **Save** (Speichern) aus.

1. Wählen Sie erneut **Save** (Speichern) und dann **Close Editing** (Bearbeitung beenden) aus.

### <a name="add-a-network-segment"></a>Hinzufügen eines Netzwerksegments

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>Angeben des DHCP-IP-Adressbereichs
 
Wenn Sie ein Relay zu einem DHCP-Server erstellen, geben Sie auch den DHCP-IP-Adressbereich an.

>[!NOTE]
>Der IP-Adressbereich darf sich nicht mit dem IP-Bereich überlappen, der in anderen virtuellen Netzwerken in Ihrem Abonnement und in Ihren lokalen Netzwerken verwendet wird.

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) aus. 
   
1. Wählen Sie die vertikalen Auslassungspunkte für den Segmentnamen und dann **Edit** (Bearbeiten) aus.
   
1. Wählen Sie **Set Subnets** (Subnetze festlegen) aus, um die DHCP-IP-Adresse für das Subnetz anzugeben. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Screenshot: Festlegen der Subnetze zum Angeben der DHCP-IP-Adresse für die Verwendung eines DHCP-Servers" border="true":::
      
1. Ändern Sie ggf. die Gateway-IP-Adresse, und geben Sie den IP-Adressbereich für DHCP ein. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Screenshot: Gateway-IP-Adresse und DHCP-Bereiche für die Verwendung eines DHCP-Servers" border="true":::
      
1. Wählen Sie **Apply** (Übernehmen) und dann **Save** (Speichern) aus. Das Segment wird einem DHCP-Serverpool zugewiesen.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Screenshot: DHCP-Serverpool ist einem Segment für die Verwendung eines DHCP-Servers zugewiesen" border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>Verwenden eines externen DHCP-Servers eines Drittanbieters

Wenn Sie einen externen DHCP-Server eines Drittanbieters verwenden möchten, erstellen Sie einen DHCP-Relaydienst in NSX-T Manager. Sie geben auch den DHCP-IP-Adressbereich an.


>[!IMPORTANT]
>Für Clouds, die am oder nach dem 1. Juli 2021 erstellt wurden, muss die vereinfachte Ansicht der NSX-T-Vorgänge verwendet werden, um DHCP auf dem Standardgateway der Ebene 1 in Ihrer Umgebung zu konfigurieren.


### <a name="create-dhcp-relay-service"></a>Erstellen eines DHCP-Relaydiensts

Verwenden Sie ein DHCP-Relay für jeden nicht auf NSX basierenden DHCP-Dienst. Beispielsweise eine VM, die DHCP in Azure VMware Solution, Azure IaaS oder lokal ausführt.

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **DHCP** (Netzwerk > DHCP) und dann **Add Server** (Server hinzufügen) aus.

1. Wählen Sie **DHCP Relay** unter **Server Type** (Servertyp) aus, geben Sie den Servernamen und die IP-Adresse an, und wählen Sie **Save** (Speichern) aus.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Screenshot: Erstellen eines DHCP-Relaydiensts in NSX-T Manager" border="true":::

1. Wählen Sie **Tier-1 Gateways** (Gateways der Ebene 1), dann die vertikalen Auslassungspunkte für das Tier-1 Gateway (Gateway der Ebene 1) und anschließend **Edit** (Bearbeiten) aus.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Screenshot: Bearbeiten des Gateways der Ebene 1" border="true":::

1. Wählen Sie **No IP Allocation Set** (Keine IP-Zuteilung festgelegt) aus, um die Zuteilung von IP-Adressen zu definieren.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Screenshot: Hinzufügen eines Subnetzes zum Gateway der Ebene 1" border="true":::

1. Wählen Sie unter **Type** (Typ) die Option **DHCP Server** aus. 
   
1. Wählen Sie **DHCP Relay** für **DHCP Server** und dann **Save** (Speichern) aus.

1. Wählen Sie erneut **Save** (Speichern) und dann **Close Editing** (Bearbeitung beenden) aus.


### <a name="specify-the-dhcp-ip-address-range"></a>Angeben des DHCP-IP-Adressbereichs

Wenn Sie ein Relay zu einem DHCP-Server erstellen, geben Sie auch den DHCP-IP-Adressbereich an.

>[!NOTE]
>Der IP-Adressbereich darf sich nicht mit dem IP-Bereich überlappen, der in anderen virtuellen Netzwerken in Ihrem Abonnement und in Ihren lokalen Netzwerken verwendet wird.

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) aus. 
   
1. Wählen Sie die vertikalen Auslassungspunkte für den Segmentnamen und dann **Edit** (Bearbeiten) aus.
   
1. Wählen Sie **Set Subnets** (Subnetze festlegen) aus, um die DHCP-IP-Adresse für das Subnetz anzugeben. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Screenshot: Festlegen der Subnetze zum Angeben der DHCP-IP-Adresse" border="true":::
      
1. Ändern Sie ggf. die Gateway-IP-Adresse, und geben Sie den IP-Adressbereich für DHCP ein. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Screenshot: Gateway-IP-Adresse und DHCP-Bereiche" border="true":::
      
1. Wählen Sie **Apply** (Übernehmen) und dann **Save** (Speichern) aus. Das Segment wird einem DHCP-Serverpool zugewiesen.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Screenshot: DHCP-Serverpool ist einem Segment zugewiesen" border="true":::


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie DHCP-Anforderungen von Ihren Azure VMware Solution-VMs an einen Nicht-NSX-T-DHCP-Server senden möchten, lesen Sie [Konfigurieren von DHCP in VMware HCX-Netzwerken mit L2-Stretching](configure-l2-stretched-vmware-hcx-networks.md).
