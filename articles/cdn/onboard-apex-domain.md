---
title: Een root-of Apex-domein onboarden naar een bestaand Azure CDN-eind punt Azure Portal
description: Meer informatie over het onboarden van een root-of Apex-domein naar een bestaand Azure CDN eind punt met behulp van de Azure Portal.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370129"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Een root-of Apex-domein onboarden naar een bestaand Azure CDN-eind punt

Azure CDN gebruikt CNAME-records om het domein eigendom te valideren voor de onboarding van aangepaste domeinen. CDN geeft niet het frontend-IP-adres weer dat is gekoppeld aan uw CDN-profiel. U kunt uw Apex-domein niet toewijzen aan een IP-adres als uw bedoeling het onboard kan worden Azure CDN.

Het DNS-protocol voor komt dat de toewijzing van CNAME-records op de zone Apex. Als uw domein bijvoorbeeld is, `contoso.com` kunt u CNAME-records maken voor `somelabel.contoso.com` , maar niet voor zichzelf een CNAME maken `contoso.com` . Deze beperking geeft een probleem met de eigen aren van toepassingen die achter Azure CDN toepassingen met taak verdeling hebben. Omdat het gebruik van een CDN-profiel het maken van een CNAME-record vereist, is het niet mogelijk om naar het CDN-profiel vanuit de zone Apex te verwijzen.

Dit probleem kan worden opgelost met behulp van alias records in Azure DNS. In tegens telling tot CNAME-records worden alias records gemaakt op de zone Apex. Eigen aren van toepassingen kunnen deze gebruiken om hun zone Apex-record te laten verwijzen naar een CDN-profiel met open bare eind punten. Toepassings eigenaren verwijzen naar hetzelfde CDN-profiel dat wordt gebruikt voor elk ander domein binnen hun DNS-zone. `contoso.com`En `www.contoso.com` kunnen bijvoorbeeld verwijzen naar hetzelfde CDN-profiel. 

Voor het toewijzen van uw Apex-of hoofd domein aan uw CDN-profiel is CNAME-afvlakking of DNS-Chasing vereist. Een mechanisme waarbij de DNS-provider de CNAME-vermelding recursief verhelpt tot deze een IP-adres heeft gevonden. Deze functionaliteit wordt ondersteund door Azure DNS voor CDN-eind punten. 

> [!NOTE]
> Er zijn ook andere DNS-providers die ondersteuning bieden voor CNAME afvlakking of DNS-Chasing, maar Azure CDN raadt u aan Azure DNS voor de klanten te gebruiken voor het hosten van hun domeinen.

U kunt de Azure Portal gebruiken om een Apex-domein op uw CDN uit te voeren en HTTPS erop in te scha kelen door het te koppelen aan een certificaat voor TLS-beëindiging. Apex-domeinen worden ook wel root-of onwaar-domeinen genoemd.

## <a name="create-an-alias-record-for-zone-apex"></a>Een alias record maken voor de zone Apex

1. Open **Azure DNS** configuratie van het domein dat u onboarding wilt uitvoeren.

2. Selecteer **+ Recordset**.

3. Voer in **recordset toevoegen** de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ------|
    | Naam | Voer in **@** . |
    | Type | Selecteer **een**. |
    | Recordset van alias | Selecteer **Ja**. |
    | Aliastype | Selecteer **Azure-resource**. |
    | Kies een abonnement | Selecteer uw abonnement. |
    | Azure-resource | Selecteer uw CDN-eind punt. |

4. Voer uw waarde in voor **TTL**.

5. Selecteer **OK** om uw wijzigingen in te dienen.

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Alias record voor zone Apex":::

6. Met de bovenstaande stap maakt u een zone Apex-record die verwijst naar uw CDN-resource. Een CNAME- **cdnverify** wordt gebruikt voor het onboarden van het domein in uw CDN-profiel.
    1. Voor beeld: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Onboarding van het aangepaste domein in uw CDN

Nadat u uw aangepaste domein hebt geregistreerd, kunt u dit toevoegen aan uw CDN-eindpunt. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en blader naar het CDN-profiel met het eindpunt dat u wilt toewijzen aan een aangepast domein.
    
2. Op de **CDN-profiel** pagina, selecteert u het CDN-eindpunt dat u wilt koppelen aan het aangepaste domein.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN-eindpunt selecteren" border="true":::
    
3. Selecteer **+ Aangepast domein**. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Knop Aangepast domein toevoegen" border="true":::

4. In **Een aangepast domein toevoegen** is **Hostnaam van het eindpunt** vooraf ingevuld en afgeleid van de URL van uw CDN-eindpunt: **\<endpoint-hostname>** .azureedge.net. De naam kan niet worden gewijzigd.

5. Voer voor **aangepaste hostnaam** het aangepaste basis-of Apex-domein in dat moet worden gebruikt als het bron domein van uw CNAME-record. 
    1. Bijvoorbeeld **contoso.com**. **Gebruik niet de naam van het cdnverify-subdomein**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Aangepast domein toevoegen" border="true":::

6. Selecteer **Toevoegen**.

   Azure controleert of het CNAME-record bestaat dat u voor de domeinnaam hebt ingevoerd. Als de CNAME juist is, wordt uw aangepaste domein gevalideerd. 

   Het kan even duren voordat de nieuwe instellingen van het aangepaste domein zijn doorgegeven aan alle CDN-edge-knooppunten: 
    - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
    - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
    - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven.   

## <a name="enable-https-on-your-custom-domain"></a>HTTPS inschakelen voor uw aangepaste domein

Zie [zelf studie: https configureren op een Azure CDN aangepast domein](cdn-custom-ssl.md) voor meer informatie over het inschakelen van https in uw aangepaste domein voor Azure CDN.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een CDN-eind punt](cdn-create-new-endpoint.md).
