---
title: 'IoT Hub Device Provisioning Service: Rollen und Vorgänge'
description: Dieser Artikel bietet eine konzeptionelle Übersicht über die Rollen und Vorgänge für die Entwicklung einer IoT-Lösung mithilfe des IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: f25aed4fa424ba7b98781f145ace2dce95d27d99
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276149"
---
# <a name="roles-and-operations"></a>Rollen und Vorgänge

Die Entwicklungsphasen bei einer IoT-Lösung können sich über Wochen oder Monate erstrecken, bedingt durch Produktionsfaktoren wie Fertigungszeit, Versand, Zollabwicklung usw. Darüber hinaus können sie Aktivitäten auf mehrere Rollen verteilen, wenn die verschiedenen beteiligten Entitäten berücksichtigt werden. In diesem Thema werden die verschiedenen Rollen und Vorgänge der einzelnen Phasen genauer untersucht, und der Ablauf wird in einem Sequenzdiagramm dargestellt. 

Die Bereitstellung stellt auch Anforderungen an den Gerätehersteller, um den [Bestätigungsmechanismus](concepts-service.md#attestation-mechanism) zu aktivieren. Die Fertigung kann auch unabhängig vom Zeitpunkt der Phasen der automatischen Bereitstellung erfolgen, insbesondere dann, wenn neue Geräte beschafft werden, nachdem die automatische Bereitstellung bereits eingerichtet wurde.

Im Inhaltsverzeichnis auf der linken Seite finden Sie eine Reihe von Schnellstarts, um die automatische Bereitstellung durch praktische Erfahrungen zu verdeutlichen. Um den Lernprozess zu ermöglichen/vereinfachen, wird Software verwendet, die ein physisches Gerät für die Anmeldung und Registrierung simuliert. Einige Schnellstarts erfordern aufgrund ihrer Simulationsnatur, dass Sie Vorgänge für mehrere Rollen ausführen, z.B. auch Vorgänge für nicht vorhandene Rollen.

| Role | Vorgang | BESCHREIBUNG |
|------| --------- | ------------|
| Hersteller | Codieren der Identität und Registrierungs-URL | Basierend auf dem verwendeten Bestätigungsmechanismus ist der Hersteller für die Verschlüsselung der Geräteidentitätsinformationen und der Registrierungs-URL des Device Provisioning-Diensts verantwortlich.<br><br>**Schnellstarts**: Da das Gerät simuliert wird, ist keine Rolle „Hersteller“ vorhanden. Weitere Informationen darüber, wie Sie diese Informationen abrufen können, finden Sie in der Rolle „Entwickler“, die beim Codieren einer Beispielregistrierungsanwendung verwendet wird. |
| | Bereitstellen der Geräteidentität | Als Urheber der Geräteidentitätsinformationen ist der Hersteller dafür verantwortlich, diese an den Operator (oder einen bestimmten Agent) zu übermitteln oder sie über APIs direkt beim Device Provisioning-Dienst zu registrieren.<br><br>**Schnellstarts**: Da das Gerät simuliert wird, ist keine Rolle „Hersteller“ vorhanden. Weitere Informationen darüber, wie Sie die Geräteidentität abrufen, mit der ein simuliertes Gerät in Ihrer Instanz des Device Provisioning-Diensts registriert wird, finden Sie in der Rolle „Operator“. |
| Operator | Konfigurieren der automatischen Bereitstellung | Dieser Vorgang entspricht der ersten Phase der automatischen Bereitstellung.<br><br>**Schnellstarts:** Sie führen die Rolle „Operator“ aus und konfigurieren den Device Provisioning Service und die IoT Hub-Instanzen in Ihrem Azure-Abonnement. |
|  | Registrieren der Geräteidentität | Dieser Vorgang entspricht der zweiten Phase der automatischen Bereitstellung.<br><br>**Schnellstarts:** Sie führen die Rolle „Operator“ aus und registrieren Ihr simuliertes Gerät in Ihrer Instanz des Device Provisioning Service. Die Geräteidentität richtet sich nach der Bestätigungsmethode, die im Schnellstart simuliert wird (TPM oder X.509). Details zur Bestätigung finden Sie in der Rolle „Entwickler“. |
| Device Provisioning-Dienst,<br>IoT Hub | \<all operations\> | Sowohl für eine Produktionsimplementierung mit physischen Geräten als auch für Schnellstarts mit simulierten Geräten werden diese Rollen über die IoT-Dienste ausgeführt, die Sie in Ihrem Azure-Abonnement konfigurieren. Die Rollen/Vorgänge funktionieren genau gleich, da die Bereitstellung von physischen und simulierten Geräten durch die IoT-Dienste auf die gleiche Weise erfolgt. |
| Entwickler | Erstellen/Bereitstellen von Registrierungssoftware | Dieser Vorgang entspricht der dritten Phase der automatischen Bereitstellung. Der Entwickler ist für die Erstellung und Bereitstellung der Registrierungssoftware auf dem Gerät unter Verwendung des entsprechenden SDKs verantwortlich.<br><br>**Schnellstarts:** Die von Ihnen erstellte Beispielregistrierungsanwendung simuliert ein echtes Gerät für Ihre bevorzugte Plattform/Sprache, das auf Ihrer Arbeitsstation ausgeführt wird (anstatt es auf einem physischen Gerät bereitzustellen). Die Registrierungsanwendung führt die gleichen Vorgänge aus wie eine Registrierungsanwendung auf einem physischen Gerät. Sie geben die Bestätigungsmethode (TPM oder X.509-Zertifikat) sowie die Registrierungs-URL und den „ID-Bereich“ Ihrer Instanz des Device Provisioning-Diensts an. Die Geräteidentität wird durch die SDK-Bestätigungslogik zur Laufzeit bestimmt, basierend auf der von Ihnen angegebenen Methode: <ul><li>**TPM-Bestätigung**: Ihre Entwicklungsarbeitsstation führt eine [TPM-Simulatoranwendung](quick-create-simulated-device-tpm.md) aus. Sobald die Ausführung erfolgt, wird eine separate Anwendung verwendet, um den „Endorsement Key“ und die „Registrierungs-ID“ des TPM für die Registrierung der Geräteidentität zu extrahieren. Die SDK-Bestätigungslogik verwendet den Simulator auch während der Registrierung, um ein signiertes SAS-Token zur Authentifizierung und Überprüfung der Registrierung vorzulegen.</li><li>**X.509-Bestätigung**: Sie verwenden ein Tool zum [Generieren eines Zertifikats](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). Nachdem dieses generiert wurde, erstellen Sie die Zertifikatdatei, die für die Verwendung bei der Registrierung erforderlich ist. Die SDK-Bestätigungslogik verwendet das Zertifikat auch während der Registrierung, um es zur Authentifizierung und Überprüfung der Registrierung vorzulegen.</li></ul> |
| Sicherungsmedium | Starten und registrieren | Dieser Vorgang entspricht der dritten Phase der automatischen Bereitstellung, die von der vom Entwickler entwickelten Geräteregistrierungssoftware übernommen wird. Details finden Sie in der Rolle „Entwickler“. Beim ersten Start: <ol><li>Die Anwendung stellt eine Verbindung mit der Instanz des Device Provisioning-Diensts her, und zwar über die bei der Entwicklung angegebene globale URL und den „ID-Bereich“ des Diensts.</li><li>Nach dem Herstellen der Verbindung wird das Gerät gemäß der während der Registrierung angegebenen Bestätigungsmethode und Identität authentifiziert.</li><li>Nachdem die Authentifizierung erfolgt ist, wird das Gerät bei der durch die Bereitstellungsdienstinstanz angegebenen IoT Hub-Instanz registriert.</li><li>Nach erfolgreicher Registrierung werden eine eindeutige Geräte-ID und ein IoT Hub-Endpunkt zur Kommunikation mit dem IoT Hub an die Registrierungsanwendung zurückgegeben.</li><li> Von dort kann das Gerät seinen ursprünglichen [Gerätezwillings](~/articles/iot-hub/iot-hub-devguide-device-twins.md)zustand für die Konfiguration abrufen und mit dem Melden von Telemetriedaten beginnen.</li></ol>**Schnellstarts**: Da das Gerät simuliert wird, wird die Registrierungssoftware auf Ihrer Entwicklungsarbeitsstation ausgeführt.|

Die folgende Abbildung fasst die Rollen und die Reihenfolge der Vorgänge bei der automatischen Gerätebereitstellung zusammen:
<br><br>
[![Automatische Bereitstellungssequenz für ein Gerät](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Optional kann der Hersteller den Vorgang „Geräteidentität registrieren“ auch über APIs des Device Provisioning-Diensts (anstatt über die Rolle „Operator“) ausführen. Eine detaillierte Beschreibung dieser Abläufe und mehr finden Sie im Video [Zero touch device registration with Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (Geräteregistrierung mit Azure IoT ohne jegliche Intervention, in englischer Sprache) ab Markierung 41:00.

## <a name="roles-and-azure-accounts"></a>Rollen und Azure-Konten

Wie jede Rolle einem Azure-Konto zugeordnet wird, ist abhängig vom Szenario, und es gibt eine Reihe von Szenarien, die beteiligt sein können. Die folgenden allgemeinen Muster sollen Ihnen helfen zu verstehen, wie Rollen üblicherweise einem Azure-Konto zugeordnet werden.

#### <a name="chip-manufacturer-provides-security-services"></a>Chiphersteller stellt Sicherheitsdienste bereit

In diesem Szenario kümmert sich der Hersteller um die Sicherheit für Kunden der Stufe 1. Dieses Szenario wird möglicherweise von diesen Kunden der Stufe 1 bevorzugt, da sie keine detaillierten Sicherheitsvorkehrungen treffen müssen. 

Der Hersteller sorgt in Hardwaresicherheitsmodulen (HSMs) für Sicherheitsmaßnahmen. Diese Sicherheitsmaßnahmen können den Hersteller einschließen, der Schlüssel, Zertifikate usw. von potenziellen Kunden abruft, die bereits DPS-Instanzen und Registrierungsgruppen eingerichtet haben. Der Hersteller könnte diese Sicherheitsinformationen auch für seine Kunden generieren.

In diesem Szenario können zwei Azure-Konten beteiligt sein:

- **Konto 1**: Wird möglicherweise zu eine bestimmten Grad von den Rollen „Operator“ und „Entwickler“ gemeinsam genutzt. Dieser Beteiligte hat die Möglichkeit, die HSM-Chips beim Hersteller zu kaufen. Diese Chips werden auf DPS-Instanzen verwiesen, die Konto 1 zugeordnet sind. Bei DPS-Registrierungen kann dieser Beteiligte Geräte an mehrere Kunden der Stufe 2 vermieten, indem er die Geräteregistrierungseinstellungen in DPS neu konfiguriert. Dieser Beteiligte kann auch IoT-Hubs für Back-End-Systeme von Endbenutzern zugewiesen bekommen, zu denen eine Schnittstelle hergestellt werden kann, um auf die Gerätetelemetrie usw. zugreifen zu können. In diesem Fall ist ein zweites Konto ggf. nicht erforderlich.

- **Konto 2**: Endbenutzer, Kunden der Stufe 2, haben möglicherweise eigene IoT-Hubs. Der Konto 1 zugeordnete Beteiligte verweist lediglich gemietete Geräte auf den richtigen Hub in diesem Konto. Diese Konfiguration erfordert die Verknüpfung von DPS- und IoT-Hubs zwischen verschiedenen Azure-Konten, was mithilfe von Azure Resource Manager-Vorlagen möglich ist.

#### <a name="all-in-one-oem"></a>Alles-in-einem-OEM

Der Hersteller kann ein „Alles-in-einem-OEM“ sein, sodass nur ein einziges Herstellerkonto benötigt wird. Der Hersteller kümmert sich von A bis Z um Sicherheit und Bereitstellung.

Der Hersteller kann Kunden, die Geräte kaufen, eine cloudbasierte Anwendung zur Verfügung stellen. Diese Anwendung arbeitet anschließend mit dem vom Hersteller zugewiesenen IoT-Hub zusammen.

Beispiele für dieses Szenario sind Warenautomaten oder Kaffeevollautomaten.




## <a name="next-steps"></a>Nächste Schritte

Möglicherweise ist es hilfreich, diesen Artikel zu Referenzzwecken mit einem Lesezeichen zu versehen, während Sie die entsprechenden Schnellstarts für die automatische Bereitstellung durcharbeiten. 

Beginnen Sie mit einem Schnellstart zum Einrichten der automatischen Bereitstellung, der am besten zu Ihrem Verwaltungstool passt und Sie durch die Phase „Dienstkonfiguration“ führt:

- [Einrichten der automatischen Bereitstellung mit der Azure CLI](quick-setup-auto-provision-cli.md)
- [Einrichten der automatischen Bereitstellung mit dem Azure-Portal](quick-setup-auto-provision.md)
- [Einrichten der automatischen Bereitstellung mit einer Resource Manager-Vorlage](quick-setup-auto-provision-rm.md)

Fahren Sie dann mit einem Schnellstart zur Bereitstellung eines Geräts fort, der für Ihren Gerätebestätigungsmechanismus und das SDK Ihres Device Provisioning-Diensts sowie Ihre Spracheinstellung geeignet ist. In diesem Schnellstart durchlaufen Sie die Phasen „Geräteregistrierung“ und „Geräteregistrierung und -konfiguration“: 

| Bestätigungsmechanismus für Geräte | Schnellstart | 
| ------------------------------- | -------------------- |
| Symmetrischer Schlüssel | [Bereitstellen eines simulierten Geräts mit symmetrischem Schlüssel](quick-create-simulated-device-symm-key.md) |
| X.509-Zertifikat | [ Bereitstellung eines simulierten X.509-Geräts ](quick-create-simulated-device-x509.md) |
| Simuliertes Trusted Platform Module (TPM) | [ Bereitstellung eines simulierten TPM-Geräts ](quick-create-simulated-device-tpm.md)|




