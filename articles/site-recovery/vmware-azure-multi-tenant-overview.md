---
title: Nood herstel met meerdere tenants voor VMware VM met Azure Site Recovery
description: Hierin wordt een overzicht gegeven van Azure Site Recovery ondersteuning voor VMWare-herstel na nood gevallen naar Azure in een multi tenant-omgeving (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145579"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Overzicht van ondersteuning voor meerdere tenants voor VMware-nood herstel naar Azure met CSP

[Azure site Recovery](site-recovery-overview.md) ondersteunt omgevingen met meerdere tenants voor Tenant abonnementen. Het ondersteunt ook multitenancy voor Tenant abonnementen die worden gemaakt en beheerd via het Microsoft Cloud Solution Provider-programma (CSP).

Dit artikel bevat een overzicht van het implementeren en beheren van multi tenant-VMware naar Azure-replicatie.

## <a name="multi-tenant-environments"></a>Omgevingen met meerdere tenants

Er zijn drie belang rijke multi tenant-modellen:

* **Shared Hosting Services provider (HSP)**: de partner is eigenaar van de fysieke infra structuur en maakt gebruik van gedeelde bronnen (vCenter, data centers, fysieke opslag, enzovoort) om meerdere Tenant-vm's te hosten op dezelfde infra structuur. De partner kan herstel na nood gevallen bieden als een beheerde service, of de Tenant kan eigenaar zijn van herstel na nood gevallen als self-service oplossing.

* **Toegewezen hosting services-provider**: de partner is eigenaar van de fysieke infra structuur, maar gebruikt toegewezen resources (meerdere vCenter, fysieke gegevens opslag, enzovoort) om de vm's van elke Tenant op een afzonderlijke infra structuur te hosten. De partner kan beheer voor herstel na nood geval bieden als een beheerde service, of de Tenant kan eigenaar zijn van het bedrijf als self-service oplossing.

* **MSP (Managed Services provider)**: de klant is eigenaar van de fysieke infra structuur die als host fungeert voor de vm's en de partner biedt ondersteuning voor herstel na nood gevallen en beheer.

## <a name="shared-hosting-services-provider-hsp"></a>Shared-Hosting Services provider (HSP)

De andere twee scenario's zijn subsets van het scenario met gedeelde hosting en gebruiken dezelfde principes. De verschillen worden beschreven aan het einde van de richt lijnen voor gedeeld gebruik.

De basis vereiste in een scenario met meerdere tenants is dat tenants moeten worden geïsoleerd. Een Tenant mag niet kunnen nagaan wat een andere Tenant heeft gehost. In een omgeving die door een partner wordt beheerd, is deze vereiste niet zo belang rijk als in een self-service omgeving, waar dit kritiek kan zijn. In dit artikel wordt ervan uitgegaan dat Tenant isolatie is vereist.

De architectuur wordt weer gegeven in het volgende diagram.

![Gedeelde HSP met één vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Shared-hosting met één vCenter-Server**

In het diagram heeft elke klant een afzonderlijke beheer server. Deze configuratie beperkt Tenant toegang tot Tenant-specifieke Vm's en schakelt Tenant isolatie in. VMware VM-replicatie maakt gebruik van de configuratie server om Vm's te detecteren en agents te installeren. Dezelfde principes zijn van toepassing op omgevingen met meerdere tenants, met toevoeging van het beperken van de detectie van VM'S met behulp van vCenter-toegangs beheer.

De vereiste gegevens isolatie houdt in dat alle gevoelige infrastructuur gegevens (zoals toegangs referenties) niet worden vrijgegeven voor tenants. Daarom is het raadzaam dat alle onderdelen van de beheer server blijven gelden onder het exclusieve beheer van de partner. De onderdelen van de beheer server zijn:

* Configuratieserver
* Proces server
* Hoofddoelserver

Een afzonderlijke scale-out proces server staat ook onder het besturings element van de partner.

## <a name="configuration-server-accounts"></a>Configuratie Server accounts

Elke configuratie server in het scenario met meerdere tenants maakt gebruik van twee accounts:

- **vCenter-toegangs account**: dit account wordt gebruikt om Tenant-vm's te detecteren. Hieraan zijn vCenter-toegangs machtigingen toegewezen. Om toegangs lekken te voor komen, raden we u aan deze referenties zelf in het configuratie programma in te voeren.

- **Toegangs account voor de virtuele machine**: dit account wordt gebruikt om de Mobility Service-agent te installeren op Tenant-vm's, met een automatische push. Normaal gesp roken is het een domein account dat een Tenant kan leveren aan een partner of een account dat door de partner rechtstreeks kan worden beheerd. Als een Tenant de gegevens niet rechtstreeks met de partner wil delen, kunnen ze de referenties invoeren via beperkte toegang tot de configuratie server. Of, met hulp van de partner, kunnen ze de Mobility Service-agent hand matig installeren.

## <a name="vcenter-account-requirements"></a>vereisten voor vCenter-account

Configureer de configuratie server met een account waaraan een speciale rol is toegewezen.

- De roltoewijzing moet worden toegepast op het vCenter-toegangs account voor elk vCenter-object en niet worden door gegeven aan de onderliggende objecten. Deze configuratie zorgt voor isolatie van de Tenant, omdat het door geven van toegang kan leiden tot onbedoelde toegang tot andere objecten.

    ![De optie door geven naar onderliggende objecten](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- De alternatieve methode is het toewijzen van het gebruikers account en de rol aan het Data Center-object en het door geven van deze aan de onderliggende objecten. Vervolgens geeft u voor elk object (zoals Vm's die deel uitmaken van andere tenants) het account **geen toegang tot** een bepaalde Tenant. Deze configuratie is omslachtig. Het biedt onbedoelde toegangs controles, omdat voor elk nieuw onderliggend object ook automatisch toegang wordt verleend die wordt overgenomen van de bovenliggende. Daarom wordt u aangeraden de eerste benadering te gebruiken.

### <a name="create-a-vcenter-account"></a>Een vCenter-account maken

1. Maak een nieuwe rol door de vooraf gedefinieerde *alleen-lezen* rol te klonen en geef deze vervolgens een handige naam (zoals Azure_Site_Recovery, zoals wordt weer gegeven in dit voor beeld).
2. Wijs de volgende machtigingen toe aan deze rol:

   * **Data Store**: ruimte toewijzen, bladeren in gegevens opslag, Bestands bewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken
   * **Netwerk**: netwerk toewijzen
   * **Resource**: een virtuele machine toewijzen aan een resource groep, een uitgeschakelde VM migreren, ingeschakelde VM migreren
   * **Taken**: taak maken, taak bijwerken
   * **VM-configuratie**: alle
   * **VM-interactie** > antwoord vraag, apparaat-verbinding, CD-media configureren, diskette media configureren, uitschakelen, inschakelen, VMware-hulpprogram ma's installeren
   * **Vm-inventaris** > maken op basis van bestaande, nieuwe maken, registreren, registratie opheffen
   * **VM-inrichting > het** downloaden van virtuele machines toestaan, het uploaden van bestanden van virtuele machines toestaan
   * **Beheer van virtuele machine-moment opnamen** > moment opnamen verwijderen

       ![Het dialoog venster rol bewerken](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. U kunt als volgt toegangs niveaus toewijzen aan het vCenter-account (dat wordt gebruikt in de Tenant configuratie server) voor verschillende objecten:

>| Object | Rol | Opmerkingen |
>| --- | --- | --- |
>| vCenter | Read-Only | Alleen vereist voor het toestaan van toegang tot vCenter voor het beheren van verschillende objecten. U kunt deze machtiging verwijderen als het account nooit aan een Tenant wordt door gegeven of wordt gebruikt voor beheer bewerkingen in de vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host-en hostcluster | Azure_Site_Recovery | Zorg ervoor dat de toegang zich op het niveau van het object bevindt, zodat alleen toegankelijke hosts Tenant-Vm's hebben vóór failover en na failback. |
>| Data Store-en Data Store-cluster | Azure_Site_Recovery | Hetzelfde als voor gaande. |
>| Netwerk | Azure_Site_Recovery |  |
>| Beheerserver | Azure_Site_Recovery | Biedt toegang tot alle onderdelen (CS, PS en MT) buiten de CS-computer. |
>| Tenant-Vm's | Azure_Site_Recovery | Zorgt ervoor dat nieuwe Tenant-Vm's van een bepaalde Tenant deze toegang krijgen, of ze kunnen niet worden gedetecteerd via de Azure Portal. |

De toegang tot het vCenter-account is nu voltooid. Deze stap voldoet aan de minimale vereiste machtigingen voor het volt ooien van de failback-bewerkingen. U kunt deze toegangs machtigingen ook gebruiken met uw bestaande beleids regels. Wijzig uw bestaande machtigingen die zijn ingesteld om rolmachtigingen van stap 2 toe te voegen, zoals eerder is beschreven.

### <a name="failover-only"></a>Alleen failover
Als u herstel bewerkingen voor nood gevallen wilt beperken tot alleen failover (dat wil zeggen, zonder failback-mogelijkheden), gebruikt u de vorige procedure, met de volgende uitzonde ringen:

- Wijs in plaats van de *Azure_Site_Recovery* rol toe te wijzen aan het vCenter-toegangs account alleen een alleen *-lezen* rol toe aan dat account. Deze machtigingenset staat VM-replicatie en-failover toe en staat geen failback toe.
- Alle andere stappen in het voor gaande proces blijven ongewijzigd. Om Tenant isolatie te garanderen en de detectie van VM'S te beperken, wordt elke machtiging nog steeds toegewezen op het niveau van het object en niet door gegeven aan onderliggende objecten.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Resources implementeren voor het Tenant abonnement

1. Maak een resource groep op de Azure Portal en implementeer vervolgens een Recovery Services kluis volgens het gebruikelijke proces.
2. Download de registratiesleutel voor de kluis.
3. Registreer de CS voor de Tenant met behulp van de kluis registratie sleutel.
4. Voer de referenties in voor de twee toegangs accounts, het account voor toegang tot de vCenter-Server en het account voor toegang tot de virtuele machine.

    ![Beheer configuratie Server accounts](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Servers in de kluis registreren

1. In de Azure Portal, in de kluis die u eerder hebt gemaakt, registreert u de vCenter-Server bij de configuratie server met behulp van het vCenter-account dat u hebt gemaakt.
2. Voltooi het proces voor het voorbereiden van de infra structuur voor Site Recovery volgens het gebruikelijke proces.
3. De virtuele machines zijn nu klaar om te worden gerepliceerd. Controleer of alleen de vm's van de Tenant worden weer gegeven bij **repliceren**  >  **selecteren virtuele machines**.

## <a name="dedicated-hosting-solution"></a>Toegewezen hosting oplossing

Zoals in het volgende diagram wordt weer gegeven, is het verschil tussen de architectuur van een toegewezen hosting oplossing dat de infra structuur van elke Tenant alleen voor die Tenant is ingesteld.

![Diagram waarin het architecturale verschil in een toegewezen hosting oplossing wordt weer gegeven, is dat de infra structuur van elke Tenant alleen voor die Tenant is ingesteld.](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Toegewijd hosting scenario met meerdere vCenter**

## <a name="managed-service-solution"></a>Beheerde service oplossing

Zoals in het volgende diagram wordt weer gegeven, is het verschil in de architectuur van een beheerde service dat de infra structuur van elke Tenant ook fysiek wordt gescheiden van de infra structuur van andere tenants. Dit scenario bestaat meestal wanneer de Tenant eigenaar is van de infra structuur en wil dat een oplossings provider nood herstel beheert.

![architectuur-gedeeld-HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Beheerd service scenario met meerdere vCenter**

## <a name="next-steps"></a>Volgende stappen
- Meer [informatie](site-recovery-role-based-linked-access-control.md) over op rollen gebaseerd toegangs beheer in site Recovery.
- Meer informatie over het [instellen van herstel na nood gevallen van virtuele VMware-machines in azure](vmware-azure-tutorial.md).
- Meer informatie over [multitenancy met CSP voor VMware-vm's](vmware-azure-multi-tenant-csp-disaster-recovery.md).
