---
title: Azure HDInsight-woorden lijst voor cluster fouten maken
description: Meer informatie over het oplossen van fouten die optreden bij het maken van Azure HDInsight-clusters
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 469adf1c0e5108f7a8c89a7c3a4bebba5d42b431
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183961"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: fouten bij het maken van een cluster

In dit artikel worden oplossingen beschreven voor fouten die u kunt tegen komen bij het maken van clusters.

> [!NOTE]
> De eerste drie fouten die in dit artikel worden beschreven, zijn validatie fouten. Ze kunnen zich voordoen wanneer een Azure HDInsight-product gebruikmaakt van de **CsmDocument_2_0** klasse.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Fout code: DeploymentDocument ' CsmDocument_2_0 ' is niet gevalideerd

**Fout**: de locatie van de script actie kan geen toegang krijgen tot \<SCRIPT ACTION URL\> URI:

### <a name="error-message-1"></a>Fout bericht 1

De externe server heeft een fout geretourneerd: (404) niet gevonden. "

#### <a name="cause"></a>Oorzaak

De HDInsight-service heeft geen toegang tot de script actie-URL die u hebt gegeven als onderdeel van de aanvraag Create cluster. De service ontvangt het voor gaande fout bericht wanneer er wordt geprobeerd om de script actie te openen.

#### <a name="resolution"></a>Oplossing

- Voor een HTTP-of HTTPS-URL verifieert u de URL door te proberen ernaar te gaan vanuit een incognito-browser venster.
- Zorg ervoor dat het script bestaat in het opslag account dat u in de aanvraag geeft voor een WASB-URL. Zorg er ook voor dat de opslag sleutel voor dit opslag account juist is.
- Zorg ervoor dat het script in het opslag account bestaat voor een ADLS-URL.

---

### <a name="error-message-2"></a>Fout bericht 2

"De opgegeven script-URI \<SCRIPT_URI\> bevindt zich in ADLS, maar dit cluster heeft geen data Lake Storage-Principal"

#### <a name="cause"></a>Oorzaak

De HDInsight-service heeft geen toegang tot de script actie-URL die u hebt gegeven als onderdeel van de aanvraag Create cluster. De service ontvangt het voor gaande fout bericht wanneer er wordt geprobeerd om de script actie te openen.

#### <a name="resolution"></a>Oplossing

Voeg het bijbehorende Azure Data Lake Storage gen 1-account toe aan het cluster. Voeg ook de service-principal die toegang heeft tot het Data Lake Storage gen 1-account toe aan het cluster.

---

### <a name="error-message-3"></a>Fout bericht 3

De VM-grootte \<CUSTOMER_SPECIFIED_VM_SIZE\> die in de aanvraag is gegeven, is ongeldig of wordt niet ondersteund voor de rol \<ROLE\> . Geldige waarden zijn: \<VALID_VM_SIZE_FOR_ROLE\> . "

#### <a name="cause"></a>Oorzaak

De grootte van de virtuele machine die u hebt opgegeven, is niet toegestaan voor de rol. Deze fout kan optreden omdat de waarde voor de VM-grootte niet werkt zoals verwacht of niet geschikt is voor de computer functie.

#### <a name="resolution"></a>Oplossing

In het fout bericht worden de geldige waarden voor de VM-grootte weer gegeven. Selecteer een van deze waarden en voer de aanvraag cluster maken opnieuw uit.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Fout code: InvalidVirtualNetworkId  

### <a name="error"></a>Fout

De VirtualNetworkId is niet geldig. VirtualNetworkId ' \<USER_VIRTUALNETWORKID\> ' * '

### <a name="cause"></a>Oorzaak

De **VirtualNetworkId** -waarde die u hebt opgegeven tijdens het maken van het cluster heeft niet de juiste indeling.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat de waarden voor **VirtualNetworkId** en subnet de juiste indeling hebben. De **VirtualNetworkId** -waarde ophalen:

1. Ga naar Azure Portal.
1. Selecteer uw virtuele netwerk.
1. Selecteer de menu opdracht **Eigenschappen** . De waarde van de eigenschap **ResourceID** is de waarde van **VirtualNetworkId** .

Hier volgt een voor beeld van een virtuele netwerk-ID:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Fout code: CustomizationFailedErrorCode

### <a name="error"></a>Fout

' De cluster implementatie is mislukt vanwege een fout in de aangepaste script actie. Mislukte acties: \<SCRIPT_NAME\> , ga naar de Ambari-gebruikers interface om de fout op te lossen.

### <a name="cause"></a>Oorzaak

