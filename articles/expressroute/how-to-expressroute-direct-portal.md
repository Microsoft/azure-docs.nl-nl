---
title: 'Azure ExpressRoute: ExpressRoute direct configureren: Portal'
description: Op deze pagina kunt u ExpressRoute direct configureren via de portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: acbd5c3aa88c2c8c14407ebda0c42d228aa6c9e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018935"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Direct ExpressRoute maken met behulp van de Azure Portal

Dit artikel laat u zien hoe u ExpressRoute direct maakt met behulp van de Azure Portal.
Met ExpressRoute direct kunt u rechtstreeks verbinding maken met het wereld wijde netwerk van micro soft op de locatie van peering strategisch gedistribueerd over de hele wereld. Zie [About ExpressRoute Direct](expressroute-erdirect-about.md) (Over ExpressRoute Direct) voor meer informatie.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Voordat u ExpressRoute direct gebruikt, moet u uw abonnement eerst registreren. Als u zich wilt inschrijven, stuurt u een E-mail naar <ExpressRouteDirect@microsoft.com> met uw abonnements-id, met inbegrip van de volgende gegevens:

* Scenario's die u wilt uitvoeren met **ExpressRoute direct**
* Locatie voorkeuren: Zie [partners en peering-locaties](expressroute-locations-providers.md) voor een volledige lijst met alle locaties
* Tijdlijn voor implementatie
* Eventuele andere vragen

Nadat u bent Inge schreven, controleert u of de resource provider **micro soft. Network** is geregistreerd voor uw abonnement. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider.

1. U hebt toegang tot uw abonnements instellingen zoals beschreven in [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md).
1. In uw abonnement, voor **resource providers**, controleert u of de provider **micro soft. Network** een **geregistreerde** status bevat. Als de resource provider micro soft. Network niet aanwezig is in de lijst met geregistreerde providers, voegt u deze toe.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>ExpressRoute direct maken

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) of op de **Start** pagina de optie **een resource maken**.

1. Op de **nieuwe** pagina, in het **veld _Marketplace _ zoeken_, typt u *_* ExpressRoute direct** en selecteert u vervolgens **Enter** om naar de zoek resultaten te gaan.

1. Selecteer **ExpressRoute direct** in de resultaten.

1. Selecteer op de pagina **ExpressRoute direct** de optie **maken** om de pagina **ExpressRoute direct maken** te openen.

1. Begin met het invullen van de velden op de pagina **basis beginselen** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Pagina Basisinformatie":::

    * **Abonnement**: het Azure-abonnement dat u wilt gebruiken voor het maken van een nieuwe ExpressRoute direct. De ExpressRoute direct resource-en ExpressRoute-circuits moeten zich in hetzelfde abonnement benemen.
    * **Resource groep**: de Azure-resource groep waarin de nieuwe ExpressRoute direct-resource wordt gemaakt. Als u geen bestaande resourcegroep hebt, kunt u een nieuwe maken.
    * **Regio**: De openbare Azure-regio waarin de resource wordt gemaakt.
    * **Naam**: de naam van de nieuwe ExpressRoute direct-resource.

