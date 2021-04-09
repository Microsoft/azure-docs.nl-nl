---
title: Transport Layer Security in Azure Backup
description: Informatie over het inschakelen van Azure Backup om het versleutelings Protocol Transport Layer Security (TLS) te gebruiken om gegevens veilig te houden wanneer ze via een netwerk worden overgedragen.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327114"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security in Azure Backup

Transport Layer Security (TLS) is een versleutelings protocol dat gegevens beveiligd houdt wanneer ze via een netwerk worden overgedragen. Azure Backup maakt gebruik van trans port Layer Security om de privacy te beschermen van back-upgegevens die worden overgedragen. In dit artikel worden de stappen beschreven voor het inschakelen van het TLS 1,2-protocol, dat verbeterde beveiliging biedt ten opzichte van vorige versies.

## <a name="earlier-versions-of-windows"></a>Eerdere versies van Windows

Als op de computer een eerdere versie van Windows wordt uitgevoerd, moeten de onderstaande overeenkomende updates worden geïnstalleerd en moeten de register wijzigingen in de KB-artikelen worden toegepast.

|Besturingssysteem  |KB-artikel |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>De vereiste protocol onderdelen worden geïnstalleerd met de update. Na de installatie moet u de register sleutel wijzigingen in de bovenstaande KB-artikelen gebruiken om de vereiste protocollen correct in te scha kelen.

## <a name="verify-windows-registry"></a>Windows-REGI ster controleren

### <a name="configuring-schannel-protocols"></a>SChannel-protocollen configureren

De volgende register sleutels zorgen ervoor dat het TLS 1,2-protocol is ingeschakeld op het SChannel-onderdeel niveau:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>De weer gegeven waarden zijn standaard ingesteld in Windows Server 2012 R2 en nieuwere versies. Als de register sleutels niet aanwezig zijn in deze versies van Windows, hoeven ze niet te worden gemaakt.

### <a name="configuring-net-framework"></a>.NET Framework configureren

De volgende register sleutels configureren .NET Framework voor de ondersteuning van sterke crypto grafie. U kunt hier meer lezen over het [configureren van .NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="why-enable-tls-12"></a>Waarom TLS 1,2 inschakelen?

TLS 1,2 is veiliger dan eerdere cryptografische protocollen, zoals SSL 2,0, SSL 3,0, TLS 1,0 en TLS-1,1. Azure Backup Services biedt al volledige ondersteuning voor TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>Wat bepaalt het gebruikte versleutelings Protocol?

De hoogste Protocol versie die door zowel de client als de server wordt ondersteund, wordt onderhandeld om de versleutelde conversatie tot stand te brengen. Zie [een beveiligde sessie tot stand brengen met behulp van TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)voor meer informatie over het TLS-hand Shake protocol.

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Wat is de impact van het niet inschakelen van TLS 1,2?

Voor een betere beveiliging van protocol downgrade-aanvallen wordt Azure Backup gestart met het uitschakelen van TLS-versies die ouder zijn dan 1,2. Dit maakt deel uit van een lange termijn verschuiving over services om verouderde protocol-en versleutelings Suite verbindingen te weigeren. Azure Backup Services en onderdelen bieden ondersteuning voor TLS 1,2. Windows-versies die geen vereiste updates of bepaalde aangepaste configuraties hebben, kunnen echter nog steeds voor komen dat TLS 1,2-protocollen worden aangeboden. Dit kan fouten veroorzaken, inclusief, maar niet beperkt tot een of meer van de volgende:

- Back-up-en herstel bewerkingen kunnen mislukken.
- Back-uponderdelen verbindings fouten met fout 10054 (een bestaande verbinding is geforceerd gesloten door de externe host).
- Services die betrekking hebben op Azure Backup, worden niet op de gebruikelijke manier gestopt of gestart.

## <a name="additional-resources"></a>Aanvullende bronnen

- [TLS-protocol (Transport Layer Security)](/windows/win32/secauthn/transport-layer-security-protocol)
- [Ondersteuning voor TLS 1,2 voor geïmplementeerde besturings systemen](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Aanbevolen procedures voor trans port Layer Security (TLS) met de .NET Framework](/dotnet/framework/network-programming/tls)