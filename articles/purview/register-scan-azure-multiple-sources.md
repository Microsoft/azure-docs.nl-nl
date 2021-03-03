---
title: Meerdere Azure-bronnen scannen
description: Meer informatie over het scannen van een volledige Azure-abonnement of resource groep in uw Azure controle sfeer liggen Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: c57ac9ddbebcf02cb0118705b63f97fd1880b0f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695960"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Azure meerdere bronnen registreren en scannen

In dit artikel wordt beschreven hoe u een Azure multiple source (Azure-abonnementen of-resource groepen) registreert in controle sfeer liggen en hoe u er een scan op uitvoert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure multiple source ondersteunt scans voor het vastleggen van meta gegevens en het schema voor de meeste Azure-resource typen die door controle sfeer liggen worden ondersteund. De gegevens worden ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn
- Verificatie instellen, zoals beschreven in de onderstaande secties

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Verificatie instellen voor het inventariseren van resources onder een abonnement of resource groep

1. Navigeer naar het abonnement of de resource groep in de Azure Portal.  
1. Selecteer **Access Control (IAM)**   in het navigatie menu links 
1. U moet eigenaar of beheerder van de gebruikers toegang zijn om een rol toe te voegen aan het abonnement of de resource groep. Selecteer de knop *+ toevoegen* . 
1. Stel de rol van **lezer** in en voer de naam van het Azure controle sfeer liggen-account in (dat staat voor de bijbehorende MSI) onder invoervak selecteren. Klik op *Opslaan* om de roltoewijzing te volt ooien.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Verificatie instellen voor het scannen van resources onder een abonnement of resource groep

Er zijn twee manieren om verificatie voor Azure meerdere bronnen in te stellen:

- Beheerde identiteit
- Service-principal

> [!NOTE]
> U moet verificatie instellen voor elke resource in uw abonnement of resource groep, die u wilt registreren en scannen. Azure Storage-Resource typen (Azure Blob-opslag en Azure Data Lake Storage Gen2) maken het eenvoudig om het MSI-of Service-Principal toe te voegen op het niveau van het abonnement/de resource groep als gegevens lezer voor Storage blob. De machtigingen worden vervolgens trickle naar elk opslag account in dat abonnement of deze resource groep. Voor alle andere resource typen moet u het MSI-of Service-Principal Toep assen op elke bron, of een script voor het apparaat. U kunt als volgt machtigingen toevoegen voor elk resource type binnen een abonnement of resource groep.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Beheerde instantie Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Een Azure multiple source registreren

Ga als volgt te werk om een nieuwe Azure-bron in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Voor **register bronnen** selecteert u **Azure (meerdere)**
1. Selecteer **Doorgaan**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Azure meerdere bron registreren":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure multiple)** :

1. Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus 
1. Kies eventueel een **beheer groep** om te filteren op
1. **Selecteer een abonnement of een specifieke resource groep** onder een bepaald abonnement in de vervolg keuzelijst. Het registratie bereik wordt ingesteld op het geselecteerde abonnement of de resource groep  
1. Een **verzameling** selecteren of een nieuwe maken (optioneel)
1. **Volt ooien** om de gegevens bron te registreren

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Meerdere bronnen instellen voor Azure":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1. Ga naar de sectie **bronnen**

1. Selecteer de gegevens bron die u hebt geregistreerd

1. Klik op Details weer geven en selecteer **+ nieuwe scan** of gebruik het pictogram voor snelle acties scannen op de bron tegel

1. Vul de *naam* in en selecteer alle typen resources die u in deze bron wilt scannen

    1. U kunt deze *altijd* laten staan (dit omvat toekomstige resource typen die momenteel mogelijk niet bestaan in het abonnement of de resource groep)
    1. U kunt **specifiek resource typen selecteren** die u wilt scannen. Als u deze optie kiest, worden toekomstige resource typen die kunnen worden gemaakt binnen dit abonnement of deze resource groep, niet opgenomen voor scans, tenzij de scan in de toekomst expliciet wordt bewerkt
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure multiple source scan":::

1. Selecteer de referentie om verbinding te maken met de resources in uw gegevens bron. 
    1. U kunt een **referentie op het bovenliggende niveau** als MSI of een bepaalde service principal-type referentie selecteren, die u kunt gebruiken voor alle resource typen onder het abonnement of de resource groep.
    1. U kunt ook specifiek **het bron type selecteren en een andere referentie** voor het bron type Toep assen
    1. Elke referentie wordt beschouwd als de verificatie methode voor alle resources onder een bepaald type
    1. U moet de gekozen referentie instellen op de resources om ze te kunnen scannen zoals beschreven in deze [sectie](#Setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) .
1. U kunt binnen elk type selecteren om alle resources of een subset ervan te scannen op naam.
    1. Als u de optie hebt ingeschakeld **, worden** toekomstige resources van dat type ook gescand in toekomstige scans
    1. Als u specifieke opslag accounts of SQL-data bases selecteert, worden toekomstige resources die zijn gemaakt van dat type binnen dit abonnement of deze resource groep niet opgenomen voor scans, tenzij de scan in de toekomst expliciet wordt bewerkt
 
1.  Klik op **Doorgaan** om door te gaan. U kunt de toegang testen om te controleren of u de controle sfeer liggen MSI hebt toegepast als lezer voor het abonnement of de resource groep. Als er een fout bericht wordt gegenereerd, volgt u de instructies [hier](#Setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Selecteer **regel sets scannen** voor elk resource type dat u in de vorige stap hebt gekozen. U kunt ook inline scan regel sets maken.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Selectie van regelset van meerdere Azure-scans":::

1. Kies de scantrigger. U kunt de app zo plannen dat deze **wekelijks of maandelijks** of **eenmaal** wordt uitgevoerd

1. Controleer de scan en selecteer Opslaan om de configuratie te volt ooien   

## <a name="viewing-your-scans-and-scan-runs"></a>Uw scans en scanuitvoeringen bekijken

1. Bekijk de bron Details door te klikken op **Details weer geven** op de tegel onder het gedeelte bronnen. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Details van meerdere bronnen in azure"::: 

1. Bekijk de details van de scan uitvoering door te navigeren naar de pagina **Scan Details** .
    1. De *status balk* is een korte samen vatting van de uitvoerings status van de onderliggende resources. Deze wordt weer gegeven op het niveau van het abonnement of de resource groep
    1. Groen betekent dat de bewerking is geslaagd. Grijs betekent dat de scan nog steeds wordt uitgevoerd
    1. U kunt in elke scan klikken om meer gedetailleerde details weer te geven

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Details van meerdere scans in azure":::

1. Bekijk een samen vatting van de recente mislukte scan uitvoeringen aan de onderkant van de pagina met bron gegevens. U kunt ook meer gedetailleerde details weer geven die betrekking hebben op deze uitvoeringen.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Uw scans beheren - bewerken, verwijderen of annuleren
Doe het volgende om een scan te beheren of te verwijderen:

- Ga naar het beheercentrum. Selecteer gegevens bronnen onder de sectie bronnen en scannen en selecteer vervolgens op de gewenste gegevens bron

- Selecteer de share die u wilt beheren. U kunt de scan bewerken door bewerken te selecteren

- U kunt de scan verwijderen door verwijderen te selecteren
- Als er een scan wordt uitgevoerd, kunt u deze ook annuleren

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)    
