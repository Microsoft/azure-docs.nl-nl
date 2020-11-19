---
title: Azure HDInsight ID Broker (HIB)
description: Meer informatie over Azure HDInsight ID Broker voor het vereenvoudigen van de verificatie voor Apache Hadoop clusters die lid zijn van een domein.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 9a2bda0a526c307ae17d8415f6f24423ddf51b63
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917763"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID Broker (HIB)

In dit artikel wordt beschreven hoe u de functie Azure HDInsight ID Broker kunt instellen en gebruiken. U kunt deze functie gebruiken voor het ophalen van moderne OAuth-authenticatie voor Apache Ambari terwijl multi-factor Authentication wordt afgedwongen zonder dat verouderde wachtwoord-hashes nodig zijn in Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Overzicht

HDInsight ID Broker vereenvoudigt complexe verificatie-instellingen in de volgende scenario's:

* Uw organisatie is afhankelijk van Federatie om gebruikers te verifiëren voor het openen van cloud resources. Voorheen moest u voor het gebruik van HDInsight-Enterprise Security Package clusters de wachtwoord-hash-synchronisatie inschakelen vanuit uw on-premises omgeving tot Azure Active Directory (Azure AD). Deze vereiste kan moeilijk of onwenselijk zijn voor sommige organisaties.
* Uw organisatie wil multi-factor Authentication afdwingen voor toegang tot Apache Ambari en andere cluster bronnen op basis van Internet of HTTP.

HDInsight ID Broker biedt de verificatie-infra structuur waarmee protocol overgang van OAuth (modern) naar Kerberos (verouderd) kan worden ingeschakeld zonder dat wacht woord-hashes moeten worden gesynchroniseerd met Azure AD DS. Deze infra structuur bestaat uit onderdelen die worden uitgevoerd op een virtuele Windows Server-machine (VM) waarbij het knoop punt HDInsight ID Broker is ingeschakeld, samen met cluster gateway-knoop punten.

Gebruik de volgende tabel om de beste verificatie optie te bepalen op basis van de behoeften van uw organisatie.

|Verificatieopties |HDInsight-configuratie | Factoren om rekening mee te houden |
|---|---|---|
| Volledig OAuth | Enterprise Security Package + HDInsight-ID Broker | Veiligste optie. (Multi-factor Authentication wordt ondersteund.) Hash-synchronisatie is *niet* vereist. Geen SSH/kinit/keytab-toegang voor on-premises accounts, waarvoor geen wacht woord-hash in azure AD DS is. Cloud-only-accounts kunnen nog wel SSH/kinit/keytab. Webgebaseerde toegang tot Ambari via OAuth. Vereist het bijwerken van verouderde apps (bijvoorbeeld JDBC/ODBC) voor de ondersteuning van OAuth.|
| OAuth + Basic auth | Enterprise Security Package + HDInsight-ID Broker | Webgebaseerde toegang tot Ambari via OAuth. Verouderde apps blijven gebruikmaken van basis verificatie. Multi-factor Authentication moet worden uitgeschakeld voor toegang tot basis verificatie. Hash-synchronisatie is *niet* vereist. Geen SSH/kinit/keytab-toegang voor on-premises accounts, waarvoor geen wacht woord-hash in azure AD DS is. Cloud-only-accounts kunnen nog steeds SSH-kinit. |
| Volledige basis verificatie | Enterprise Security Package | Vergelijkbaar met on-premises instellingen. Wacht woord-hash synchroniseren met Azure AD DS is vereist. On-premises accounts kunnen SSH-kinit of keytab gebruiken. Multi-factor Authentication moet worden uitgeschakeld als de back-upopslag Azure Data Lake Storage Gen2 is. |

Het volgende diagram toont de moderne op OAuth gebaseerde verificatie stroom voor alle gebruikers, waaronder federatieve gebruikers, nadat HDInsight-ID Broker is ingeschakeld:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagram waarin de verificatie stroom met HDInsight ID Broker wordt weer gegeven.":::

