---
title: Privé koppeling voor Azure API voor FHIR
description: In dit artikel wordt beschreven hoe u een persoonlijk eind punt instelt voor Azure API voor FHIR Services
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: cdb41f12e8f050e3c74fccddb392d7a816c15b2f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621875"
---
# <a name="configure-private-link"></a>Persoonlijke koppeling configureren

Met persoonlijke koppeling kunt u toegang krijgen tot de Azure-API voor FHIR via een persoonlijk eind punt, een netwerk interface waarmee u privé en veilig verbinding maakt met behulp van een privé-IP-adres van uw virtuele netwerk. Met persoonlijke koppeling kunt u onze services veilig vanuit uw Vnet openen als een eerste partij service zonder dat u een open bare DNS hoeft te door lopen. Dit artikel helpt u bij het maken, testen en beheren van uw persoonlijke eind punt voor Azure API voor FHIR.

## <a name="prerequisites"></a>Vereisten

Voordat u een persoonlijk eind punt maakt, moet u eerst een aantal Azure-resources maken:

- Resource groep: de Azure-resource groep die het virtuele netwerk en het persoonlijke eind punt zal bevatten.
- Azure-API voor FHIR: de FHIR-resource die u achter een persoonlijk eind punt wilt plaatsen.
- Virtual Network: het VNet waarmee uw client services en het persoonlijke eind punt worden verbonden.

Raadpleeg de documentatie van de [privé koppeling](../private-link/index.yml)voor meer informatie.

## <a name="disable-public-network-access"></a>Open bare netwerk toegang uitschakelen

Als u een persoonlijk eind punt voor uw FHIR-resource maakt, wordt het open bare verkeer niet automatisch uitgeschakeld. U moet uw FHIR-resource bijwerken om een nieuwe eigenschap ' open bare toegang ' van ' enabled ' in te stellen op ' uitgeschakeld '. Wees voorzichtig met het uitschakelen van open bare netwerk toegang als alle aanvragen van uw FHIR-service die niet afkomstig zijn van een juist geconfigureerd persoonlijk eind punt, worden geweigerd. Alleen verkeer van uw privé-eind punten is toegestaan.

![Open bare netwerk toegang uitschakelen](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Privé-eindpunt maken

Voor het maken van een persoonlijk eind punt kan een ontwikkelaar met RBAC-machtigingen voor de FHIR-resource gebruikmaken van Azure Portal, [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)of [Azure cli](../private-link/create-private-endpoint-cli.md). In dit artikel worden de stappen beschreven voor het gebruik van Azure Portal. Het gebruik van Azure Portal wordt aanbevolen omdat hiermee het maken en configureren van de Privé-DNS zone wordt geautomatiseerd. U kunt verwijzen naar de [persoonlijke koppeling snel starten hand leidingen](../private-link/create-private-endpoint-portal.md) voor meer informatie.

Er zijn twee manieren om een persoonlijk eind punt te maken. Met de automatische goedkeurings stroom kan een gebruiker met RBAC-machtigingen voor de FHIR-resource een persoonlijk eind punt maken zonder dat hiervoor goed keuring nodig is. Met hand matige goedkeurings stroom kan een gebruiker zonder machtigingen voor de FHIR-resource een persoonlijk eind punt aanvragen dat moet worden goedgekeurd door eigen aren van de FHIR-resource.

### <a name="auto-approval"></a>Automatische goed keuring

Zorg ervoor dat de regio voor het nieuwe persoonlijke eind punt hetzelfde is als de regio voor uw Virtual Network. De regio voor uw FHIR-resource kan afwijken.

![Tabblad Azure Portal-basis](media/private-link/private-link-portal2.png)

Voor resource type zoekt en selecteert u ' micro soft. HealthcareApis/Services '. Selecteer voor resource de FHIR-resource. Selecteer ' fhir ' bij doel-subresource.

![Azure Portal resource tabblad](media/private-link/private-link-portal1.png)

Als u geen bestaande Privé-DNS zone hebt ingesteld, selecteert u (nieuw) privatelink. azurehealthcareapis. com. Als u uw Privé-DNS zone al hebt geconfigureerd, kunt u deze selecteren in de lijst. Deze moet de indeling ' privatelink.azurehealthcareapis.com ' hebben.

![Tabblad Azure Portal configuratie](media/private-link/private-link-portal3.png)

Nadat de implementatie is voltooid, kunt u teruggaan naar het tabblad ' persoonlijke eindpunt verbindingen ' waarop u ' goedgekeurd ' ziet als de verbindings status.

### <a name="manual-approval"></a>Hand matige goed keuring

Voor hand matige goed keuring selecteert u de tweede optie onder resource, "verbinding maken met een Azure-resource op Resource-ID of alias". Voer bij doel-subresource ' fhir ' in als automatische goed keuring.

![Hand matige goed keuring](media/private-link/private-link-manual.png)

Nadat de implementatie is voltooid, kunt u teruggaan naar het tabblad ' persoonlijke eindpunt verbindingen ' waarop u de verbinding kunt goed keuren, afwijzen of verwijderen.

![Opties](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Persoonlijk eind punt testen

Om ervoor te zorgen dat uw FHIR-server geen openbaar verkeer ontvangt nadat de open bare netwerk toegang is uitgeschakeld, kunt u het/metadata-eind punt voor uw server op uw computer aanzoeken. U ontvangt een 403 verboden. Houd er rekening mee dat het tot vijf minuten kan duren nadat de toegangs vlag voor het open bare netwerk is bijgewerkt voordat het open bare verkeer wordt geblokkeerd.

Om ervoor te zorgen dat uw persoonlijke eind punt verkeer naar uw server kan verzenden:

1. Maak een virtuele machine die is verbonden met het virtuele netwerk en het subnet waarin uw persoonlijke eind punt is geconfigureerd. Om ervoor te zorgen dat uw verkeer van de virtuele machine alleen gebruikmaakt van het particuliere netwerk, kunt u uitgaand Internet verkeer via NSG regel uitschakelen.
2. RDP-verbinding met de virtuele machine.
3. Probeer het/metadata-eind punt van uw FHIR-server op te halen van de virtuele machine. u ontvangt de instructie als antwoord.

## <a name="manage-private-endpoint"></a>Privé-eindpunt beheren

### <a name="view"></a>Weergave

Persoonlijke eind punten en de bijbehorende NIC zijn zichtbaar in Azure Portal van de resource groep waarin ze zijn gemaakt.

![Weer geven in resources](media/private-link/private-link-view.png)

### <a name="delete"></a>Verwijderen

Privé-eind punten kunnen alleen worden verwijderd uit Azure Portal via de Blade overzicht (zoals hieronder) of via de optie verwijderen onder netwerk (preview) ' particuliere endpoint Connections '. Als u op de knop verwijderen klikt, worden het persoonlijke eind punt en de bijbehorende NIC verwijderd. Als u alle persoonlijke eind punten aan de FHIR-resource verwijdert en de toegang tot het open bare netwerk is uitgeschakeld, wordt deze door geen enkele aanvraag naar uw FHIR-server door gemaakt.

![Persoonlijk eind punt verwijderen](media/private-link/private-link-delete.png)
