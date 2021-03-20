---
title: Machines groeperen voor evaluatie met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u machines groepeert voordat u een evaluatie uitvoert met de Azure Migrate-service.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 364b5949e944a4317aa25f1f1b12545122881cec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752104"
---
# <a name="create-a-group-for-assessment"></a>Een groep maken voor evaluatie

In dit artikel wordt beschreven hoe u groepen machines maakt voor evaluatie met Azure Migrate: Server evaluatie.

[Azure migrate](migrate-services-overview.md) helpt u bij het migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden. 

## <a name="grouping-machines"></a>Machines groeperen

U verzamelt computers in groepen om te beoordelen of ze geschikt zijn voor migratie naar Azure en om Azure-omvang en kosten schattingen voor hen te krijgen. Er zijn een aantal manieren om groepen te maken:

- Als u weet welke computers samen moeten worden gemigreerd, kunt u de groep hand matig maken in Azure Migrate.
- Als u niet zeker weet welke computers moeten worden gegroepeerd, kunt u de visualisatie functionaliteit voor afhankelijkheid in Azure Migrate gebruiken om groepen te maken. 

> [!NOTE]
> De functie voor visualisatie van afhankelijkheden is niet beschikbaar in Azure Government.

## <a name="create-a-group-manually"></a>Hand matig een groep maken

U kunt een groep maken op het moment dat u [een evaluatie maakt](how-to-create-assessment.md).

Als u hand matig een groep wilt maken buiten het maken van een beoordeling, doet u het volgende:

1. Klik in het Azure Migrate project > **overzicht** op **servers evalueren en migreren**. Klik in **Azure migrate: Server evaluatie** op **groepen**
    - Als u het Azure Migrate: Server Assessment nog niet hebt toegevoegd, klikt u hierop om het toe te voegen. [Meer informatie](how-to-assess.md).
    - Als u nog geen Azure Migrate project hebt gemaakt, kunt u [meer informatie](./create-manage-projects.md)vinden.

    ![Groepen selecteren](./media/how-to-create-a-group/select-groups.png)

2. Klik op het **groeps** pictogram.
3. Geef in **groep maken** een groeps naam op en selecteer bij **apparaatnaam** het Azure migrate apparaat dat u voor machine detectie gebruikt.
4. Selecteer in de lijst computer de machines die u wilt toevoegen aan de groep > **maken**.

    ![Groep maken](./media/how-to-create-a-group/create-group.png)

U kunt deze groep nu gebruiken bij het [maken van een Azure VM-evaluatie](how-to-create-assessment.md) of [een AVS-evaluatie (Azure VMware-oplossing)](how-to-create-azure-vmware-solution-assessment.md). Houd er rekening mee dat u een AVS-evaluatie kunt maken voor groepen met alleen VMware-Vm's. 

## <a name="refine-a-group-with-dependency-mapping"></a>Een groep verfijnen met afhankelijkheids toewijzing

Met afhankelijkheids toewijzing kunt u afhankelijkheden op verschillende computers visualiseren. U gebruikt meestal afhankelijkheids toewijzing als u computer groepen wilt beoordelen met een hoger vertrouwens niveau.
- U kunt hiermee computer afhankelijkheden van elkaar controleren voordat u een evaluatie uitvoert. 
- Het helpt ook om uw migratie naar Azure effectief te plannen door ervoor te zorgen dat er niets achterblijft, waardoor onverwachte storingen worden voor komen tijdens de migratie.
- U kunt onafhankelijke systemen detecteren die samen moeten worden gemigreerd, en identificeren of een actief systeem nog steeds gebruikers is of een kandidaat is voor buiten gebruik stellen in plaats van migratie.

Als u al een [afhankelijkheids toewijzing hebt ingesteld](how-to-create-group-machine-dependencies.md)en een bestaande groep wilt verfijnen, gaat u als volgt te werk:

1. Klik op het tabblad **servers** in **Azure migrate: tegel server bepaling** op **groepen**.
2. Klik op de groep die u wilt verfijnen.
    - Als u nog geen afhankelijkheids toewijzing hebt ingesteld, wordt in de kolom **afhankelijkheden** de **installatie status vereist** weer gegeven. Voor elke virtuele machine waarvoor u afhankelijkheden wilt visualiseren, klikt u op **installatie vereist**. Installeer een aantal agents op elke virtuele machine voordat u de computer afhankelijkheden kunt toewijzen. [Meer informatie](how-to-create-group-machine-dependencies.md).

        ![Afhankelijkheids toewijzing toevoegen](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Als u de afhankelijkheids toewijzing al hebt ingesteld, klikt u op de pagina groep op **afhankelijkheden weer geven** om de toewijzing van de groeps afhankelijke map te openen.

3. Nadat u op **afhankelijkheden weer geven** hebt geklikt, wordt in de groep afhankelijkheids kaart het volgende weer gegeven:

    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen naar en van alle computers in de groep waarvoor de afhankelijkheids agenten zijn geïnstalleerd.
    - Afhankelijke computers waarop geen afhankelijkheids agenten zijn geïnstalleerd, worden gegroepeerd op poort nummers.
    - Afhankelijke computers waarop afhankelijkheids agenten zijn geïnstalleerd, worden weer gegeven als afzonderlijke vakken.
    - Processen die worden uitgevoerd op de computer. Vouw elke machine uit om de processen weer te geven.
    - Computer eigenschappen (inclusief FQDN, besturings systeem, MAC-adres). Klik op elke computer vak om de details weer te geven.

4. Als u afhankelijkheden binnen een tijds interval wilt weer geven, wijzigt u het tijds bereik (standaard een uur) door begin-en eind datums op te geven, of de duur.

    > [!NOTE]
    > Het tijds bereik kan Maxi maal een uur duren. Als u een langere periode nodig hebt, gebruikt u Azure Monitor voor het [opvragen van afhankelijke gegevens](how-to-create-group-machine-dependencies.md) gedurende langere tijd.

5. Nadat u de afhankelijkheden hebt geïdentificeerd die u wilt toevoegen aan of verwijderen uit de groep, kunt u de groep wijzigen. Gebruik Ctrl + klikken om computers toe te voegen aan of te verwijderen uit de groep.

    - U kunt alleen gedetecteerde computers toevoegen.
    - Door computers toe te voegen en te verwijderen, worden eerdere beoordelingen voor een groep ongeldig.
    - U kunt eventueel een nieuwe evaluatie maken wanneer u de groep wijzigt.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen en gebruiken van [afhankelijkheids toewijzing](how-to-create-group-machine-dependencies.md) voor het maken van groepen met hoge betrouw baarheid.