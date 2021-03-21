---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555308"
---
#### <a name="to-create-a-cloud-appliance"></a>Een cloudapparaat maken

1. Ga in Azure Portal naar de **StorSimple Manager-apparaatbeheerfunctie**.
2. Ga naar de blade **Apparaten**. Klik in de opdrachtbalk op de blade met het serviceoverzicht op **Cloudapparaat maken**.
    ![StorSimple- cloudapparaat maken](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. Geef de volgende gegevens op de blade **Cloudapparaat maken** op.
   
    ![StorSimple maken](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Naam**: een unieke naam voor uw cloudapparaat.
   2. **Model**: het model van het cloudapparaat. Een 8010-apparaat biedt 30 TB Standard-opslag terwijl 8020 64 TB Premium Storage heeft. Geef 8010 op om ophaalscenario's op itemniveau vanuit back-ups te implementeren. Selecteer 8020 om workloads met hoge prestaties en lage latentie te implementeren of voor gebruik als een tweede apparaat voor herstel na noodgevallen.
   3. **Versie**: kies de versie van het cloudapparaat. De versie die overeenkomt met de versie van de installatiekopie van de virtuele schijf die wordt gebruikt voor het maken van het cloudapparaat. Aangezien de versie van het cloudapparaat bepaalt welk fysiek apparaat voor failover kan dienen of van welk fysiek apparaat u een kloon kunt maken, is het belangrijk dat u de juiste versie van het cloudapparaat maakt.
   4. **Virtueel netwerk**: geef een virtueel netwerk op dat u wilt gebruiken met dit cloudapparaat. Als u Premium Storage gebruikt, moet u een virtueel netwerk selecteren dat wordt ondersteund met het Premium Storage-account. De niet-ondersteunde virtuele netwerken worden lichter gekleurd weergegeven in de vervolgkeuzelijst. U wordt gewaarschuwd als u een niet-ondersteund virtueel netwerk selecteert.
   5. **Subnet**: op basis van het virtuele netwerk dat is geselecteerd, geeft de vervolgkeuzelijst de gekoppelde subnetten weer. Wijs een subnet toe aan uw cloudapparaat.
   6. **Opslagaccount**: selecteer een opslagaccount voor de installatiekopie van het cloudapparaat tijdens het inrichten. Dit opslagaccount moet zich in dezelfde regio bevinden als het cloudapparaat en virtuele netwerk. Het mag niet worden gebruikt voor de opslag van gegevens door ofwel het fysieke of het cloudapparaat. Voor dit doel wordt standaard een nieuw opslagaccount gemaakt. Als u echter al een opslagaccount hebt dat hiervoor geschikt is, kunt u dit in de lijst selecteren. Als u een Premium cloudapparaat maakt, worden in de vervolgkeuzelijst alleen Premium Storage-accounts weergegeven.
      
      > [!NOTE]
      > Het cloudapparaat kan alleen met de Azure Storage-accounts werken.
    
   7. Selecteer het selectievakje om aan te geven dat u begrijpt dat de gegevens die zijn opgeslagen op het cloudapparaat in een Microsoft-datacenter worden gehost.
       * Wanneer u alleen een fysiek apparaat gebruikt, wordt de versleutelingssleutel op uw apparaat opgeslagen. Daarom kan Microsoft deze niet ontsleutelen.

       * Wanneer u een cloudapparaat gebruikt, worden zowel de versleutelingssleutel als de ontsleutelingssleutel opgeslagen in Microsoft Azure. Zie de [beveiligingsoverwegingen voor het gebruik van een cloudapparaat](../articles/storsimple/storsimple-8000-security.md) voor meer informatie.
   8. Klik op **Maken** om het cloudapparaat in te richten. Het kan ongeveer 30 minuten duren voordat het apparaat is ingericht. U krijgt een melding wanneer het cloudapparaat is gemaakt. Ga naar de blade Apparaten. De lijst met apparaten wordt vernieuwd en het cloudapparaat wordt weergegeven. De status van het apparaat is **Gereed voor configuratie**.
      
      ![StorSimple-cloudapparaat - gereed voor configuratie](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)