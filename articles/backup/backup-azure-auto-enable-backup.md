---
title: Back-up automatisch inschakelen bij het maken van VM's met Azure Policy
description: Een artikel met informatie over het gebruik van Azure Policy om back-up automatisch in te scha kelen voor alle Vm's die in een bepaald bereik zijn gemaakt
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896864"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Back-up automatisch inschakelen bij het maken van VM's met Azure Policy

Een van de belangrijkste verantwoordelijkheden van een back-up-of nalevings beheerder in een organisatie is ervoor te zorgen dat alle bedrijfs kritieke computers een back-up maken met de juiste Bewaar periode.

Azure Backup biedt nu diverse ingebouwde beleids regels (met behulp van [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)) waarmee u automatisch kunt controleren of uw virtuele machines van Azure zijn geconfigureerd voor back-up. Afhankelijk van hoe uw back-upteams en-resources zijn georganiseerd, kunt u een van de onderstaande beleids regels gebruiken:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Beleid 1: back-up op Vm's zonder een bepaalde tag configureren voor een bestaande Recovery Services-kluis op dezelfde locatie

Als uw organisatie een centraal back-upteam heeft dat back-ups beheert tussen toepassings teams, kunt u dit beleid gebruiken om de back-up te configureren voor een bestaande centrale Recovery Services kluis in hetzelfde abonnement en dezelfde locatie als de virtuele machines die worden beheerd. U kunt ervoor kiezen om vm's met een bepaalde tag uit te **sluiten** van het bereik van dit beleid.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Beleid 2-[Preview] Configureer back-up op Vm's met een bepaalde tag naar een bestaande Recovery Services-kluis op dezelfde locatie
Dit beleid werkt hetzelfde als beleid 1 hierboven, met het enige verschil dat u dit beleid kunt gebruiken om vm's op te **nemen** die een bepaalde tag bevatten, in het bereik van dit beleid. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Beleid 3-[Preview] Configureer back-up op Vm's zonder een bepaalde tag naar een nieuwe Recovery Services-kluis met een standaard beleid
Als u toepassingen indeelt in specifieke resource groepen en u wilt dat ze van dezelfde kluis een back-up maken, kunt u met dit beleid deze actie automatisch beheren. U kunt ervoor kiezen om vm's met een bepaalde tag uit te **sluiten** van het bereik van dit beleid.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Beleid 4-[Preview] Configureer back-up op Vm's met een bepaalde tag naar een nieuwe Recovery Services-kluis met een standaard beleid
Dit beleid werkt hetzelfde als beleids 3 hierboven, met het enige verschil dat u dit beleid kunt gebruiken om vm's op te **nemen** die een bepaalde tag bevatten, in het bereik van dit beleid. 

Naast de bovenstaande procedure, bevat Azure Backup ook een beleid voor [alleen controle](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) - **Azure backup moet worden ingeschakeld voor virtual machines**. Met dit beleid wordt aangegeven op welke virtuele machines geen back-up is ingeschakeld, maar worden back-ups voor deze Vm's niet automatisch geconfigureerd. Dit is handig wanneer u alleen de algemene compatibiliteit van de virtuele machines wilt evalueren, maar niet onmiddellijk actie moet ondernemen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Het ingebouwde beleid wordt momenteel alleen ondersteund voor virtuele Azure-machines. Gebruikers moeten er zeker van zijn dat het Bewaar beleid dat tijdens de toewijzing is opgegeven, een VM-Bewaar beleid is. Raadpleeg [Dit](./backup-azure-policy-supported-skus.md) document voor een overzicht van alle VM-sku's die door dit beleid worden ondersteund.

* Beleids regels 1 en 2 kunnen per keer worden toegewezen aan één locatie en abonnement. Als u back-ups wilt maken voor Vm's op verschillende locaties en abonnementen, moet u meerdere exemplaren van de beleids toewijzing maken, één voor elke combi natie van locatie en abonnement.

* Voor beleids regels 1 en 2 wordt het beheer groeps bereik momenteel niet ondersteund.

* Voor beleids regels 1 en 2 kunnen de opgegeven kluis en de virtuele machines die voor back-up zijn geconfigureerd, zich onder verschillende resource groepen bevindt.

* Beleids regels 1, 2, 3 en 4 zijn momenteel niet beschikbaar in nationale Clouds.

* Beleids regels 3 en 4 kunnen worden toegewezen aan één abonnement tegelijk (of in een resource groep binnen een abonnement).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Het ingebouwde beleid gebruiken

In de onderstaande stappen wordt het end-to-end-proces voor het toewijzen van beleid 1 beschreven: **back-up configureren op vm's zonder een bepaalde tag naar een bestaande Recovery Services-kluis op dezelfde locatie** naar een bepaald bereik. Vergelijk bare instructies zijn van toepassing op het andere beleid. Zodra een nieuwe VM wordt gemaakt in het bereik, wordt deze automatisch geconfigureerd voor back-up.

1. Meld u aan bij de Azure Portal en navigeer naar het **beleids** dashboard.
2. Selecteer **definities** in het linkermenu om een lijst op te halen met alle ingebouwde beleids regels voor Azure-resources.
3. Filter de lijst voor **Category = backup** en selecteer het beleid met de naam ' back-up configureren op vm's van een locatie naar een bestaande centrale kluis op dezelfde locatie '.
![Beleids dashboard](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Selecteer de naam van het beleid. U wordt omgeleid naar de gedetailleerde definitie van dit beleid.
![Deel venster beleids definitie](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Selecteer de knop **toewijzen** boven aan het deel venster. Hiermee wordt u omgeleid naar het deel venster **beleid toewijzen** .
6. Selecteer onder **basis beginselen** de drie punten naast het veld **bereik** . Hiermee opent u een context paneel met de rechter muisknop waarin u het abonnement kunt selecteren waarop het beleid moet worden toegepast. U kunt desgewenst ook een resource groep selecteren, zodat het beleid alleen wordt toegepast op virtuele machines in een bepaalde resource groep.
![Basis principes van beleids toewijzing](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Kies op het tabblad **para meters** een locatie in de vervolg keuzelijst en selecteer de kluis en het back-upbeleid waaraan de vm's in het bereik moeten worden gekoppeld. U kunt er ook voor kiezen om een label naam en een matrix met label waarden op te geven. Een virtuele machine die een of meer van de opgegeven waarden voor het opgegeven label bevat, wordt uitgesloten van het bereik van de beleids toewijzing.
![Beleids toewijzings parameters](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Zorg ervoor dat het **effect** is ingesteld op deployIfNotExists.
9. Ga naar **controleren en maken** en selecteer **maken**.

> [!NOTE]
>
> Azure Policy kunnen ook worden gebruikt op bestaande Vm's, met behulp van [herstellen](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Het is raadzaam dat dit beleid per keer niet wordt toegewezen aan meer dan 200 Vm's. Als het beleid is toegewezen aan meer dan 200 Vm's, kan dit ertoe leiden dat de back-up een paar uur later wordt geactiveerd dan het tijdstip dat is opgegeven in de planning.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Policy](../governance/policy/overview.md)
