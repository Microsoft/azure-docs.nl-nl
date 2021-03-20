---
title: Virtuele Windows-bureau blad (klassieke) omgeving-Azure
description: De basis elementen van een Windows-omgeving (virtueel bureau blad).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008607"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Windows-omgeving (virtueel bureau blad)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../environment-setup.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Virtueel bureau blad van Windows is een service waarmee gebruikers eenvoudig en veilig toegang krijgen tot hun gevirtualiseerde Bureau bladen en RemoteApps. In dit onderwerp vindt u meer informatie over de algemene structuur van de virtueel-bureaublad omgeving van Windows.

## <a name="tenants"></a>Tenants

De Windows Virtual Desktop-Tenant is de primaire interface voor het beheren van uw virtuele Windows-bureau blad-omgeving. Elke virtuele bureau blad-Tenant van Windows moet worden gekoppeld aan de Azure Active Directory met de gebruikers die zich aanmelden bij de omgeving. Vanuit de virtuele bureau blad-Tenant van Windows kunt u beginnen met het maken van hostgroepen om de werk belastingen van uw gebruikers uit te voeren.

## <a name="host-pools"></a>Hostgroepen

Een hostgroep is een verzameling virtuele Azure-machines die als sessie-hosts worden geregistreerd bij Windows virtueel bureau blad wanneer u de virtueel-bureaublad agent van Windows uitvoert. Alle virtuele machines van de host in een hostgroep moeten vanuit dezelfde installatie kopie worden gebrond voor een consistente gebruikers ervaring.

Een hostgroep kan een van de volgende twee typen zijn:

- Persoonlijk, waarbij elke sessiehost wordt toegewezen aan individuele gebruikers.
- Gepoold, waarbij sessie-hosts verbindingen kunnen accepteren van alle gebruikers die zijn gemachtigd voor een app-groep in de hostgroep.

U kunt extra eigenschappen instellen voor de hostgroep om het gedrag van de taak verdeling te wijzigen, het aantal sessies dat elke sessie-host kan uitvoeren en wat de gebruiker kan doen om in de hostgroep sessies in te stellen, terwijl u zich aanmeldt bij de virtuele Windows-bureaublad sessies. U beheert de resources die worden gepubliceerd voor gebruikers via app-groepen.

## <a name="app-groups"></a>App-groepen

Een app-groep is een logische groepering van toepassingen die zijn geïnstalleerd op sessie-hosts in de hostgroep. Een app-groep kan een van de volgende twee typen zijn:

- RemoteApp, waar gebruikers toegang hebben tot de RemoteApps die u afzonderlijk selecteert en publiceert naar de app-groep
- Bureau blad, waar gebruikers toegang tot het volledige bureau blad hebben

Standaard wordt een bureau blad-app-groep (met de naam bureau blad-toepassings groep) automatisch gemaakt wanneer u een hostgroep maakt. U kunt deze app-groep op elk gewenst moment verwijderen. U kunt echter geen andere bureau blad-app-groep maken in de hostgroep terwijl er een bureau blad-app-groep bestaat. Als u RemoteApps wilt publiceren, moet u een RemoteApp-app-groep maken. U kunt meerdere RemoteApp-app-groepen maken die geschikt zijn voor verschillende werk scenario's. Verschillende RemoteApp-app-groepen kunnen ook overlappende RemoteApps bevatten.

Als u resources naar gebruikers wilt publiceren, moet u deze toewijzen aan app-groepen. Houd rekening met de volgende zaken wanneer u gebruikers aan app-groepen toewijst:

- Een gebruiker kan niet worden toegewezen aan zowel een bureau blad-app-groep als een RemoteApp-app-groep in dezelfde hostgroep.
- Een gebruiker kan worden toegewezen aan meerdere app-groepen binnen dezelfde hostgroep en hun feed wordt een accumulatie van beide app-groepen.

## <a name="tenant-groups"></a>Tenant groepen

In virtueel bureau blad van Windows bevindt de virtuele bureau blad-Tenant van Windows zich de meeste van de installatie en configuratie. De Windows Virtual Desktop Tenant bevat de hostgroepen, app-groepen en toepassings toewijzingen van de app-groep. Er kunnen echter bepaalde situaties zijn waarin u meerdere virtuele bureau blad-tenants tegelijk moet beheren, met name als u een Cloud serviceprovider (CSP) of een hosting partner bent. In deze situaties kunt u een aangepaste virtuele bureau blad-Tenant groep van Windows gebruiken om elk van de virtuele Bureau bladen van Windows te plaatsen en de toegang centraal te beheren. Als u echter slechts één virtueel bureau blad-Tenant van Windows beheert, is het concept van de Tenant groep niet van toepassing en kunt u blijven werken en uw Tenant beheren die voor komt in de standaard Tenant groep.

## <a name="end-users"></a>Eindgebruikers

Nadat u gebruikers aan hun app-groepen hebt toegewezen, kunnen ze verbinding maken met een virtuele Windows-bureau blad-implementatie met een van de virtuele bureau blad-clients van Windows.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gedelegeerde toegang en het toewijzen van rollen aan gebruikers op het [virtuele bureau blad van Windows](delegated-access-virtual-desktop-2019.md).

Zie [een Tenant maken in virtueel bureau blad van Windows](tenant-setup-azure-active-directory.md)voor meer informatie over het instellen van uw virtuele bureau blad-Tenant voor Windows.

Zie een van de volgende artikelen voor meer informatie over het maken van verbinding met Windows Virtual Desktop:

- [Verbinding maken vanaf Windows 10 of Windows 7](connect-windows-7-10-2019.md)
- [Verbinding maken via een webbrowser](connect-web-2019.md)
