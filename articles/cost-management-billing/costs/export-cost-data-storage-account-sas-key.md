---
title: Kosten gegevens exporteren met een SAS-sleutel Azure Storage account
description: Dit artikel helpt partners bij het maken van een SAS-sleutel en het configureren van Cost Management-export.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509661"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Kosten gegevens exporteren met een SAS-sleutel Azure Storage account

De volgende informatie is alleen van toepassing op micro soft-partners.

Vaak hebben partners geen eigen Azure-abonnementen in de Tenant die is gekoppeld aan hun eigen micro soft-partner overeenkomst. Partners met een micro soft-partner overeenkomst die globale beheerders zijn van hun facturerings account, kunnen kosten gegevens exporteren en kopiëren naar een opslag account in een andere Tenant met behulp van een SAS-sleutel (Shared Access service). Met andere woorden: met een opslag account met een SAS-sleutel kan de partner een opslag account gebruiken dat zich buiten de partner overeenkomst bevindt voor het ontvangen van geëxporteerde gegevens. Dit artikel helpt partners bij het maken van een SAS-sleutel en het configureren van Cost Management-export.

## <a name="requirements"></a>Vereisten

- U moet een partner zijn met een micro soft-partner overeenkomst en klanten over het Azure-abonnement hebben.
- U moet een globale beheerder zijn voor het facturerings account van uw partner organisatie.
- U moet toegang hebben tot het configureren van een opslag account in een andere Tenant van uw partner organisatie. U bent zelf verantwoordelijk voor het onderhouden van machtigingen en gegevens toegang wanneer uw gegevens exporteren naar uw opslag account.

## <a name="configure-azure-storage-with-a-sas-key"></a>Azure Storage configureren met een SAS-sleutel

Een SAS-token van het opslag account ophalen of er een maken met behulp van de Azure Portal. Voer de volgende stappen uit om de Azure Portal te maken. Zie [beperkte toegang verlenen tot gegevens met Shared Access signatures (SAS)](../../storage/common/storage-sas-overview.md) voor meer informatie over SAS-sleutels.

1. Navigeer naar het opslag account in de Azure Portal.
    - Als uw account toegang heeft tot meerdere tenants, activeert u mappen om toegang te krijgen tot het opslag account. Selecteer uw account in de rechter bovenhoek van de Azure Portal en selecteer vervolgens **mappen wisselen**.
    - Mogelijk moet u zich aanmelden bij de Azure Portal met het bijbehorende Tenant account om toegang te krijgen tot het opslag account.
1. Selecteer in het menu links **gedeelde toegangs handtekening**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Scherm opname met een geconfigureerde Azure Storage-hand tekening voor gedeelde toegang." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Configureer het token met de instellingen die zijn opgegeven in de vorige installatie kopie.
    1. Selecteer **BLOB** voor _toegestane Services_.
    1. Selecteer **service**, **container** en **object** voor _toegestane resource typen_.
    1. Selecteer **lezen**, **schrijven**, **verwijderen**, **lijst**, **toevoegen** en **maken** voor _toegestane machtigingen_.
    1. Kies verval datum en datums. Zorg ervoor dat u het SAS-token voor exporteren bijwerkt voordat het verloopt. Hoe langer het duurt voordat de periode is verlopen, hoe langer de export wordt uitgevoerd voordat er een nieuw SAS-token nodig is.
1. Selecteer **alleen https** voor _toegestane protocollen_.
1. Selecteer **basis** voor _Voorkeurs routerings laag_.
1. Selecteer **key1** voor de _handtekening sleutel_. Als u de sleutel die wordt gebruikt om het SAS-token te ondertekenen, roteert of bijwerkt, moet u een nieuw SAS-token voor uw export opnieuw genereren.
1. Klik op **SAS en verbindingsreeks genereren**.
    De weer gegeven **SAS-token** waarde is het token dat u nodig hebt bij het configureren van export bewerkingen.

## <a name="create-a-new-export-with-a-sas-token"></a>Een nieuwe export met een SAS-token maken

Navigeer naar **exports** op het bereik van het facturerings account en maak een nieuwe export met behulp van de volgende stappen.

1. Selecteer **Maken**.
1. Configureer de export gegevens zoals u dat voor een normale export zou doen. U kunt de export configureren voor het gebruik van een bestaande map of container, of u kunt een nieuwe map of container opgeven en deze voor u maken.
1. Bij het configureren van opslag selecteert **u een SAS-token gebruiken**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Scherm afbeelding van de nieuwe export waar u een SAS-token selecteert." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Voer de naam van het opslag account in en plak het SAS-token.
1. Geef een bestaande container of map op of Identificeer nieuwe mappen die moeten worden gemaakt.
1. Selecteer **Maken**.

Het exporteren op basis van SAS-tokens werkt alleen wanneer het token geldig blijft. Het token opnieuw instellen voordat het huidige verloopt, of uw export bewerking werkt niet meer. Omdat het token toegang biedt tot uw opslag account, moet u het token zo zorgvuldig beveiligen als andere gevoelige informatie. U bent verantwoordelijk voor het onderhouden van machtigingen en gegevens toegang wanneer uw gegevens worden geëxporteerd naar uw opslag account.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Problemen met exporteren oplossen met SAS-tokens

Hieronder volgen enkele veelvoorkomende problemen die zich kunnen voordoen bij het configureren of gebruiken van op SAS-tokens gebaseerde exports.

- U ziet de SAS-sleutel optie niet in de Azure Portal.
  - Controleer of u een partner bent met een micro soft-partner overeenkomst en of u over globale beheerders rechten beschikt voor het facturerings account. Dit zijn de enige personen die met een SAS-sleutel kunnen exporteren.

- U krijgt het volgende fout bericht bij het configureren van uw export:

    **Zorg ervoor dat het SAS-token geldig is voor de BLOB-service, geldig is voor container-en object bron typen en machtigingen heeft: toevoegen lezen schrijven verwijderen. (Fout code van de opslag service: AuthorizationResourceTypeMismatch)**

    - Zorg ervoor dat u de SAS-sleutel correct configureert en genereert in Azure Storage.

- U kunt de volledige SAS-sleutel niet zien nadat u een export hebt gemaakt.
  - De sleutel is waarschijnlijk niet te zien. Nadat de SAS-export is geconfigureerd, wordt de sleutel uit veiligheids overwegingen verborgen.

- U hebt geen toegang tot het opslag account vanuit de Tenant waar de export is geconfigureerd.
  - Het is het verwachte gedrag. Als het opslag account zich in een andere Tenant bevindt, moet u eerst naar die Tenant navigeren in het Azure Portal om het opslag account te vinden.

- Het exporteren is mislukt vanwege een fout met betrekking tot SAS-tokens.
  - Uw export werkt alleen als de SAS-token geldig blijft. Maak een nieuwe sleutel en voer de export uit.

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevens maken en exporteren](tutorial-export-acm-data.md)voor meer informatie over het exporteren van Cost Management gegevens.
- Zie voor meer informatie over het exporteren van grote hoeveel heden gebruiks gegevens [grote gegevens sets ophalen met uitvoer](ingest-azure-usage-at-scale.md).
