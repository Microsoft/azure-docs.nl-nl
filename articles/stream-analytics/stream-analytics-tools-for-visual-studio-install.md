---
title: Azure Stream Analytics-hulpprogram ma's voor Visual Studio instellen
description: In dit artikel worden de installatie vereisten en het instellen van de Azure Stream Analytics-hulpprogram ma's voor Visual Studio beschreven.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018697"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Azure Stream Analytics-hulpprogram ma's voor Visual Studio installeren

Visual Studio 2019 en Visual Studio 2017 ondersteunen Azure Data Lake en Stream Analytics-Hulpprogram Ma's. In dit artikel wordt beschreven hoe u de hulpprogram ma's installeert en verwijdert.

Voor meer informatie over het gebruik van de hulpprogram ma's raadpleegt u [Quick Start: een Azure stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installeren

De hulpprogram ma's worden ondersteund door Visual Studio Enter prise (Ultimate/Premium), Professional en community Editions. Express Edition en Visual Studio voor Mac ondersteunen deze niet.

Hiervoor wordt Visual Studio 2019 aanbevolen.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installeren voor Visual Studio 2019 en 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake-en Stream Analytics-Hulpprogram Ma's maken deel uit van de werk belastingen voor **Azure-ontwikkeling** en- **gegevens opslag en-verwerking** . Schakel een van deze twee werk belastingen in tijdens de installatie. Als Visual Studio al is geïnstalleerd, selecteert u **hulpprogram** ma's  >  **en functies ophalen** om workloads toe te voegen.

Down load [Visual studio 2019 (Preview 2 of hoger) of Visual Studio 2017 (15,3 of hoger)](https://www.visualstudio.com/) en volg de instructies voor het installeren.

Selecteer de werk belasting voor **gegevens opslag en-verwerking** , zoals wordt weer gegeven:

![De werk belasting voor gegevens opslag en-verwerking is geselecteerd](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selecteer de werk belasting **Azure Development** zoals wordt weer gegeven:

![De werk belasting Azure Development is geselecteerd](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Nadat u de werk belasting hebt toegevoegd, werkt u de hulpprogram ma's bij. Deze procedure verwijst naar Visual Studio 2019:

1. Selecteer **extensies extensies**  >  **beheren**.

1. Selecteer in **uitbrei dingen beheren** de optie **Updates** en kies **Azure data Lake en stream Analytics hulpprogram ma's**.

1. Selecteer **bijwerken** om de nieuwste extensie te installeren.

![Visual Studio-extensies en-updates](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installeren voor Visual Studio 2015 en 2013<a name="visual-studio-2015-2013"></a>

De hulpprogram ma's worden ondersteund door Visual Studio Enter prise (Ultimate/Premium), Professional en community Editions. De Express-editie ondersteunt deze niet.

* Installeer Visual Studio 2015 of Visual Studio 2013 update 4.
* Installeer de Microsoft Azure SDK voor .NET versie 2.7.1 of hoger met behulp van het [installatie programma voor het webplatform](https://www.microsoft.com/web/downloads/platform.aspx).
* Installeer [Microsoft Azure data Lake en stream Analytics Hulpprogram ma's voor Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Bijwerk<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Voor Visual Studio 2019 en Visual Studio 2017 wordt een nieuwe versie-herinnering weer gegeven als een Visual Studio-melding.

Voor Visual Studio 2015 en Visual Studio 2013 worden de hulpprogram ma's automatisch op nieuwe versies gecontroleerd. Volg de instructies voor het installeren van de meest recente versie.

## <a name="uninstall"></a>Verwijderen

U kunt Azure Data Lake en Stream Analytics-Hulpprogram Ma's verwijderen. Voor Visual Studio 2019 of Visual Studio 2017 selecteert u **hulpprogram** ma's  >  **en functies ophalen**. Schakel in **wijzigen** Azure data Lake- **en stream Analytics-hulpprogram ma's** uit. Deze wordt weer gegeven onder de werk belasting voor **gegevens opslag en-verwerking** of de werk belasting **Azure Development** .

Ga naar **configuratie scherm**  >  **Program ma's en onderdelen** om te verwijderen van Visual Studio 2015 of Visual Studio 2013. Verwijder **Microsoft Azure data Lake en stream Analytics Hulpprogram ma's voor Visual Studio**.