In dit diagram moet de-client (dat wil zeggen, een browser of app) eerst het OAuth-Token ophalen. Vervolgens wordt het token in een HTTP-aanvraag aan de gateway gepresenteerd. Als u zich al hebt aangemeld bij andere Azure-Services, zoals de Azure Portal, kunt u zich aanmelden bij uw HDInsight-cluster met eenmalige aanmelding.

Er zijn nog steeds veel oudere toepassingen die alleen basis verificatie ondersteunen (dat wil zeggen, gebruikers naam en wacht woord). Voor deze scenario's kunt u HTTP-basis verificatie blijven gebruiken om verbinding te maken met de cluster gateways. In deze installatie moet u ervoor zorgen dat de netwerk verbinding van de gateway knooppunten naar het Active Directory Federation Services-eind punt (AD FS) wordt voor een directe regel van het gezichts vermogen van Gateway knooppunten.

In het volgende diagram ziet u de basis verificatie stroom voor federatieve gebruikers. Eerst probeert de gateway de verificatie te volt ooien met behulp van de [ROPC-stroom](../../active-directory/develop/v2-oauth-ropc.md). Als er geen wacht woord-hashes zijn gesynchroniseerd met Azure AD, valt deze weer op het detecteren van het AD FS-eind punt en wordt de verificatie voltooid door toegang te krijgen tot het AD FS-eind punt.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagram waarin de architectuur met basis verificatie wordt weer gegeven.":::


## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker inschakelen

Een Enterprise Security Package cluster maken waarbij HDInsight ID Broker is ingeschakeld:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Volg de basis stappen voor het maken van een Enterprise Security Package cluster. Zie [een HDInsight-cluster maken met Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)voor meer informatie.
1. Selecteer **HDINSIGHT id Broker inschakelen**.

Met de functie HDInsight ID Broker wordt één extra virtuele machine aan het cluster toegevoegd. Deze VM is het knoop punt HDInsight ID Broker en bevat Server onderdelen ter ondersteuning van verificatie. Het knoop punt HDInsight ID Broker is een domein dat is gekoppeld aan het Azure AD DS-domein.

