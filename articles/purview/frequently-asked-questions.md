---
title: Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure controle sfeer liggen.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: eca0b9986c4da30adeeb02bc3d90d1e3d2892df7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552640"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Veelgestelde vragen over Azure controle sfeer liggen

## <a name="overview"></a>Overzicht

Veel organisaties hebben geen holistische informatie over hun gegevens. Het is lastig te begrijpen welke gegevens er bestaan, waar gegevens zich bevinden en hoe u relevante gegevens kunt vinden en benaderen. Gegevens hebben geen context zoals afkomst, classificatie en uitgebreide meta gegevens, waardoor zakelijke gebruikers moeilijk kunnen zoeken naar de juiste gegevens en deze gegevens op de juiste manier gebruiken. Als gevolg hiervan wordt slechts een klein deel van de verzamelde gegevens gebruikt om zakelijke beslissingen te informeren. Ten slotte is het identificeren van problemen met gegevens beveiliging en het beschermen van gevoelige gegevens inconsistent. Het vergt voortdurend tijd en moeite, met name bij het behoud van de flexibiliteit van gegevens.

Azure controle sfeer liggen is een oplossing voor gegevens beheer. Het helpt klanten om dieper kennis te krijgen van alle gegevens en de controle over het gebruik te houden. Met Azure controle sfeer liggen kunnen organisaties gegevens detecteren en naleven. Ze hebben inzicht in hun gegevens en kunnen de toegang tot gegevens centraal regelen.

## <a name="purpose-of-this-faq"></a>Doel van deze veelgestelde vragen

Deze veelgestelde vragen antwoorden op veelgestelde vragen die klanten en veld teams vaak vragen. Het is bedoeld om vragen te verduidelijken over Azure controle sfeer liggen en gerelateerde oplossingen, zoals Azure Data Catalog (ADC) Gen 2 (afgeschaft) en Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Wat zijn de bron typen die beschikbaar zijn voor het scannen van meta gegevens en classificatie?

|Azure|Niet-Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (beschikbaar op einde van 2020)|
|Azure SQL Managed Instance|SAP ECC (beschikbaar tegen einde van 2020)|
|Azure Data Explorer|SAP S/4 HANA (beschikbaar op einde van 2020)|
|Azure Data Lake Storage Gen1|Hive-meta Store (beschikbaar op einde van 2020)|
|Azure Data Lake Storage Gen2|--|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Welke gegevens systemen/processors kunnen er verbinding mee maken en afkomst ophalen?

|Gegevens systeem/processor 
|---------
|Azure Data Factory: Kopieer activiteit, activiteit van gegevens stroom 
|Aangepaste afkomst   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Wat is de relatie tussen ADC gen 2, Azure Information Protection en Azure controle sfeer liggen?

Azure controle sfeer liggen begon oorspronkelijk als ADC gen 2, maar is sindsdien uitgebreid in het bereik. Nu worden de geavanceerde catalogus mogelijkheden van ADC gen 2 gecombineerd met de gegevens classificatie, labels en handhavings mogelijkheden voor nalevings beleid van Azure Information Protection. Vandaag wordt het nauw keuriger uitgelijnd met de bredere industrie definitie van data governance.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Wat gebeurt er met klanten die ADC gen 1 gebruiken?

Azure controle sfeer liggen is de focus van alle product innovatie in de oplossings ruimte van de catalogus voor micro soft. ADC gen 1 wordt nog steeds ondersteund.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Kunnen klanten meerdere Azure controle sfeer liggen-accounts hebben in hetzelfde abonnement?

Ja, we ondersteunen veel Azure controle sfeer liggen-accounts per abonnement en per Tenant.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Kan ik ADC gen 1 en Azure controle sfeer liggen parallel uitvoeren?

Ja. Beide zijn onafhankelijke services.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Hoe kan ik bestaande ADC gen 1-gegevensassets migreren naar Azure controle sfeer liggen?

Gebruik de Azure controle sfeer liggen-Api's voor het extra heren van ADC gen 1 en de opname in azure controle sfeer liggen. Voor de verklarende woorden lijst bieden we ondersteuning voor bulk hulpprogramma's op basis van CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Hoe kan ik gevoelige gegevens voor SQL-tabellen versleutelen met behulp van Azure controle sfeer liggen?