Het aangepaste script dat u hebt gegeven tijdens de aanvraag voor het maken van een cluster wordt uitgevoerd nadat het cluster is geïmplementeerd. Deze fout code geeft aan dat er een fout is opgetreden tijdens het uitvoeren van het aangepaste script met de naam \<SCRIPT_NAME\> .

### <a name="resolution"></a>Oplossing

Omdat het script het aangepaste script is, raden we u aan het probleem op te lossen en het script zo nodig opnieuw uit te voeren. Raadpleeg de logboeken in de map/var/lib/ambari-agent/* om de script fout op te lossen. Of open de pagina **bewerkingen** in de gebruikers interface van Ambari en selecteer vervolgens de bewerking **run_customscriptaction** om de fout gegevens weer te geven.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Fout code: InvalidDocumentErrorCode

### <a name="error"></a>Fout

"De \<META_STORE_TYPE\> versie van het meta Store-schema \<METASTORE_MAJOR_VERSION\> in de Data Base \<DATABASE_NAME\> is incompatibel met de Cluster versie \<CLUSTER_VERSION\> "

### <a name="cause"></a>Oorzaak

De aangepaste meta Store is niet compatibel met de geselecteerde versie van het HDInsight-cluster. Op dit moment ondersteunen HDInsight 4,0-clusters alleen de meta Store-versie 3,0 en hoger, terwijl HDInsight 3,6-clusters geen ondersteuning bieden voor de meta Store-versie 3,0 en hoger.

### <a name="resolution"></a>Oplossing

Alleen meta Store-versies gebruiken die worden ondersteund door uw versie van het HDInsight-cluster. Als u geen aangepaste meta Store opgeeft, maakt HDInsight intern een meta Store en verwijdert deze vervolgens tijdens het verwijderen van het cluster.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Fout code: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Fout

' Kan geen verbinding maken met het eind punt van het cluster beheer om een schaal bewerking uit te voeren. Controleer of de netwerk beveiligings regels geen externe toegang tot het cluster blok keren en of de Cluster beheer-UI (Ambari) kan worden geopend.

### <a name="cause"></a>Oorzaak

Een firewall regel in uw netwerk beveiligings groep (NSG) blokkeert de cluster communicatie met de essentiële Azure-status-en beheer Services.

### <a name="resolution"></a>Oplossing

Als u van plan bent om netwerk beveiligings groepen te gebruiken om netwerk verkeer te beheren, voert u de volgende acties uit voordat u HDInsight installeert:

- Bepaal de Azure-regio die u wilt gebruiken voor HDInsight.
- Identificeer de IP-adressen die zijn vereist voor HDInsight. Zie voor meer informatie [HDInsight management IP addresses](./hdinsight-management-ip-addresses.md) (IP-adressen voor HDInsight-beheer).
  - Maak of wijzig de netwerk beveiligings groepen voor het subnet waarop u HDInsight wilt installeren.
  - Voor netwerk beveiligings groepen staat u inkomend verkeer op poort 443 van de IP-adressen toe. Deze configuratie zorgt ervoor dat HDInsight-beheer Services het cluster kunnen bereiken van buiten het virtuele netwerk.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Fout code: StoragePermissionsBlockedForMsi

### <a name="error"></a>Fout

"De beheerde identiteit heeft geen machtigingen voor het opslag account. Controleer of de rol Storage BLOB data owner is toegewezen aan de beheerde identiteit voor het opslag account. Opslag:/Subscriptions/ \<Subscription ID\> /ResourceGroups/ \< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\> , beheerde identiteit:/Subscriptions/ \<Subscription ID\> /resourceGroups// \< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\> "

### <a name="cause"></a>Oorzaak

U hebt niet de vereiste machtigingen voor het beheren van de identiteit opgegeven. De door de gebruiker toegewezen beheerde identiteit heeft geen Blob Storage rol Inzender op het Azure Data Lake Storage Gen2 Storage-account.

### <a name="resolution"></a>Oplossing

1. Open Azure Portal.
1. Ga naar uw opslagaccount.
1. Kijk onder **Access Control (IAM)**.
1. Zorg ervoor dat de gebruiker de rol Storage BLOB data contributor of de gebruikersrol Storage BLOB-gegevens eigenaar heeft toegewezen.

Zie [machtigingen voor de beheerde identiteit instellen op het data Lake Storage Gen2-account](hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie.

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Fout code: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Fout

"De beveiligings regels in de netwerk beveiligings groep/Subscriptions/ \<SubscriptionID\> /resourceGroups/<naam van de resource groep \> standaard/providers/micro soft. Network/networkSecurityGroups/ \<Network Security Group Name\> geconfigureerd met subnet/Subscriptions/ \<SubscriptionID\> /resourceGroups/ \<Resource Group name\> RG-westeurope-vnet-TomTom-default/providers/micro soft. Network/virtualNetworks/ \<Virtual Network Name\> /subnets/ \<Subnet Name\> staat geen vereiste binnenkomende en/of uitgaande connectiviteit toe. Ga voor meer informatie naar [een virtueel netwerk voor Azure HDInsight plannen](./hdinsight-plan-virtual-network-deployment.md)of neem contact op met de ondersteuning.

### <a name="cause"></a>Oorzaak

Als netwerk beveiligings groepen of door de gebruiker gedefinieerde routes (Udr's) binnenkomend verkeer naar uw HDInsight-cluster beheren, moet u ervoor zorgen dat uw cluster kan communiceren met de essentiële Azure-status-en beheer Services.

### <a name="resolution"></a>Oplossing

Als u van plan bent om netwerk beveiligings groepen te gebruiken om netwerk verkeer te beheren, voert u de volgende acties uit voordat u HDInsight installeert:

- Identificeer de Azure-regio die u wilt gebruiken voor HDInsight en maak een veilige lijst met de IP-adressen voor uw regio. Zie [status-en beheer Services: specifieke regio's](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions)voor meer informatie.
- Identificeer de IP-adressen die HDInsight nodig heeft. Zie [IP-adressen voor HDInsight-beheer](./hdinsight-management-ip-addresses.md)voor meer informatie.
- Maak of wijzig de netwerk beveiligings groepen voor het subnet waarop u HDInsight wilt installeren. Voor netwerk beveiligings groepen staat u inkomend verkeer op poort 443 van de IP-adressen toe. Deze configuratie zorgt ervoor dat HDInsight-beheer Services het cluster kunnen bereiken van buiten het virtuele netwerk.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Fout code: het installeren van de onderdelen op een of meer hosts door het cluster installatie programma is mislukt

### <a name="error"></a>Fout

"Tijdens het installeren van het cluster installatie programma zijn geen onderdelen geïnstalleerd op een of meer hosts. Probeer de aanvraag opnieuw uit te voeren. "

### <a name="cause"></a>Oorzaak 

Deze fout wordt doorgaans gegenereerd wanneer er een tijdelijk probleem is of een Azure-storing.

### <a name="resolution"></a>Oplossing

Controleer de [Azure-status](https://status.azure.com) pagina voor eventuele Azure-uitval die van invloed kunnen zijn op de cluster implementatie. Als er geen storingen zijn, voert u de cluster implementatie opnieuw uit.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Fout code: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Fout

Kan geen verbinding maken met het eind punt van het cluster beheer. Probeer het later opnieuw.

### <a name="cause"></a>Oorzaak

De HDInsight-service kan geen verbinding maken met uw cluster tijdens het maken van het cluster

### <a name="resolution"></a>Oplossing

Als u aangepaste VNet-netwerk beveiligings groep (Nsg's) en door de gebruiker gedefinieerde routes (Udr's) gebruikt, moet u ervoor zorgen dat uw cluster kan communiceren met HDInsight-beheer Services. Zie [IP-adressen voor HDInsight-beheer](./hdinsight-management-ip-addresses.md)voor meer informatie.

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Fout code: implementaties zijn mislukt vanwege beleids overtreding: ' resource ' <Resource URI> ' is niet toegestaan door beleid. Beleids-id's: [{"policyAssignment": {"naam": " <Policy Name> ", "ID": "/providers/Microsoft.Management/managementgroups/. <Management Group Name> providers/micro soft. Authorization/policyAssignments/ <Policy Name> "}, "policyDefinition": <Policy Definition>

### <a name="cause"></a>Oorzaak

Met Azure-beleid op basis van een abonnement kan het maken van open bare IP-adressen niet worden geweigerd. Voor het maken van een HDInsight-cluster zijn twee openbare IP-adressen vereist.

De volgende beleids regels hebben over het algemeen invloed op het maken van het cluster:

* Beleids regels waarmee wordt voor komen dat IP-adressen of load balancers binnen het abonnement worden gemaakt.
* Beleid waarmee wordt voor komen dat opslag accounts worden gemaakt.
* Beleid waarmee wordt voor komen dat netwerk bronnen, zoals IP-adressen of load balancers, worden verwijderd.

### <a name="resolution"></a>Oplossing

De Azure Policy toewijzing op basis van een abonnement verwijderen of uitschakelen tijdens het maken van een HDInsight-cluster.

---

## <a name="next-steps"></a>Volgende stappen

Zie problemen met het maken van [clusters oplossen met Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)voor meer informatie over het oplossen van problemen bij het maken van een cluster.