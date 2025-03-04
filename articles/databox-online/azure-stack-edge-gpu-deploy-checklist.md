---
title: Prüfliste für das Sammeln von Informationen vor der Bereitstellung eines Azure Stack Edge Pro-GPU-Geräts | Microsoft-Dokumentation
description: In diesem Artikel werden die Informationen beschrieben, die gesammelt werden können, bevor Sie Ihr Azure Stack Edge Pro- GPU-Gerät bereitstellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/07/2021
ms.author: alkohli
ms.openlocfilehash: 9bb7f552fccf4b6f58def2046b80473a57e6116f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756381"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Bereitstellungsprüfliste für Ihr Azure Stack Edge Pro-GPU-Gerät  

In diesem Artikel werden die Informationen beschrieben, die vor der tatsächlichen Bereitstellung Ihres Azure Stack Edge Pro-Geräts gesammelt werden können. 

Stellen Sie mithilfe der folgenden Prüfliste sicher, dass Ihnen diese Informationen vorliegen, nachdem Sie eine Bestellung für ein Azure Stack Edge Pro-Gerät aufgegeben haben und bevor Sie es erhalten. 

## <a name="deployment-checklist"></a>Bereitstellungsprüfliste 

| Phase                             | Parameter                                                                                                                                                                                                                           | Details                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Geräteverwaltung               | <li>Azure-Abonnement</li><li>Ressourcenanbieter registriert</li><li>Azure-Speicherkonto</li>|<li>Aktiviert für Azure Stack Edge Pro/Data Box Gateway, Zugriff als Besitzer oder Mitwirkender.</li><li>Wechseln Sie im Azure-Portal zu **Home > Abonnements > Ihr-Abonnement > Ressourcenanbieter**. Suchen Sie nach `Microsoft.DataBoxEdge`, und registrieren Sie sich. Wiederholen Sie diesen Vorgang für `Microsoft.Devices`, wenn Sie IoT-Workloads bereitstellen.</li><li>Zugriffsanmeldeinformationen erforderlich</li> |
| Geräteinstallation               | Netzkabel im Paket. <br>Für die USA wird ein SVE 18/3-Kabel für 125 Volt und 15 Ampère mit einem Steckverbinder für NEMA 5-15P und C13 (Eingang zu Ausgang) mitgeliefert. | Weitere Informationen finden Sie in der Liste [unterstützter Netzkabel nach Land/Region](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Mindestens ein 1-GbE-RJ-45-Netzwerkkabel für Port 1  </li><li> Mindestens ein 25/10-GbE-SFP+-Kupferkabel für Port 3, Port 4, Port 5 oder Port 6</li>| Diese Kabel muss der Kunde beschaffen.<br>Eine vollständige Liste der unterstützten Netzwerkkabel, Switches und Transceiver für Gerätenetzwerkkarten von Cavium finden Sie in der [Interoperabilitätsmatrix für die Cavium FastlinQ 41000-Reihe](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).<br>Eine vollständige Liste der unterstützten Kabel und Module für 25 GbE und 10 GbE von Mellanox finden Sie unter [Mit Mellanox Dual Port 25G ConnectX-4-Kanal-Netzwerkadapter kompatible Produkte](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Erstmalige Geräteverbindung      | <li>Laptop, dessen IPv4-Einstellungen geändert werden können. Dieser Laptop stellt über einen Switch oder einen USB-zu-Ethernet-Adapter eine Verbindung mit Port 1 her.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Anmeldung beim Gerät                      | Geräteadministratorkennwort, das aus 8 bis 16 Zeichen besteht und drei der folgenden Zeichentypen enthält: Großbuchstaben, Kleinbuchstaben, numerische Zeichen und Sonderzeichen                                            | Das Standardkennwort lautet *Password1* und läuft nach der ersten Anmeldung ab.                                                     |
| Netzwerkeinstellungen                  | Das Gerät enthält zwei 1-GbE- und vier 25-GbE-Netzwerkports. <li>Port 1 wird nur zum Konfigurieren von Verwaltungseinstellungen verwendet. Mindestens ein Datenport kann angeschlossen und konfiguriert werden. </li><li> Mindestens eine Schnittstelle zum Datennetzwerk (PORT 2 bis PORT 6) muss mit dem Internet verbunden sein und über Azure-Konnektivität verfügen.</li><li> DHCP und statische IPv4-Konfiguration werden unterstützt. | Die statische IPv4-Konfiguration erfordert das Angeben von IP-Adresse, DNS-Server und Standardgateway.   |
| Compute-Netzwerkeinstellungen     | <li>Zwei freie, statische und zusammenhängende IP-Adressen für Kubernetes-Knoten und eine statische IP-Adresse für den IoT Edge-Dienst erforderlich.</li><li>Eine zusätzliche IP-Adresse für jeden zusätzlichen Dienst oder jedes zusätzliche Modul erforderlich, der bzw. das bereitgestellt wird.</li>| Nur die statische IPv4-Konfiguration wird unterstützt.|
| (Optional) Webproxyeinstellungen     | <li>IP-Adresse/FQDN des Webproxyservers, Port: </li><li>Benutzername, Kennwort des Webproxys</li> |  |
| Firewall- und Porteinstellungen        | Wenn Sie eine Firewall verwenden, stellen Sie sicher, dass die [aufgelisteten URL-Muster und -Ports](azure-stack-edge-system-requirements.md#networking-port-requirements) für IP-Adressen von Geräten zulässig sind. |  |
| (Empfohlen) Zeiteinstellungen       | Konfigurieren Sie die Zeitzone sowie den primären und sekundären NTP-Server. | Konfigurieren Sie im lokalen Netzwerk den primären und sekundären NTP-Server.<br>Wenn der lokale Server nicht verfügbar ist, können öffentliche NTP-Server konfiguriert werden.                                                    |
| (Optional) Einstellungen für Updateserver | <li>IP-Adresse des Updateservers im lokalen Netzwerk und Pfad zum WSUS-Server erforderlich. </li> | Standardmäßig wird der öffentliche Windows Update-Server verwendet.|
| Geräteeinstellungen | <li>Vollqualifizierter Domänenname des Geräts </li><li>DNS-Domäne</li> | |
| (Optional) Zertifikate  | Zum Testen von nicht produktiven Workloads verwenden Sie die Option [Zertifikate generieren](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). <br><br> Wenn Sie eigene Zertifikate einschließlich der Signaturkette(n) bereitstellen, [fügen Sie Zertifikate](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) im entsprechenden Format hinzu.| Konfigurieren Sie Zertifikate nur, wenn Sie den Gerätenamen und/oder die DNS-Domäne ändern. |
| Aktivierung  | Aktivierungsschlüssel von Azure Stack Edge Pro-/Data Box Gateway-Ressource erforderlich.    | Nach der Generierung läuft der Schlüssel nach 3 Tagen ab. |

<!--
| (Optional) MAC Address            | If MAC address needs to be on the allowed list, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Nächste Schritte

Vorbereiten der Bereitstellung Ihres [Azure Stack Edge Pro-Geräts](azure-stack-edge-gpu-deploy-prep.md).
