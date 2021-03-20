---
title: Kerberos Key Distribution Center-proxy instellen Windows virtueel bureau blad-Azure
description: Een hostgroep voor virtuele Windows-Bureau bladen instellen voor gebruik van een Kerberos-Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219652"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Een Kerberos-Key Distribution Center proxy configureren (preview-versie)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als openbare preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u een Kerberos-Key Distribution Center (KDC)-proxy (preview) configureert voor uw hostgroep. Met deze proxy kunnen organisaties worden geverifieerd met Kerberos buiten hun bedrijfs grenzen. U kunt bijvoorbeeld de KDC-proxy gebruiken om smartcard verificatie in te scha kelen voor externe clients.

## <a name="how-to-configure-the-kdc-proxy"></a>De KDC-proxy configureren

De KDC-proxy configureren:

1. Meld u aan bij de Azure Portal als beheerder.

2. Ga naar de pagina virtueel bureau blad van Windows.

3. Selecteer de hostgroep waarvoor u de KDC-proxy wilt inschakelen en selecteer vervolgens **RDP-eigenschappen**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de Azure Portal pagina met een gebruiker die hostgroepen selecteert, gevolgd door de naam van de voor beeld-hostgroep en vervolgens RDP-eigenschappen.](media/rdp-properties.png)

4. Selecteer het tabblad **Geavanceerd** en geef een waarde op in de volgende notatie zonder spaties:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname waarin het tabblad Geavanceerd wordt weer gegeven, met de waarde die is ingevoerd, zoals beschreven in stap 4.](media/advanced-tab-selected.png)

5. Selecteer **Opslaan**.

6. De geselecteerde hostgroep moet nu beginnen met het verlenen van RDP-verbindings bestanden met het kdcproxyname-veld dat u hebt ingevoerd.

## <a name="next-steps"></a>Volgende stappen

De RDGateway-functie in Extern bureaublad-services bevat een KDC-proxy service. Zie [de functie RD-Gateway implementeren in het virtuele bureau blad van Windows](rd-gateway-role.md) voor het instellen van een doel voor Windows virtueel bureau blad.
