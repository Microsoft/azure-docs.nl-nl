---
title: Just-in-time-toegang aanvragen
description: Hierin wordt beschreven hoe uitgevers van Azure Managed Applications just-in-time toegang aanvragen tot een beheerde toepassing.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: f839eb04e0c036b5059e35fac16d9c702646cbd7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435685"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Just-in-time-toegang inschakelen en aanvragen voor Azure Managed Applications

Consumenten van uw beheerde toepassing kunnen niet alleen permanente toegang tot de beheerde resource groep verlenen. Als uitgever van een beheer toepassing kunt u de voor keur geven aan gebruikers die precies weten wanneer u toegang nodig hebt tot de beheerde bronnen. Azure Managed Applications biedt de mogelijkheid om gebruikers meer controle te geven over het verlenen van toegang tot beheerde resources, een functie die just-in-time (JIT) toegang wordt genoemd. Deze functie is momenteel beschikbaar als preview-product.

Met JIT-toegang kunt u verhoogde toegang tot de resources van een beheerde toepassing aanvragen voor het oplossen van problemen of onderhoud. U hebt altijd alleen-lezen toegang tot de resources, maar gedurende een bepaalde periode kunt u meer toegang hebben.

De werk stroom voor het verlenen van toegang is:

1. U voegt een beheerde toepassing toe aan de Marketplace en geeft aan dat JIT-toegang beschikbaar is.

1. Tijdens de implementatie schakelt de gebruiker JIT-toegang in voor dat exemplaar van de beheerde toepassing.

1. Na de implementatie kan de gebruiker de instellingen voor JIT-toegang wijzigen.

1. U verzendt een aanvraag voor toegang wanneer u de beheerde resources moet oplossen of bijwerken.

1. De gebruiker keurt uw aanvraag goed.

In dit artikel wordt gekeken naar de acties die zijn uitgevoerd voor het inschakelen van JIT-toegang en het verzenden van aanvragen. Zie [just-in-time-toegang goed keuren in azure Managed Applications](approve-just-in-time-access.md)voor meer informatie over het goed keuren van JIT-toegangs aanvragen.

## <a name="add-jit-access-step-to-ui"></a>JIT-toegangs stap toevoegen aan de gebruikers interface

Neem in uw CreateUiDefinition.jsin het bestand een stap op waarmee gebruikers JIT-toegang kunnen inschakelen. Voeg de volgende inhoud toe aan uw CreateUiDefinition.jsin het bestand om de JIT-functionaliteit voor uw aanbieding te ondersteunen.

In ' stappen ':

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

In "uitvoer":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT-toegang is beschikbaar als preview-versie. Het schema voor JIT-configuratie kan worden gewijzigd in toekomstige herhalingen.

## <a name="enable-jit-access"></a>JIT-toegang inschakelen

Zorg er bij het maken van uw aanbieding in het partner centrum voor dat u JIT-toegang inschakelt.

1. Meld u aan bij de portal voor commerciële Marketplace in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

1. Volg de stappen in [een Azure-toepassings aanbieding maken](../../marketplace/create-new-azure-apps-offer.md)voor hulp bij het maken van een nieuwe beheerde toepassing.

1. Schakel op de pagina **technische configuratie** het selectie vakje **just-in-time-toegang inschakelen** in.

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Just-in-time-toegang inschakelen":::

U hebt een JIT-configuratie stap toegevoegd aan uw gebruikers interface en u hebt JIT-toegang ingeschakeld in de commerciële Marketplace-aanbieding. Wanneer gebruikers uw beheerde toepassing implementeren, kunnen ze [JIT-toegang inschakelen voor hun exemplaar](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Toegang aanvragen

Wanneer u toegang nodig hebt tot de beheerde resources van de gebruiker, verzendt u een aanvraag voor een specifieke rol, tijd en duur. De consument moet vervolgens de aanvraag goed keuren.

Een JIT-toegangs aanvraag verzenden:

1. Selecteer **JIT-toegang** voor de beheerde toepassing waartoe u toegang wilt.

1. Selecteer in **aanmerking komende rollen** en selecteer **activeren** in de kolom Actie voor de gewenste rol.

   ![De aanvraag voor toegang activeren](./media/request-just-in-time-access/send-request.png)

1. Selecteer in het formulier **rol activeren** een begin tijd en duur voor de functie die actief moet zijn. Selecteer **activeren** om de aanvraag te verzenden.

   ![Toegang activeren](./media/request-just-in-time-access/activate-access.png) 

1. Bekijk de meldingen om te zien dat de nieuwe JIT-aanvraag is verzonden naar de consumer.

   ![Melding](./media/request-just-in-time-access/in-progress.png)

   Nu moet u wachten tot de consument [uw aanvraag heeft goedgekeurd](approve-just-in-time-access.md#approve-requests).

1. Als u de status van alle JIT-aanvragen voor een beheerde toepassing wilt weer geven, selecteert u **JIT-toegang** en **aanvraag geschiedenis**.

   ![Status weer geven](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Bekende problemen

De principal-ID van het account dat JIT-toegang aanvraagt, moet expliciet worden opgenomen in de definitie van de beheerde toepassing. Het account kan niet alleen worden opgenomen via een groep die is opgegeven in het pakket. Deze beperking wordt in een toekomstige release opgelost.

## <a name="next-steps"></a>Volgende stappen

Zie [just-in-time-toegang goed keuren in azure Managed Applications](approve-just-in-time-access.md)voor meer informatie over het goed keuren van aanvragen voor JIT-toegang.
