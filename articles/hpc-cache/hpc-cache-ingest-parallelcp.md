---
title: Azure HPC cache-gegevens opname-parallel kopiëren script
description: Een script voor parallel kopiëren gebruiken om gegevens naar een Blob Storage-doel te verplaatsen in de Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: ff7b15a36c5ef19a1fa6ffdca7697dd6ba97c29f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092351"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Script methode voor het opslaan van gegevens in de Azure HPC-cache-parallel kopiëren

In dit artikel vindt u instructies voor het maken van het ``parallelcp`` script en het gebruik ervan om gegevens te verplaatsen naar een BLOB storage-container voor gebruik met de Azure HPC-cache.

Lees [gegevens verplaatsen naar Azure Blob Storage](hpc-cache-ingest.md)voor meer informatie over het verplaatsen van gegevens naar Blob Storage voor uw Azure HPC-cache.

## <a name="create-the-parallelcp-script"></a>Het parallelcp-script maken

In het onderstaande script wordt het uitvoer bare bestand toegevoegd `parallelcp` . (Dit script is ontworpen voor Ubuntu; als u een andere distributie gebruikt, moet u dit ``parallel`` afzonderlijk installeren.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Voor beeld van parallel kopiëren

In dit voor beeld wordt het script voor parallelle kopieën gebruikt om te compileren ``glibc`` met behulp van bron bestanden in de Azure HPC-cache.

De bron bestanden worden in het cache geheugen van de Azure HPC-cache geplaatst en de object bestanden worden opgeslagen op de lokale vaste schijf.

In dit voor beeld wordt het parallel kopiëren-script gebruikt met de optie ``-j`` en ``make`` om parallel Lise ring te krijgen.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
