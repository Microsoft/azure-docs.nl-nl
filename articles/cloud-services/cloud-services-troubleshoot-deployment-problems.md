---
title: Problemen met de implementatie van Cloud Services (klassiek) oplossen | Microsoft Docs
description: Er zijn enkele veelvoorkomende problemen die u kunt ondervinden bij het implementeren van een Cloud service naar Azure. In dit artikel vindt u oplossingen voor sommige daarvan.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741074"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Problemen met de implementatie van Azure Cloud Services (klassiek) oplossen

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Wanneer u een Cloud service toepassings pakket implementeert in azure, kunt u informatie over de implementatie verkrijgen via het deel venster **Eigenschappen** in de Azure Portal. U kunt de details in dit deel venster gebruiken om problemen met de Cloud service op te lossen en u kunt deze informatie aan Azure-ondersteuning verstrekken bij het openen van een nieuwe ondersteunings aanvraag.

U kunt het deel venster **Eigenschappen** als volgt vinden:

* Klik in de Azure Portal op de implementatie van uw Cloud service, klik op **alle instellingen** en klik vervolgens op **Eigenschappen**.

> [!NOTE]
> U kunt de inhoud van het deel venster **Eigenschappen** naar het klem bord kopiëren door te klikken op het pictogram in de rechter bovenhoek van het deel venster.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probleem: Ik heb geen toegang tot mijn website, maar mijn implementatie wordt gestart en alle rolinstanties zijn gereed
De URL-koppeling van de website die in de portal wordt weer gegeven, bevat niet de poort. De standaard poort voor websites is 80. Als uw toepassing is geconfigureerd om te worden uitgevoerd in een andere poort, moet u het juiste poort nummer toevoegen aan de URL bij het openen van de website.

1. Klik in de Azure Portal op de implementatie van uw Cloud service.
2. Controleer in het deel venster **Eigenschappen** van de Azure Portal de poorten voor de rolinstanties (onder **invoer eindpunten**).
3. Als de poort niet 80 is, voegt u de juiste poort waarde toe aan de URL wanneer u de toepassing opent. Als u een niet-standaard poort wilt opgeven, typt u de URL, gevolgd door een dubbele punt (:), gevolgd door het poort nummer, zonder spaties.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probleem: mijn rolinstanties worden gerecycled zonder dat ik iets heb gedaan
Service retoucheert automatisch wanneer Azure probleem knooppunten detecteert en daarom rollen instanties verplaatst naar nieuwe knoop punten. Als dit gebeurt, worden uw rolinstanties mogelijk automatisch opnieuw gerecycled. Nagaan of service Retoucheer is:

1. Klik in de Azure Portal op de implementatie van uw Cloud service.
2. Bekijk de informatie in het deel venster **Eigenschappen** van de Azure Portal en bepaal of service retoucheert tijdens de tijd dat u de rollen recycling hebt geobserveerd.

Rollen worden ook ongeveer eenmaal per maand herhaald tijdens de updates van het hostbesturingssysteem en het gast besturingssysteem.  
Zie voor meer informatie de instantie blog post [opnieuw opstarten als gevolg van upgrades van het besturings systeem](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probleem: Ik kan geen VIP-swap uitvoeren en er wordt een fout bericht weer gegeven
Een VIP-swap is niet toegestaan als een implementatie-update wordt uitgevoerd. Implementatie-updates kunnen automatisch worden uitgevoerd wanneer:

* Er is een nieuw gast besturingssysteem beschikbaar en u hebt geconfigureerd voor automatische updates.
* Herstel van de service vindt plaats.

Als u wilt weten of een automatische update verhindert dat u een VIP-wissel uitvoert:

1. Klik in de Azure Portal op de implementatie van uw Cloud service.
2. Bekijk in het deel venster **Eigenschappen** van de Azure Portal de waarde **status**. Als de app **klaar** is, controleert u de **laatste bewerking** om te zien of er onlangs een fout is opgetreden waardoor de VIP niet kan worden gewisseld.
3. Herhaal stap 1 en 2 voor de productie-implementatie.
4. Als een automatische update wordt uitgevoerd, wacht u totdat deze is voltooid voordat u de VIP-swap uitvoert.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probleem: een rolinstantie is een lus tussen start, initialiseren, bezet en gestopt
Dit probleem kan duiden op een probleem met uw programmacode, pakket of configuratiebestand. In dat geval ziet u dat de status elke paar minuten kan worden gewijzigd. de Azure Portal kan bijvoorbeeld worden **gerecycled**, **bezet** of **initialisatie**. Dit geeft aan dat er iets mis is met de toepassing die ervoor zorgt dat de rolinstantie actief blijft.

Voor meer informatie over het oplossen van problemen met dit probleem raadpleegt u het blog bericht [Azure PaaS Compute Diagnostics-gegevens](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) en [veelvoorkomende problemen waardoor rollen kunnen worden gerecycled](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Probleem: mijn toepassing werkt niet meer
1. Klik in de Azure Portal op de rolinstantie.
2. Houd rekening met de volgende voor waarden in het deel venster **Eigenschappen** van de Azure Portal om het probleem op te lossen:
   * Als de rolinstantie onlangs is gestopt (u kunt de waarde van het **aantal afgebroken items** controleren), kan de implementatie worden bijgewerkt. Wacht om te zien of de rolinstantie wordt hervat.
   * Als de rolinstantie **bezet** is, controleert u de toepassings code om te zien of de gebeurtenis [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) wordt verwerkt. Mogelijk moet u een code toevoegen of herstellen waarmee deze gebeurtenis wordt afgehandeld.
   * Bespreek de diagnostische gegevens en scenario's voor probleem oplossing in de blog post [Azure PaaS Compute Diagnostics-gegevens](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Als u de Cloud service opnieuw hebt gerecycled, worden de eigenschappen voor de implementatie opnieuw ingesteld en worden de gegevens voor het oorspronkelijke probleem effectief gewist.
>
>

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleem oplossing](./cloud-services-allocation-failures.md) voor Cloud Services.

Voor informatie over het oplossen van problemen met Cloud service rollen met behulp van Azure PaaS computer diagnostische gegevens raadpleegt u [de blog serie van Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).