---
title: 'Quickstart: Een Windows Data Science Virtual Machine maken'
titleSuffix: Azure Data Science Virtual Machine
description: Configureer en maak een Data Science Virtual Machine op Azure voor analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: e876ddc9b4df32cad5bbf15215da5ce2f912bba1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109722"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Quickstart: De Data Science Virtual Machine instellen voor Windows

Aan de slag met een Windows Server 2019 Data Science Virtual Machine.

## <a name="prerequisite"></a>Vereiste

Voor het maken van een Windows Data Science Virtual Machine moet u over een Azure-abonnement beschikken. [Azure gratis uitproberen](https://azure.com/free).
Gratis Azure-accounts bieden geen ondersteuning voor SKU’s voor virtuele machines met GPU.

## <a name="create-your-dsvm"></a>Uw DSVM maken

Ga als volgt te werk om een DSVM-exemplaar te maken:

1. Ga naar de [Azure-portal](https://portal.azure.com). Als u nog niet bent aangemeld bij uw Azure-account, wordt u gevraagd om dit te doen.
1. Zoek de vermelding van de virtuele machine door 'data science virtual machine' te typen en 'Data Science Virtual Machine - Windows 2019' te selecteren.

1. Selecteer de knop **Maken** onderaan.

1. U wordt omgeleid naar de blade Een virtuele machine maken.

1. Vul het tabblad **Basis** in:
      * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u datgene waarop de machine wordt gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
      * **Resourcegroep**: Maak een nieuwe groep of gebruik een bestaande.
      * **Naam van virtuele machine**: Voer de naam van de virtuele machine in. Dit is hoe het eruit zal zien in de Azure-portal.
      * **Locatie**: Selecteer het geschiktste datacenter. Dit is het datacenter waarop de meeste van uw gegevens zijn opgeslagen of dat zich fysiek het dichtst bij u in de buurt bevindt, voor de snelste netwerktoegang. Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Installatiekopie**: Laat de standaardwaarde staan.
      * **Grootte**: Dit moet automatisch worden gevuld met een grootte die geschikt is voor algemene werkbelastingen. Lees meer over [Windows VM-grootten in Azure](../../virtual-machines/sizes.md).
      * **Gebruikersnaam**: Voer de gebruikersnaam van de beheerder in. Dit is de gebruikersnaam die u zult gebruiken om u aan te melden bij de virtuele machine, en hoeft niet hetzelfde te zijn als uw Azure-gebruikersnaam.
      * **Wachtwoord**: Voer het wachtwoord in waarmee u zich gaat aanmelden bij de virtuele machine.    
1. Selecteer **Controleren + maken**.
1. **Beoordelen en maken**
   * Controleer of alle informatie die u hebt ingevoerd, correct is. 
   * Selecteer **Maken**.


> [!NOTE]
> * U betaalt geen licentiekosten voor de software, die vooraf is geladen op de virtuele machine. U betaalt wel de berekeningskosten voor de servergrootte die u in de stap **Grootte** hebt gekozen.
> * De inrichting duurt 10 tot 20 minuten. U kunt de status van uw VM bekijken in de Azure-portal.

## <a name="access-the-dsvm"></a>De DSVM openen

Nadat de VM is gemaakt en ingericht, volgt u de vermelde stappen om [verbinding te maken met uw Azure-gebaseerde virtuele machine](../../marketplace/azure-vm-create-using-approved-base.md). Gebruik de beheerdersaccountreferenties die u hebt geconfigureerd in de stap **Basis** voor het maken van een virtuele machine. 

U bent nu klaar om te beginnen met het gebruik van de hulpprogramma's die zijn geïnstalleerd en geconfigureerd op de VM. Veel van de hulpprogramma's kunnen worden geopend via tegels in het **Start**-menu en pictogrammen op het bureaublad.

<a name="tools"></a>


## <a name="next-steps"></a>Volgende stappen

* Verken de hulpprogramma’s op de DSVM door het **Start**-menu te openen.
* Ontdek meer over Azure Machine Learning door [Wat is Azure Machine Learning?](../overview-what-is-azure-ml.md) te lezen en [zelfstudies](../index.yml) uit te proberen.
* Lees het artikel [Data Science met een Windows Data Science Virtual Machine in Azure](./vm-do-ten-things.md)