Gegevens versleuteling wordt uitgevoerd op het niveau van de gegevens bron. In azure controle sfeer liggen worden alleen de meta gegevens opgeslagen. Er worden geen voorbeeld gegevens weer gegeven.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Bestaan er al de mogelijkheden van ADC gen 2 in azure controle sfeer liggen?

Ja.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Wat is het verschil tussen een woorden lijst en classificatie?

In een verklarende woorden lijst wordt gebruikgemaakt van een naamgevings Conventie gevolgd door niet-technische/zakelijke gebruikers van de gegevens, ook wel bekend als gegevens gebruikers. Deze soorten mensen zijn bedrijfs analisten of gegevens wetenschappers die Azure controle sfeer liggen gebruiken om te zoeken naar bepaalde soorten gegevens, op basis van bedrijfs gebruik. Het is bijvoorbeeld mogelijk dat analisten van de supply-chain moeten zoeken naar de voor waarden van de *SKU* en Details van de *verzen ding*. Ze doorzoeken de woorden lijst op deze termen om relevante gegevens te vinden.
Classificatie is een tag die wordt toegepast op een gegevensasset op tabel-, kolom-of bestands niveau, waarmee wordt aangegeven welke gegevens in de Asset voor komt. Classificatie kan automatisch of hand matig worden toegepast op basis van het type gegevens dat is gevonden. Normaal gesp roken gebruikt u classificatie Tags om te bepalen of een activum gevoelige gegevens bevat en wat voor soort gevoelige gegevens mogelijk is.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Scant Azure controle sfeer liggen e-mail berichten, Pdf's enz. in mijn share point en OneDrive?

Scannen voor on-premises share point-sites en-bibliotheken wordt via de Azure Information Protection scanner verschaft. De scanner is beschikbaar voor gebruik door het Microsoft 365-abonnement van een klant met de volgende Sku's: beheerders P1, EMS E3 en M365 E3. Als u een van deze Sku's hebt, moet u de juiste rechten hebben om te beginnen met het gebruik van de Azure Information Protection scanner.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Wat is de reken kracht die wordt gebruikt voor de scan?
Er is een door micro soft beheerde scan infrastructuur. Voor de meeste Azure/AWS-resources die worden ondersteund, hoeft u geen scan infrastructuur te implementeren.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Is er een manier om Azure controle sfeer liggen in te richten via Azure Resource Manager (ARM)-sjabloon/CLI/Power shell?

Ja, ARM-sjabloon is beschikbaar

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Ik gebruik al Atlas. kan ik eenvoudig verplaatsen naar Azure controle sfeer liggen?

Azure controle sfeer liggen is compatibel met de Atlas-API. Als u migreert van Atlas, is het raadzaam om eerst uw gegevens bronnen te scannen met behulp van Azure controle sfeer liggen. Zodra de activa beschikbaar zijn in uw account, kunt u soort gelijke Atlas-Api's gebruiken om te integreren, zoals het bijwerken van assets of het toevoegen van aangepaste afkomst. Azure controle sfeer liggen wijzigt de zoek-API om Azure Search te gebruiken, zodat u de zoek opdracht vooraf kunt gebruiken.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Kan ik meerdere catalogi maken in mijn Tenant?

Ja, u kunt meerdere Azure controle sfeer liggen-accounts per abonnement en per Tenant maken. U kunt de limieten pagina [voor het beheren en verg Roten van quota's voor resources met Azure controle sfeer liggen](how-to-manage-quotas.md)bekijken.

Extra aanbevelingen over wanneer u of mag niet meerdere accounts hebben, worden beschreven in onze [Aanbevolen procedures voor de implementatie van Azure controle sfeer liggen](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Kan ik meerdere tenants registreren binnen één Azure controle sfeer liggen-account?

Nee, momenteel als u de gegevens bron van een andere Tenant wilt scannen, moet u een afzonderlijk Azure controle sfeer liggen-account in die Tenant maken.

### <a name="does-azure-purview-support-column-level-lineage"></a>Ondersteunt Azure controle sfeer liggen afkomst op kolom niveau?

Ja, Azure controle sfeer liggen ondersteunt afkomst op kolom niveau.