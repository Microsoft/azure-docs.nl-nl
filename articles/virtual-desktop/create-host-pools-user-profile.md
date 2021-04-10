---
title: Container share voor het FSLogix-Profiel van Windows-virtueel bureau blad-Azure
description: Een FSLogix-profiel container instellen voor een Windows Virtual Desktop-hostgroep met behulp van een bestands share op basis van een virtuele machine.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 69582f26544d954088fd5a57bfe5730750b7b5a8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447910"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Een profielcontainer maken voor een hostpool met behulp van een bestandsshare

De virtueel-bureaublad service van Windows biedt FSLogix-profiel containers als de aanbevolen gebruikers profiel oplossing. Het is niet raadzaam om de oplossing voor de gebruikers profiel schijf (UPD) te gebruiken, die wordt afgeschaft in toekomstige versies van Windows virtueel bureau blad.

In dit artikel wordt uitgelegd hoe u een FSLogix voor een hostgroep instelt met behulp van een bestands share op basis van een virtuele machine. Het is raadzaam om Azure Files te gebruiken in plaats van bestands shares. Zie de [FSLogix-site](https://docs.fslogix.com/)voor meer documentatie over FSLogix.

>[!NOTE]
>Zie [opslag opties voor FSLogix-profiel containers](store-fslogix-profile.md)als u op zoek bent naar het vergelijkings materiaal over de verschillende FSLogix-profiel container opslag opties in Azure.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Een nieuwe virtuele machine maken die zal fungeren als een bestands share

Zorg er bij het maken van de virtuele machine voor dat u deze plaatst op hetzelfde virtuele netwerk als de virtuele machines van de hostgroep of op een virtueel netwerk dat verbinding heeft met de virtuele machines van de hostgroep. U kunt op verschillende manieren een virtuele machine maken:

- [Een virtuele machine maken op basis van een installatie kopie van Azure galerie](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele machine maken op basis van een beheerde installatie kopie](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Een virtuele machine maken op basis van een onbeheerde installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Nadat u de virtuele machine hebt gemaakt, voegt u deze toe aan het domein door de volgende handelingen uit te voeren:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt ingevoerd tijdens het maken van de virtuele machine.
2. Start **configuratie scherm** op de virtuele machine en selecteer **systeem**.
3. Selecteer **computer naam**, selecteer **instellingen wijzigen** en selecteer vervolgens **wijzigen...**
4. Selecteer **domein** en voer vervolgens het Active Directory domein in op het virtuele netwerk.
5. Verifieer met een domein account dat bevoegdheden heeft voor computers die lid zijn van een domein.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>De virtuele machine voorbereiden om te fungeren als een bestands share voor gebruikers profielen

Hieronder vindt u algemene instructies voor het voorbereiden van een virtuele machine om te fungeren als een bestands share voor gebruikers profielen:

1. Voeg het virtuele bureau blad van Windows Active Directory gebruikers toe aan een [Active Directory beveiligings groep](/windows/security/identity-protection/access-control/active-directory-security-groups/). Deze beveiligings groep wordt gebruikt om de virtuele Windows-bureaublad gebruikers te verifiëren op de bestands share-virtuele machine die u zojuist hebt gemaakt.
2. [Maak verbinding met de virtuele machine van de bestands share](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Op de bestands share-virtuele machine maakt u een map op **station C** die wordt gebruikt als de profiel share.
4. Klik met de rechter muisknop op de nieuwe map, selecteer **Eigenschappen**, **delen** en selecteer vervolgens **Geavanceerd delen...**.
5. Selecteer **deze map delen**, selecteer **machtigingen..**. en selecteer vervolgens **toevoegen...**.
6. Zoek naar de beveiligings groep waaraan u de Windows-gebruikers van het virtuele bureau blad hebt toegevoegd en zorg ervoor dat de groep **volledig beheer** heeft.
7. Nadat u de beveiligings groep hebt toegevoegd, klikt u met de rechter muisknop op de map, selecteert u **Eigenschappen**, **delen** selecteren en kopieert u het **netwerkpad dat u wilt gebruiken voor later** gebruik.

Zie de [FSLogix-documentatie](/fslogix/fslogix-storage-config-ht/)voor meer informatie over machtigingen.

## <a name="configure-the-fslogix-profile-container"></a>De FSLogix-profiel container configureren

Als u de virtuele machines met de FSLogix-software wilt configureren, gaat u als volgt te werk op elke computer die is geregistreerd bij de hostgroep:

1. [Maak verbinding met de virtuele machine](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) met de referenties die u hebt ingevoerd tijdens het maken van de virtuele machine.
2. Start een Internet browser en navigeer naar [deze koppeling](https://go.microsoft.com/fwlink/?linkid=2084562) om de FSLogix-agent te downloaden.
3. Ga \\ \\ in het zip-bestand naar een Win32- \\ versie of \\ \\ x64- \\ versie en voer **FSLogixAppsSetup** uit om de FSLogix-agent te installeren.  Zie [FSLogix downloaden en installeren](/fslogix/install-ht/)voor meer informatie over het installeren van FSLogix.
4. Navigeer naar **programma bestanden**  >  **FSLogix**  >  **apps** om te bevestigen dat de agent is geïnstalleerd.
5. Voer in het menu Start **regedit** uit als Administrator. Navigeer naar **Computer \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.
6. Maak een sleutel met de naam **profielen**.
7. Maak de volgende waarden voor de sleutel profielen:

| Naam                | Type               | Gegevens/waarde                        |
|---------------------|--------------------|-----------------------------------|
| Ingeschakeld             | DWORD              | 1                                 |
| VHDLocations        | Waarde met meerdere teken reeksen | "Netwerkpad voor bestands share"     |

>[!IMPORTANT]
>Voor het beveiligen van uw virtuele bureau blad-omgeving in azure, raden we u aan om de binnenkomende poort 3389 niet te openen op uw virtuele machines. Voor het virtuele bureau blad van Windows is geen open bare poort 3389 voor gebruikers nodig om toegang te krijgen tot de virtuele machines van de hostgroep. Als u poort 3389 moet openen voor het oplossen van problemen, raden we u aan [just-in-time-VM-toegang](../security-center/security-center-just-in-time.md)te gebruiken.
