---
title: Een root-of Apex-domein onboarden naar een bestaande front-deur-Azure Portal
description: Meer informatie over het voorbereiden van een root-of Apex-domein naar een bestaande front-deur met behulp van de Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646335"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Onboarding van een basis- of hoofddomein op uw Front door
Azure front-deur gebruikt CNAME-records om het domein eigendom te valideren voor de onboarding van aangepaste domeinen. Aan de voor deur wordt niet het frontend-IP-adres weer gegeven dat is gekoppeld aan uw front deur-profiel. U kunt uw Apex-domein dus niet toewijzen aan een IP-adres als uw intentie het niet kan uitvoeren op de voor deur van Azure.

Het DNS-protocol voor komt dat de toewijzing van CNAME-records op de zone Apex. Als uw domein bijvoorbeeld is, `contoso.com` kunt u CNAME-records maken voor `somelabel.contoso.com` , maar niet voor zichzelf een CNAME maken `contoso.com` . Deze beperking geeft een probleem met de eigen aren van toepassingen die toepassingen met taak verdeling achter de Azure front-deur hebben. Omdat voor het gebruik van een front-deur profiel een CNAME-record moet worden gemaakt, is het niet mogelijk om naar het voorste deur Profiel van de zone Apex te wijzen.

Dit probleem kan worden opgelost met behulp van alias records in Azure DNS. In tegens telling tot CNAME-records worden alias records gemaakt op de zone Apex. Eigen aren van toepassingen kunnen deze gebruiken om hun zone Apex-record te laten verwijzen naar een front deur profiel met open bare eind punten. Toepassings eigenaren verwijzen naar hetzelfde deur profiel dat wordt gebruikt voor elk ander domein binnen hun DNS-zone. `contoso.com`En `www.contoso.com` kunnen bijvoorbeeld verwijzen naar hetzelfde deur profiel voor beide. 

Voor het toewijzen van uw Apex-of hoofd domein aan het voorste deur profiel is in principe CNAME-afvlakking of DNS-Chasing vereist. Een mechanisme waarbij de DNS-provider de CNAME-vermelding recursief verhelpt tot deze een IP-adres heeft gevonden. Deze functionaliteit wordt ondersteund door Azure DNS voor eind punten van de voor deur. 

> [!NOTE]
> Er zijn ook andere DNS-providers die ondersteuning bieden voor CNAME afvlakking of DNS-Chasing, maar Azure front-deur raadt u aan om Azure DNS voor de klanten te gebruiken voor het hosten van hun domeinen.

U kunt de Azure Portal gebruiken om een Apex-domein op uw voor deur uit te voeren en HTTPS in te scha kelen door het te koppelen aan een certificaat voor TLS-beëindiging. Apex-domeinen worden ook wel root-of onwaar-domeinen genoemd.

## <a name="create-an-alias-record-for-zone-apex"></a>Een alias record maken voor de zone Apex

1. Open **Azure DNS** configuratie van het domein dat u onboarding wilt uitvoeren.

1. De record voor de zone Apex maken of bewerken.

1. Selecteer het record **type** als *record* en selecteer vervolgens *Ja* voor **alias records instellen**. Het **alias type** moet worden ingesteld op de *Azure-resource*.

1. Selecteer het Azure-abonnement waar uw front-deur profiel wordt gehost. Selecteer vervolgens de voor deur van de resource in de vervolg keuzelijst **Azure-resource** .

1. Selecteer **OK** om uw wijzigingen in te dienen.

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Alias record voor zone Apex":::

1. Met de bovenstaande stap maakt u een zone Apex-record die verwijst naar uw voor deur en ook een CNAME-record met de naam ' afdverify ' (voor beeld `afdverify.contosonews.com` ) aan die wordt gebruikt voor het voorbereiden van het domein op uw front deur-profiel.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Het aangepaste domein onboarden op de voor deur

1. Selecteer op het tabblad front deur Designer op het pictogram ' + ' op het gedeelte frontend-hosts om een nieuw aangepast domein toe te voegen.

1. Voer de root-of Apex-domein naam in het veld aangepaste hostnaam in, bijvoorbeeld `contosonews.com` .

1. Zodra de CNAME-toewijzing van het domein naar uw voor deur is gevalideerd, selecteert u op **toevoegen** om het aangepaste domein toe te voegen.

1. Selecteer **Opslaan** om de wijzigingen te verzenden.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Menu voor aangepaste domeinen":::

## <a name="enable-https-on-your-custom-domain"></a>HTTPS inschakelen voor uw aangepaste domein

1. Selecteer het aangepaste domein dat is toegevoegd en wijzig de **status in de sectie voor het** https-gedeelte van het **aangepaste domein**.

1. Selecteer het  **type certificaat beheer** om *mijn eigen certificaat te gebruiken*.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="HTTPS-instellingen voor aangepast domein":::    

   > [!WARNING]
   > Het beheer type van de front-deur wordt momenteel niet ondersteund voor Apex-of hoofd domeinen. De enige optie die beschikbaar is om HTTPS in te scha kelen op een Apex of hoofd domein voor de voor deur, gebruikt uw eigen aangepaste TLS/SSL-certificaat dat wordt gehost op Azure Key Vault.

1. Zorg ervoor dat u de juiste machtigingen hebt ingesteld voor de voor deur om toegang te krijgen tot uw sleutel kluis, zoals vermeld in de gebruikers interface, voordat u verdergaat met de volgende stap.

1. Kies een **Key Vault-account** in uw huidige abonnement en selecteer vervolgens de juiste **geheime** en **geheime versie** om aan het juiste certificaat toe te wijzen.

1. Selecteer **bijwerken** om de selectie op te slaan en selecteer vervolgens **Opslaan**.

1. Selecteer na een paar minuten **vernieuwen** en selecteer vervolgens het aangepaste domein opnieuw om de voortgang van het inrichten van het certificaat te bekijken. 

> [!WARNING]
> Zorg ervoor dat u de juiste routerings regels hebt gemaakt voor het Apex-domein of het domein aan bestaande routerings regels hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