1. Vul vervolgens de velden op de pagina **configuratie** in.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Scherm opname van de pagina ' Create ExpressRoute direct ' op het tabblad ' configuratie ' is geselecteerd.":::

    * **Locatie van peering**: de peering-locatie waar u verbinding maakt met de ExpressRoute direct-resource. Bekijk [ExpressRoute-locaties](expressroute-locations-providers.md)voor meer informatie over peering locaties.
   * **Band breedte**: de band breedte van het poort paar dat u wilt reserveren. ExpressRoute direct ondersteunt de bandbreedte opties van 10 GB en 100 GB. Als uw gewenste band breedte niet beschikbaar is op de opgegeven peering-locatie, [opent u een ondersteunings aanvraag in de Azure Portal](https://aka.ms/azsupt).
   * **Inkapseling**: ExpressRoute direct ondersteunt zowel QinQ-als Dot1Q-inkapseling.
     * Als QinQ is geselecteerd, wordt elk ExpressRoute-circuit dynamisch toegewezen aan een S-tag en is deze uniek in de directe resource van ExpressRoute.
     *  Elk C-tag op het circuit moet uniek zijn op het circuit, maar niet via de ExpressRoute direct.
     * Als Dot1Q-inkapseling is geselecteerd, moet u de uniekheid van de C-tag (VLAN) beheren voor de volledige ExpressRoute direct-resource.
     >[!IMPORTANT]
     >ExpressRoute direct kan slechts één encapsulation-type zijn. Inkapseling kan niet worden gewijzigd nadat ExpressRoute direct is gemaakt.
     >

1. Geef een wille keurige resource label op en selecteer vervolgens **controleren + maken** om de ExpressRoute direct-resource-instellingen te valideren.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Scherm afbeelding waarin de pagina ' ExpressRoute maken ' wordt weer gegeven op het tabblad ' controleren + maken ' is geselecteerd.":::

1. Selecteer **Maken**. Er wordt een bericht weergegeven met de melding dat uw implementatie aan de gang is. De status wordt op deze pagina weergegeven terwijl de resources worden gemaakt. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>De autorisatie brief (LOA) genereren

Het genereren van de autorisatie brief is op dit moment niet beschikbaar in de portal. Gebruik **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** om de autorisatie brief te verkrijgen.

## <a name="change-admin-state-of-links"></a><a name="state"></a>De beheer status van koppelingen wijzigen

Dit proces moet worden gebruikt om een laag 1-test uit te voeren, zodat elke Kruis verbinding op de juiste wijze wordt gerepareerd in elke router voor primair en secundair.

1. Selecteer op de pagina **overzicht** van directe bronnen voor ExpressRoute in het gedeelte **koppelingen** de optie **link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Koppeling 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Schakel de instelling voor **beheer status** in op **ingeschakeld** en selecteer vervolgens **Opslaan**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Beheer status":::

    >[!IMPORTANT]
    >De facturering begint wanneer de status van de beheerder op een van beide koppelingen is ingeschakeld.
    >

1. Herhaal hetzelfde proces voor **link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Een circuit maken

Standaard kunt u 10 circuits maken in het abonnement waarin de ExpressRoute direct-resource zich bevindt. Dit aantal kan worden verhoogd met ondersteuning. U bent zelf verantwoordelijk voor het bijhouden van zowel ingerichte als gebruikte band breedte. Ingerichte band breedte is de som van de band breedte van alle circuits op de ExpressRoute direct-resource. Gebruikte band breedte is het fysieke gebruik van de onderliggende fysieke interfaces.

* Er zijn extra circuit bandbreedten die alleen op ExpressRoute direct kunnen worden gebruikt om de hierboven beschreven scenario's te ondersteunen. Dit zijn: 40 Gbps en 100 Gbps.

* SkuTier kan lokaal, standaard of Premium zijn.

* SkuFamily moet alleen MeteredData zijn. Onbeperkt wordt niet ondersteund op ExpressRoute direct.

Met de volgende stappen kunt u een ExpressRoute-circuit maken op basis van de ExpressRoute direct-werk stroom. In plaats daarvan kunt u ook een circuit maken met behulp van de normale circuit werk stroom, hoewel er geen voor deel is van het gebruik van de standaardwerk stroom stappen voor deze configuratie. Zie [een ExpressRoute-circuit maken en wijzigen](expressroute-howto-circuit-portal-resource-manager.md).

1. Selecteer in de sectie directe **instellingen** van ExpressRoute **circuits** en selecteer vervolgens **+ toevoegen**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Scherm afbeelding toont de instellingen voor ExpressRoute waarvoor circuits zijn geselecteerd en die zijn gemarkeerd." lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Configureer de instellingen op de pagina **configuratie** .

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Configuratie pagina-ExpressRoute direct":::

1. Geef een wille keurige bron code op, selecteer **controleren + maken** om de waarden te valideren voordat u de resource maakt.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Controleren en maken-ExpressRoute direct":::

1. Selecteer **Maken**. Er wordt een bericht weergegeven met de melding dat uw implementatie aan de gang is. De status wordt op deze pagina weergegeven terwijl de resources worden gemaakt. 

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht](expressroute-erdirect-about.md)voor meer informatie over ExpressRoute direct.
