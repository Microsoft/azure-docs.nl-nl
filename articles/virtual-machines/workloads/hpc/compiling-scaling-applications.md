---
title: HPC-toepassingen schalen - Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het schalen van HPC-toepassingen op azure-VM's.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600961"
---
# <a name="scaling-hpc-applications"></a>HPC-toepassingen schalen

Voor optimale prestaties bij omhoog schalen en uitschalen van HPC-toepassingen in Azure zijn experimenten voor het afstemmen van prestaties en optimalisatie voor de specifieke workload vereist. Deze sectie en de pagina's met specifieke VM-reeksen bieden algemene richtlijnen voor het schalen van uw toepassingen.

## <a name="application-setup"></a>Installatie van toepassing
De [azurehpc-repo](https://github.com/Azure/azurehpc) bevat veel voorbeelden van:
- Toepassingen optimaal instellen [en](https://github.com/Azure/azurehpc/tree/master/apps) uitvoeren.
- Configuratie van [bestandssystemen en clusters](https://github.com/Azure/azurehpc/tree/master/examples).
- [Zelfstudies over](https://github.com/Azure/azurehpc/tree/master/tutorials) hoe u eenvoudig aan de slag kunt gaan met een aantal algemene toepassingswerkstromen.

## <a name="optimally-scaling-mpi"></a>MPI optimaal schalen 

De volgende suggesties zijn van toepassing voor een optimale efficiëntie, prestaties en consistentie bij het schalen van toepassingen:

- Gebruik voor kleinere taken (dat wil zeggen < 256.000 verbindingen) de optie:
   ```bash
   UCX_TLS=rc,sm
   ```

- Voor grotere taken (dat wil zeggen > 256.000 verbindingen) gebruikt u de volgende optie:
   ```bash
   UCX_TLS=dc,sm
   ```

- In het bovenstaande gebruikt u het volgende om het aantal verbindingen voor uw MPI-taak te berekenen:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Adaptieve routering
Met adaptieve routering (AR) kunnen met Azure Virtual Machines (VM's) met EDR en MALWARE InfiniBand netwerkcongestie automatisch worden gedetecteerd en voorkomen door dynamisch optimale netwerkpaden te selecteren. Als gevolg hiervan biedt AR verbeterde latentie en bandbreedte op het InfiniBand-netwerk, waardoor de prestaties en schaalefficiëntie worden verbeterd. Raadpleeg het [TechCommunity-artikel voor meer informatie.](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)

## <a name="process-pinning"></a>Proces vastmaken

- Maak processen vast aan kernen met behulp van een sequentiële pinning-benadering (in plaats van een benadering voor automatisch in balans brengen). 
- Binding per Numa/Core/HwThread is beter dan standaardbinding.
- Gebruik voor hybride parallelle toepassingen (OpenMP+ MPI) 4 threads en 1 MPI-positie per CCX op HB- en HBv2-VM-grootten.
- Voor pure MPI-toepassingen experimenteert u met 1-4 MPI-rangschikken per CCX voor optimale prestaties op VM-grootten voor HB en HBv2.
- Sommige toepassingen met een extreme gevoeligheid voor geheugenbandbreedte kunnen profiteren van het gebruik van een kleiner aantal kernen per CCX. Voor deze toepassingen kan het gebruik van 3 of 2 kernen per CCX leiden tot minder geheugenbandbreedte- en hogere werkelijke prestaties of consistentere schaalbaarheid. Met name MPI Allreduce kan profiteren van deze aanpak.
- Voor aanzienlijk grotere runs wordt u aangeraden UD- of hybride RC+UD-transporten te gebruiken. Veel MPI-bibliotheken/runtimebibliotheken doen dit intern (zoals UCX of MVAPICH2). Controleer uw transportconfiguraties op grootschalige uitvoeringen.

## <a name="compiling-applications"></a>Toepassingen compileren
<br>
<details>
<summary>Klik om uit te vouwen</summary>

Hoewel dit niet nodig is, biedt het compileren van toepassingen met de juiste optimalisatievlaggen de beste opschaalprestaties op VM's uit de HB- en HC-serie.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimizing C/C++ Compiler

Het AOCC-compilersysteem (AMD Optimizing C/C++ Compiler) biedt een hoog niveau van geavanceerde optimalisaties, multithreading en processorondersteuning, waaronder wereldwijde optimalisatie, vectorisatie, inter-procedurele analyses, lustransformaties en het genereren van code. Binaire AOCC-compiler-bestanden zijn geschikt voor Linux-systemen met GNU C Library (binaire bestanden) versie 2.17 en hoger. De compilersuite bestaat uit een C/C++-compiler (clang), een Fortran-compiler (FLANG) en een Fortran-front-end naar Clang (Dragon Dragon).

### <a name="clang"></a>Clang

Clang is een C-, C++- en Objective-C-compiler die voorverwerking, parsering, optimalisatie, codegeneratie, assembly en koppeling afhandelt. Clang ondersteunt de vlag voor het genereren en afstemmen van de beste code voor de  `-march=znver1` op AMD gebaseerde x86-architectuur.

### <a name="flang"></a>FLANG

De FLANG-compiler is een recente toevoeging aan de AOCC-suite (april 2018 toegevoegd) en is momenteel beschikbaar als voorlopige versie voor ontwikkelaars om te downloaden en te testen. Op basis van Fortran 2008 breidt AMD de GitHub-versie van FLANG uit ( https://github.com/flang-compiler/flang) . De FLANG-compiler ondersteunt alle Clang-compileropties en een extra aantal FLANG-specifieke compileropties.

### <a name="dragonegg"></a>DragonEgg

DragonEgg is een gcc-invoegcode die de optimalisaties en codegeneratoren van GCC vervangt door de generatoren van het LLVM-project. DragonEgg die wordt geleverd met AOCC werkt met gcc-4.8.x, is getest op x86-32/x86-64-doelen en is met succes gebruikt op verschillende Linux-platforms.

G Aggregateran is de werkelijke front-end voor Fortran-programma's die verantwoordelijk zijn voor het voorverwerken, parseren en semantische analyse die de tussenliggende GCC-EDEMOPLE-weergave (IR) genereert. DragonEgg is een GNU-invoegprogramma dat kan worden aangesloten op de GRan-compilatiestroom. De GNU-invoeglijkings-API wordt geïmplementeerd. Met de invoegarchitectuur wordt DragonEgg het compiler-stuurprogramma, dat de verschillende fasen van de compilatie aandurft.  Nadat u de download- en installatie-instructies hebt gevolgd, kan Dragon Dragon worden aangeroepen met behulp van: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI Compiler
PGI Community Edition ver. 17 is bevestigd dat het werkt met AMD EPYC. Een met PGI gecompileerde versie van STREAM biedt volledige geheugenbandbreedte van het platform. De nieuwere Community Edition 18.10 (november 2018) zou ook goed moeten werken. Hieronder vindt u voorbeeld-CLI om optimaal te compileren met de Intel Compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel Compiler ver. 18 is bevestigd dat het werkt met AMD EPYC. Hieronder vindt u voorbeeld-CLI om optimaal te compileren met de Intel Compiler.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC Compiler 
Voor HPC raadt AMD GCC-compiler 7.3 of nieuwer aan. Oudere versies, zoals 4.8.5 in RHEL/CentOS 7.4, worden niet aanbevolen. GCC 7.3 en hoger levert aanzienlijk betere prestaties op HPL-, HPCG- en DGEMM-tests.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Volgende stappen

- Test uw kennis met een [leermodule over het optimaliseren van HPC-toepassingen in Azure.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Bekijk het [overzicht van de HBv3-serie](hbv3-series-overview.md) en [het overzicht van de HC-serie.](hc-series-overview.md)
- Lees meer over de meest recente aankondigingen, HPC-workloadvoorbeelden en prestatieresultaten op de [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Meer informatie over [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.