![Diagram waarin de optie wordt weer gegeven voor het inschakelen van HDInsight ID Broker.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

Als u een nieuwe rol met de naam `idbrokernode` met de volgende kenmerken aan het reken Profiel van uw sjabloon toevoegt, wordt het cluster gemaakt met het knoop punt HDInsight-id Broker ingeschakeld:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Als u een volledig voor beeld van een ARM-sjabloon wilt weer geven, raadpleegt u de sjabloon die u [hier](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)hebt gepubliceerd.


## <a name="tool-integration"></a>Integratie van hulp programma

HDInsight-hulpprogram ma's worden bijgewerkt naar systeem eigen ondersteuning voor OAuth. Gebruik deze hulpprogram ma's voor moderne, op OAuth gebaseerde toegang tot de clusters. De HDInsight [IntelliJ-invoeg toepassing](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) kan worden gebruikt voor toepassingen op basis van Java, zoals scala. [Spark-en Hive-Hulpprogram ma's voor Visual Studio code](../hdinsight-for-vscode.md) kunnen worden gebruikt voor PySpark en Hive-taken. De hulpprogram ma's ondersteunen zowel batch-als interactieve taken.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-toegang zonder wacht woord-hash in azure AD DS

|SSH-opties |Factoren om rekening mee te houden |
|---|---|
| Lokaal VM-account (bijvoorbeeld sshuser) | U hebt dit account bij de aanmaak tijd van het cluster ingevoerd. Er is geen Kerberos-verificatie voor dit account. |
| Alleen-Cloud account (bijvoorbeeld alice@contoso.onmicrosoft.com ) | De wacht woord-hash is beschikbaar in azure AD DS. Kerberos-verificatie is mogelijk via SSH Kerberos. |
| On-premises account (bijvoorbeeld alice@contoso.com ) | SSH Kerberos-verificatie is alleen mogelijk als er een wacht woord-hash beschikbaar is in azure AD DS. Anders kan deze gebruiker geen SSH-verbinding met het cluster. |

Als u SSH wilt maken naar een virtuele machine die lid is van een domein of als u de opdracht wilt uitvoeren `kinit` , moet u een wacht woord opgeven. Voor SSH Kerberos-authenticatie moet de hash beschikbaar zijn in azure AD DS. Als u SSH alleen wilt gebruiken voor beheer scenario's, kunt u één Cloud account maken en dit gebruiken om te SSHen naar het cluster. Andere on-premises gebruikers kunnen nog steeds gebruikmaken van de hulpprogram ma's Ambari of HDInsight of HTTP Basic auth zonder dat de wacht woord-hash beschikbaar is in azure AD DS.

Als uw organisatie geen wacht woord-hashes synchroniseert met Azure AD DS, kunt u als best practice één Cloud gebruiker maken in azure AD. Wijs deze vervolgens als cluster beheerder toe wanneer u het cluster maakt en gebruik dat voor beheer doeleinden. U kunt deze gebruiken om toegang te krijgen tot de virtuele machines via SSH.

Zie [deze hand leiding](./domain-joined-authentication-issues.md)voor informatie over het oplossen van problemen met verificatie.

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Clients die gebruikmaken van OAuth om verbinding te maken met een HDInsight-gateway met HDInsight ID Broker

In het installatie programma voor de HDInsight-ID-Broker kunnen aangepaste apps en clients die verbinding maken met de gateway, worden bijgewerkt om eerst het vereiste OAuth-token te verkrijgen. Volg de stappen in [dit document](../../storage/common/storage-auth-aad-app.md) om het token te verkrijgen met de volgende informatie:

*   OAuth-resource-URI: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Machtiging: (naam: cluster. ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Nadat u het OAuth-token hebt verkregen, gebruikt u het in de autorisatie-header van de HTTP-aanvraag voor de cluster gateway (bijvoorbeeld https:// <clustername> -int.azurehdinsight.net). Een voor beeld van een krul opdracht naar Apache livy API kan er als volgt uitzien:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Voor het gebruik van Beeline en livy kunt u ook de voor beelden-codes volgen die [hier](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) worden weer gegeven om de client te configureren voor het gebruik van OAuth en verbinding maken met het cluster.

## <a name="faq"></a>Veelgestelde vragen
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Welke app is gemaakt door HDInsight in AAD?
Voor elk cluster wordt een toepassing van derden geregistreerd in AAD met de URI van het cluster als identifierUri (zoals https://clustername.azurehdinsight.net ).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Waarom wordt gebruikers gevraagd om toestemming voordat ze HIB ingeschakelde clusters gebruiken?
In AAD is toestemming vereist voor alle toepassingen van derden voordat deze gebruikers kan verifiëren of toegang kan krijgen tot gegevens.

### <a name="can-the-consent-be-approved-programatically"></a>Kan de toestemming worden goedgekeurd via een programma?
Met Microsoft Graph-API kunt u de toestemming automatiseren, raadpleegt u de [API-documentatie](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0) voor het automatiseren van de toestemming:

* Registreer een app en Ken Application. ReadWrite. alle machtigingen voor de app toe om toegang te krijgen tot Microsoft Graph
* Nadat een cluster is gemaakt, kunt u een query uitvoeren voor de cluster-app op basis van de id-URI
* Toestemming voor de app registreren

Wanneer het cluster wordt verwijderd, verwijdert HDInsight de app en hoeft er geen toestemming te worden opschonen.

 


## <a name="next-steps"></a>Volgende stappen

* [Een HDInsight-cluster met Enterprise Security Package configureren met behulp van Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Cluster-prestaties bewaken](../hdinsight-key-scenarios-to-monitor.md)
