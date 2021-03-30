---
title: Azure Information Protection verbinden met Azure Sentinel
description: Informatie over logboek registratie van Azure Information Protection naar Azure Sentinel door de Azure Information Protection Data Connector te configureren.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655983"
---
# <a name="connect-data-from-azure-information-protection"></a>Verbinding maken met gegevens van Azure Information Protection

> [!IMPORTANT]
> De Azure Information Protection gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt logboek registratie gegevens van [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) naar Azure Sentinel streamen door de Azure Information Protection Data Connector te configureren. Azure Information Protection helpt u bij het beheren en beveiligen van uw gevoelige gegevens, ongeacht of deze zijn opgeslagen in de Cloud of on-premises.

Als [centrale rapportage voor Azure Information Protection](/azure/information-protection/reports-aip) al zo is geconfigureerd dat logboek registratie gegevens van deze service worden opgeslagen in dezelfde log Analytics werk ruimte als u momenteel hebt geselecteerd voor Azure Sentinel, kunt u de configuratie van deze gegevens connector overs Laan. De logboek gegevens van Azure Information Protection zijn al beschikbaar voor Azure Sentinel.

Als de logboek gegevens van Azure Information Protection echter naar een andere Log Analytics-werk ruimte gaan dan die die u momenteel hebt geselecteerd voor Azure Sentinel, voert u een van de volgende handelingen uit:

- Wijzig de werk ruimte die is geselecteerd in azure Sentinel.

- Wijzig de werk ruimte voor Azure Information Protection, die u kunt doen door deze gegevens connector te configureren.
    
    Als u de werk ruimte wijzigt, worden nieuwe rapport gegevens voor Azure Information Protection nu opgeslagen in de werk ruimte die u voor Azure Sentinel gebruikt en zijn historische gegevens niet beschikbaar voor Azure Sentinel. Als de vorige werk ruimte is geconfigureerd voor aangepaste query's, waarschuwingen of REST Api's, moeten deze bovendien opnieuw worden geconfigureerd voor de Azure Sentinel-werk ruimte als u deze voor Azure Information Protection wilt gebruiken. Er is geen herconfiguratie vereist voor clients en services die gebruikmaken van Azure Information Protection.

## <a name="prerequisites"></a>Vereisten

- Een van de volgende Azure AD-beheerders rollen voor uw Tenant: 
    - Azure Information Protection beheerder
    - Beveiligingsbeheerder
    - Beheerder voor naleving
    - Beheerder van nalevings gegevens
    - Globale beheerder
    
    > [!NOTE]
    > U kunt de rol Azure Information Protection beheerder niet gebruiken als uw Tenant zich op het [Unified labeling platform](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)bevindt.
    
    Deze beheerders rollen zijn alleen vereist voor het configureren van de Azure Information Protection-connector en zijn niet vereist wanneer Azure Sentinel is verbonden met Azure Information Protection.

- Machtigingen voor het lezen van en schrijven naar de Log Analytics werk ruimte die u gebruikt voor de Azure-Sentinel en Azure Information Protection.

- Azure Information Protection is toegevoegd aan de Azure Portal. Zie [Azure Information Protection toevoegen aan de Azure Portal](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)als u hulp nodig hebt bij deze stap.

## <a name="connect-to-azure-information-protection"></a>Verbinding maken met Azure Information Protection

Gebruik de volgende instructies als u geen Log Analytics werk ruimte voor Azure Information Protection hebt geconfigureerd of als u de werk ruimte wilt wijzigen waarin de logboek gegevens van Azure Information Protection worden opgeslagen.

1. Selecteer in azure Sentinel Azure Information Protection **Data connectors**  >  **(preview)**.

2. Selecteer de **pagina connector openen**.

3. Selecteer onder **configuratie** de optie **verbinding maken Azure Information Protection logboeken**.

4. Selecteer op de Blade **analyse configureren (preview)** de werk ruimte die u momenteel gebruikt voor Azure Sentinel. Als u een andere werk ruimte selecteert, zijn de rapport gegevens van Azure Information Protection niet beschikbaar voor Azure Sentinel.

5. Nadat u een werk ruimte hebt geselecteerd, selecteert u **OK**. De **status** van de connector verandert in **verbonden**.

6. De rapport gegevens van Azure Information Protection worden opgeslagen in de **InformationProtectionLogs_CL** tabel in de geselecteerde werk ruimte. 
    
    Als u het relevante schema in Azure Monitor voor deze rapport gegevens wilt gebruiken, zoekt u naar **InformationProtectionEvents**. Zie de sectie [beschrijvende schema verwijzing voor gebeurtenis functies](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) in de Azure Information Protection-documentatie voor meer informatie over deze gebeurtenis functies.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Information Protection kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).