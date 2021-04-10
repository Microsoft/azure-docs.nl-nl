---
title: Beheerde identiteiten in azure HDInsight
description: Hierin wordt een overzicht gegeven van de implementatie van beheerde identiteiten in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944126"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Beheerde identiteiten in azure HDInsight

Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties door Azure worden beheerd. Met beheerde identiteiten hoeft u geen service-principals te registreren in azure AD. Of behoud referenties zoals certificaten.

Beheerde identiteiten worden in azure HDInsight gebruikt om toegang te krijgen tot Azure AD Domain Services of om bestanden te openen in Azure Data Lake Storage Gen2 wanneer dat nodig is.

Er zijn twee soorten beheerde identiteiten: aan de gebruiker toegewezen en het systeem toegewezen. Azure HDInsight ondersteunt alleen door de gebruiker toegewezen beheerde identiteiten. HDInsight biedt geen ondersteuning voor door het systeem toegewezen beheerde identiteiten. Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-resource, die u vervolgens kunt toewijzen aan een of meer Azure-service-exemplaren. Daarentegen wordt een door het systeem toegewezen beheerde identiteit gemaakt in azure AD en vervolgens automatisch direct ingeschakeld op een bepaald exemplaar van de Azure-service. De levens duur van de door het systeem toegewezen beheerde identiteit wordt vervolgens gekoppeld aan de levens duur van het service-exemplaar waarop het is ingeschakeld.

## <a name="hdinsight-managed-identity-implementation"></a>Implementatie van door HDInsight beheerde identiteit

In azure HDInsight kunnen beheerde identiteiten alleen worden gebruikt door de HDInsight-service voor interne onderdelen. Er wordt momenteel geen methode ondersteund voor het genereren van toegangs tokens met behulp van de beheerde identiteiten die zijn geïnstalleerd op HDInsight-cluster knooppunten voor toegang tot externe services. Voor sommige Azure-Services, zoals reken-Vm's, worden beheerde identiteiten geïmplementeerd met een eind punt dat u kunt gebruiken voor het verkrijgen van toegangs tokens. Dit eind punt is momenteel niet beschikbaar in HDInsight-knoop punten.

Als u uw toepassingen moet Boots trappen om geheimen/wacht woorden te vermijden in de analyse taken (bijvoorbeeld SCALA-taken), kunt u uw eigen certificaten distribueren naar de cluster knooppunten met behulp van script acties en dat certificaat vervolgens gebruiken om een toegangs token te verkrijgen (bijvoorbeeld om toegang te krijgen tot Azure-sleutel kluis).

## <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Beheerde identiteiten kunnen worden gemaakt met een van de volgende methoden:

* [Azure-portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

De resterende stappen voor het configureren van de beheerde identiteit zijn afhankelijk van het scenario waarin het wordt gebruikt.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenario's voor beheerde identiteiten in azure HDInsight

Beheerde identiteiten worden in meerdere scenario's gebruikt in azure HDInsight. Raadpleeg de gerelateerde documenten voor gedetailleerde installatie-en configuratie-instructies:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Schijfversleuteling met behulp van door klant beheerde sleutel](disk-encryption.md)

HDInsight zal de certificaten voor de beheerde identiteiten die u voor deze scenario's gebruikt, automatisch vernieuwen. Er is echter een beperking wanneer meerdere verschillende beheerde identiteiten worden gebruikt voor langlopende clusters, het vernieuwen van het certificaat mogelijk niet zoals verwacht wordt uitgevoerd voor alle beheerde identiteiten. Als u van plan bent om langlopende clusters te gebruiken (bijvoorbeeld meer dan 60 dagen), raden we u aan om dezelfde beheerde identiteit te gebruiken voor alle bovenstaande scenario's. 

Als u al een langlopend cluster met meerdere verschillende beheerde identiteiten hebt gemaakt en als een van deze problemen wordt uitgevoerd:
 * In ESP-clusters wordt het mislukken of schalen van Cluster Services gestart en worden andere bewerkingen niet gestart met verificatie fouten.
 * Bij het wijzigen van het LDAPS-certificaat van AAD-DS in ESP-clusters worden de LDAPS-certificaten niet automatisch bijgewerkt en daarom kan LDAP-synchronisatie en-schaal nood voeding worden gestart.
 * MSI-toegang tot ADLS Gen2 kan niet worden gestart.
 * Versleutelings sleutels kunnen niet worden gedraaid in het CMK-scenario.

vervolgens moet u de vereiste rollen en machtigingen voor de bovenstaande scenario's toewijzen aan al deze beheerde identiteiten die worden gebruikt in het cluster. Als u bijvoorbeeld verschillende beheerde identiteiten voor ADLS Gen2-en ESP-clusters hebt gebruikt, moeten beide de rollen Storage BLOB data owner en "HDInsight Domain Services contributor" hebben toegewezen om te voor komen dat deze problemen worden uitgevoerd.

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Wat gebeurt er als ik de beheerde identiteit Verwijder nadat het cluster is gemaakt?

Het cluster wordt uitgevoerd wanneer de beheerde identiteit nodig is. Er is momenteel geen manier om een beheerde identiteit bij te werken of te wijzigen nadat het cluster is gemaakt. Daarom is het raadzaam om ervoor te zorgen dat de beheerde identiteit niet wordt verwijderd tijdens de cluster runtime. U kunt ook het cluster opnieuw maken en een nieuwe beheerde identiteit toewijzen.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)
