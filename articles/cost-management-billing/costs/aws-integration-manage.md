---
title: AWS-kosten en -gebruik beheren in Azure Cost Management
description: In dit artikel leert u hoe u kostenanalyse en budgetten kunt gebruiken in Cost Management om uw AWS-kosten en -gebruik te beheren.
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188102"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>AWS-kosten en -gebruik beheren in Azure

Nadat u integratie van het kosten en gebruiksrapport voor AWS hebt ingesteld en geconfigureerd voor Azure Cost Management, kunt u de kosten en het gebruik van AWS gaan beheren. In dit artikel leert u hoe u kostenanalyse en budgetten kunt gebruiken in Cost Management om uw AWS-kosten en -gebruik te beheren.

Zie [Integratie van kosten- en gebruiksrapport voor AWS instellen en configureren](aws-integration-set-up-configure.md) als u de integratie nog niet hebt geconfigureerd.

_Voordat u begint_: Als u niet bekend bent met kostenanalyse, raadpleegt u de quickstart [Kosten verkennen en analyseren met kostenanalyse](quick-acm-cost-analysis.md). En als u niet bekend bent met budgetten in Azure, raadpleegt u de zelfstudie [Azure-budgetten maken en beheren](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>AWS-kosten bekijken in Kostenanalyse

AWS-kosten zijn beschikbaar in Kostenanalyse in de volgende bereiken:

- Gekoppelde AWS-accounts in een beheergroep
- Kosten van gekoppelde AWS-accounts
- Kosten van geconsolideerde AWS-accounts

In de volgende secties wordt beschreven hoe u de bereiken gebruikt, zodat u de kosten en gebruiksgegevens voor elk bereik kunt zien.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Gekoppelde AWS-accounts in een beheergroep bekijken

Het weergeven van kosten met behulp van het bereik van een beheergroep is de enige manier om geaggregeerde kosten te zien die afkomstig zijn uit verschillende Azure-abonnementen en gekoppelde AWS-accounts. Het gebruik van een beheergroep biedt u de mogelijkheid om samengevoegde cloudkosten van Azure en AWS te bekijken.

Gebruik in Kostenanalyse de bereikkiezer om de beheergroep met de gekoppelde AWS-accounts te selecteren. Hier ziet u een voorbeeld van een afbeelding in de Azure-portal:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Voorbeeld van de bereikkiezer met gekoppelde accounts onder een beheergroep" :::

Hier volgt een voorbeeld van de kosten van de beheergroep in Kostenanalyse, gegroepeerd op provider (Azure en AWS).

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Voorbeeld van de kosten voor Azure en AWS voor een kwartaal in Kostenanalyse" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Beheergroepen worden momenteel niet ondersteund voor klanten met een Microsoft-klantovereenkomst (MCA). MCA-klanten kunnen de connector maken en hun AWS-gegevens weergeven. MCA-klanten kunnen hun Azure-kosten en AWS-kosten echter niet samen weergeven onder een beheergroep.

### <a name="view-aws-linked-account-costs"></a>Kosten van gekoppelde AWS-accounts weergeven

Als u de kosten van gekoppelde AWS-accounts wilt weergeven, opent u de bereikkiezer en selecteert u het gekoppelde AWS-account. Houd er rekening mee dat gekoppelde accounts zijn gekoppeld aan een beheergroep, zoals gedefinieerd in de AWS-connector.

Hier ziet u een voorbeeld waarin het bereik van gekoppelde AWS-accounts wordt geselecteerd.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Voorbeeld van de bereikkiezer met gekoppelde AWS-accounts" :::

### <a name="view-aws-consolidated-account-costs"></a>Kosten van geconsolideerde AWS-accounts weergeven

Als u de kosten voor geconsolideerde AWS-accounts wilt bekijken, opent u de bereikkiezer en selecteert u het geconsolideerde AWS-account. Hier ziet u een voorbeeld waarin het bereik van een geconsolideerd AWS-account wordt geselecteerd.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Voorbeeld van de bereikkiezer met geconsolideerde accounts" :::

Dit bereik biedt een geaggregeerde weergave van alle gekoppelde AWS-accounts die aan het geconsolideerde AWS-account zijn gekoppeld. Hier ziet u een voorbeeld van de kosten voor een geconsolideerd AWS-account, gegroepeerd op servicenaam.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Voorbeeld van geconsolideerde AWS-kosten in Kostenanalyse" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensies beschikbaar voor filteren en groeperen

In de volgende tabel worden de dimensies beschreven die beschikbaar zijn voor groeperen en filteren in Kostenanalyse.

| Dimensie | Amazon CUR-kop | Bereiken | Opmerkingen |
| --- | --- | --- | --- |
| Beschikbaarheidszone | lineitem/AvailabilityZone | Alle |   |
| Locatie | product/Region | Alles |   |
| Meter |   | Alle |   |
| Metercategorie | lineItem/ProductCode | Alle |   |
| Subcategorie van de meter | lineitem/UsageType | Alle |   |
| Bewerking | lineItem/Operation | Alle |   |
| Resource | lineItem/ResourceId | Alle |   |
| Resourcetype | product/instanceType | Alles | Als product/instanceType null is, wordt lineItem/UsageType gebruikt. |
| ResourceGuid | N.v.t. | Alles | GUID van Azure-meter. |
| Servicenaam | product/ProductName | Alles | Als product/ProductName null is, wordt lineItem/ProductCode gebruikt. |
| Servicelaag |   |   |   |
| Abonnements-id | lineItem/UsageAccountId | Geconsolideerd account en beheergroep |   |
| Abonnementsnaam | N.v.t. | Geconsolideerd account en beheergroep | Accountnamen worden verzameld met behulp van de AWS-API's voor organisaties. |
| Label | resourceTags | Alles | Het voorvoegsel _user:_ wordt verwijderd uit door de gebruiker gedefinieerde labels om cloudtags toe te staan. Het voorvoegsel _aws:_ blijft intact. |
| Factureringsaccount-id | bill/PayerAccountId | Beheergroep |   |
| Naam van factureringsaccount | N.v.t. | Beheergroep | Accountnamen worden verzameld met behulp van de AWS-API's voor organisaties. |
| Provider | N.v.t. | Beheergroep | AWS of Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Budgetten instellen voor AWS-bereiken

Gebruik budgetten om kosten proactief te beheren en aansprakelijkheid in uw organisatie te bevorderen. Budgetten worden ingesteld op de bereiken van geconsolideerde AWS-accounts en gekoppelde AWS-accounts. Hier volgt een voorbeeld van budgetten voor een geconsolideerd AWS-account die worden weergegeven in Cost Management:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Voorbeeld van budgetten voor een geconsolideerd AWS-account" :::

## <a name="aws-data-collection-process"></a>Proces voor verzamelen van AWS-gegevens

Nadat de AWS-connector is ingesteld, worden de processen van het verzamelen en detecteren van gegevens gestart. Het kan enkele uren duren om alle gebruiksgegevens te verzamelen. De duur is afhankelijk van:

- De tijd die nodig is voor het verwerken van de CUR-bestanden in de S3-bucket van AWS.
- De tijd die nodig is voor het maken van de bereiken van geconsolideerde AWS-accounts en gekoppelde AWS-accounts.
- De tijd en frequentie waarmee AWS de bestanden van het kosten- en gebruiksrapport wegschrijft naar de S3-bucket.

## <a name="aws-integration-pricing"></a>Prijzen voor AWS-integratie

Elke AWS-connector krijgt 90 gratis proefdagen.

De catalogusprijs is 1% van de maandelijkse kosten van AWS. Elke maand worden er kosten in rekening gebracht op basis van de gefactureerde kosten van de vorige maand.

Het gebruik van AWS-API's kan extra kosten met zich mee brengen in AWS.

## <a name="aws-integration-limitations"></a>Beperkingen voor AWS-integratie

- Budgetten in Cost Management bieden geen ondersteuning voor beheergroepen met meerdere valuta's. Er wordt geen budgetevaluatie weergegeven voor beheergroepen met meerdere valuta's. Als u een beheergroep met meerdere valuta's selecteert bij het maken van een budget, wordt een foutbericht weergegeven.
- Cloudconnectors bieden geen ondersteuning voor AWS GovCloud (US), AWS Gov of AWS China.
- In Cost Management worden alleen _gebruikskosten_ van AWS weergegeven. Belastingen, ondersteuning, restituties, RI, tegoeden of andere kostensoorten worden nog niet ondersteund.

## <a name="troubleshooting-aws-integration"></a>Problemen met AWS-integratie oplossen

Gebruik de volgende informatie voor het oplossen van problemen om veelvoorkomende problemen op te lossen.

### <a name="no-permission-to-aws-linked-accounts"></a>Geen machtiging voor gekoppelde AWS-accounts

**Foutcode:** _Unauthorized_

Er zijn twee manieren om machtigingen te krijgen voor de toegang tot kosten van gekoppelde AWS-accounts:

- Toegang krijgen tot de beheergroep met de gekoppelde AWS-accounts.
- Vraag iemand u een machtiging te geven voor toegang tot het gekoppelde AWS-account.

De maker van de AWS-connector is standaard de eigenaar van alle objecten die de connector heeft gemaakt, dus ook van het geconsolideerde AWS-account en het gekoppelde AWS-account.

Om de connectorinstellingen te kunnen controleren, hebt u ten minste de rol van Inzender nodig. Een gebruiker met de rol Lezer kan deze instellingen niet verifiëren.

### <a name="collection-failed-with-assumerole"></a>Verzameling mislukt met AssumeRole

**Foutcode:** _FailedToAssumeRole_

Deze fout betekent dat Cost Management de AssumeRole-API van AWS niet kan aanroepen. Dit probleem kan optreden vanwege een probleem met de roldefinitie. Controleer of aan de volgende voorwaarden wordt voldaan:

- De externe id is hetzelfde als die in de roldefinitie en de definitie van de connector.
- Het roltype is ingesteld op **Een ander AWS-account**.
- De optie **MFA vereisen** is uitgeschakeld.
- Het vertrouwde AWS-account in de AWS-rol is _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Verzameling mislukt omdat toegang is geweigerd - CUR-rapportdefinities

**Foutcode:** _AccessDeniedReportDefinitions_

Deze fout betekent dat Cost Management de definities van het kosten- en gebruiksrapport niet kan zien. Deze machtiging wordt gebruikt om te controleren of de CUR is gedefinieerd zoals verwacht door Azure Cost Management. Zie [Een kosten- en gebruiksrapport maken in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Verzameling mislukt omdat toegang is geweigerd - Rapporten vermelden

**Foutcode:** _AccessDeniedListReports_

Deze fout betekent dat Cost Management niet het object in de S3-bucket kan vermelden waarin de CUR zich bevindt. Voor het AWS IAM-beleid is een machtiging vereist voor de bucket en voor de objecten in de bucket. Zie [Een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Verzameling mislukt omdat toegang is geweigerd - Rapport downloaden

**Foutcode:** _AccessDeniedDownloadReport_

Deze fout betekent dat Cost Management geen toegang krijgt tot de CUR-bestanden die zijn opgeslagen in de Amazon S3-bucket en deze dus ook niet kan downloaden. Zorg ervoor dat het AWS JSON-beleid dat is gekoppeld aan de rol lijkt op het voorbeeld dat wordt weergegeven onder aan het gedeelte [Een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Verzameling mislukt omdat het kosten- en gebruiksrapport niet is gevonden

**Foutcode:** _FailedToFindReport_

Deze fout betekent dat Cost Management het kosten- en gebruiksrapport niet kan vinden dat in de connector is gedefinieerd. Zorg ervoor dat het rapport niet is verwijderd en dat het AWS JSON-beleid dat is gekoppeld aan de rol, lijkt op het voorbeeld dat wordt weergegeven onder aan het gedeelte [Een rol en beleid maken in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Kan connector niet maken of controleren vanwege niet-overeenkomende definities van kosten- en gebruiksrapport

**Foutcode:** _ReportIsNotValid_

Deze fout heeft betrekking op de definitie van het kosten- en gebruiksrapport van AWS. We hebben specifieke instellingen voor dit rapport nodig. Zie de vereisten in [Een kosten- en gebruiksrapport maken in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="internal-error-when-creating-connector"></a>Interne fout bij maken van connector

**Foutcode:** _Connector maken: Kan connector &lt;Connectornaam&gt; niet maken. Reden: Interne fout. Controleer of de juiste AWS-eigenschappen zijn opgegeven._

Deze fout kan optreden wanneer uw AWS-connector en-abonnement zich in verschillende beheergroepen bevinden. De AWS-connector en het abonnement moeten zich in dezelfde beheergroep bevinden.

## <a name="next-steps"></a>Volgende stappen

- Zie [Eerste installatie van beheergroepen](../../governance/management-groups/overview.md#initial-setup-of-management-groups) als u uw Azure-omgeving nog niet hebt geconfigureerd met beheergroepen.
