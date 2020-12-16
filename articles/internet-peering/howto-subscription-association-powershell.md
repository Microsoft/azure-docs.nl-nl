---
title: Peer-ASN koppelen aan een Azure-abonnement met behulp van Power shell
titleSuffix: Azure
description: Peer-ASN koppelen aan een Azure-abonnement met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590185"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Peer-ASN koppelen aan een Azure-abonnement met behulp van Power shell

Voordat u een peering-aanvraag indient, moet u eerst uw ASN met het Azure-abonnement koppelen met behulp van de onderstaande stappen.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN maken om uw ASN te koppelen aan een Azure-abonnement

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Aanmelden bij uw Azure-account en uw abonnement selecteren
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registreren voor de resource provider voor peering
Registreer u voor de resource provider voor de peering in uw abonnement met behulp van de onderstaande opdracht. Als u deze niet uitvoert, zijn Azure-resources die vereist zijn voor het instellen van peering, niet toegankelijk.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

U kunt de registratie status controleren met behulp van de onderstaande opdrachten:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Wacht tot *RegistrationState* is ingesteld op ' geregistreerd ' voordat u doorgaat. Het kan 5 tot 30 minuten duren nadat u de opdracht hebt uitgevoerd.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>De peer gegevens bijwerken die zijn gekoppeld aan dit abonnement

Hieronder ziet u een voor beeld van het bijwerken van peer gegevens.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -De naam komt overeen met de resource naam en kan alles zijn wat u kiest. -Peer naam komt echter overeen met de naam van uw bedrijf en moet zo dicht mogelijk bij uw PeeringDB-profiel staan. Houd er rekening mee dat waarde voor-peernaam alleen de tekens a-z, A-Z en spaties ondersteunt.

Een abonnement kan meerdere Asn's hebben. Werk de peering-informatie voor elke ASN bij. Zorg ervoor dat ' naam ' uniek is voor elke ASN.

Peers wordt verwacht een volledig en bijgewerkt profiel te hebben op [PeeringDB](https://www.peeringdb.com). We gebruiken deze informatie tijdens de registratie om de details van de peer te valideren, zoals NOC-informatie, technische contact gegevens en hun aanwezigheid bij de peering-faciliteiten, enzovoort.

Houd er rekening mee dat in plaats van **{subscriptionId}** in de bovenstaande uitvoer de werkelijke abonnements-id wordt weer gegeven.

## <a name="view-status-of-a-peerasn"></a>De status van een PeerASN weer geven

Controleer op ASN-validatie status met behulp van de onderstaande opdracht:

```powershell
Get-AzPeerAsn
```

Hieronder ziet u een voor beeld van een antwoord:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Wacht totdat de ValidationState is ingeschakeld voordat een peering-aanvraag wordt verzonden. Het kan tot 12 uur duren voordat deze goed keuring is uitgevoerd.

## <a name="modify-peerasn"></a>PeerAsn wijzigen
U kunt NOC elke contact gegevens op elk gewenst moment wijzigen.

Hieronder ziet u een voorbeeld:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn verwijderen
Het is momenteel niet mogelijk om een PeerASN te verwijderen. Als u PeerASN moet verwijderen, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Volgende stappen

* [Direct-peering maken of bewerken](howto-direct-powershell.md)
* [Een verouderde Direct-peering converteren in een Azure-resource](howto-legacy-direct-powershell.md)
* [Uitwisseling van peering maken of wijzigen](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)
