---
title: seznam AzCopy | Microsoft Docs
description: Tento článek popisuje referenční informace o příkazu AzCopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f4d54e4fd8ca0d43cf3d7087006bb51a15a97bcd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289224"
---
# <a name="azcopy-list"></a>azcopy list

Zobrazí seznam entit v daném prostředku.

## <a name="synopsis"></a>Stručný obsah

V aktuální verzi jsou podporovány pouze kontejnery objektů BLOB.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Související koncepční články

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)
- [Přenos dat s použitím AzCopy a úložiště souborů](storage-use-azcopy-files.md)
- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Příklady

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Možnosti

|Možnost|Popis|
|--|--|
|-h,--help|Zobrazit obsah nápovědu pro příkaz list|
|--strojově čitelné|Zobrazuje seznam velikostí souborů v bajtech.|
|--megabajtů-units|Zobrazí jednotky v objednávkách od 1000 do 1024.|
|--spuštěno – se shodují|Spočítá celkový počet souborů a jejich velikosti.|

## <a name="options-inherited-from-parent-commands"></a>Možnosti zděděné z nadřazených příkazů

|Možnost|Popis|
|---|---|
|--Cap – MB/s float|Velká rychlost přenosu v megabajtech za sekundu. Okamžitá propustnost se může mírně lišit od Cap. Pokud je tato možnost nastavená na hodnotu nula nebo je vynechána, propustnost nebude omezené.|
|--výstupní řetězec typu|Formát výstupu příkazu Mezi možnosti patří: text, JSON. Výchozí hodnota je "text".|
|--Trusted – řetězec Microsoft-přípony   |Určuje další přípony domén, kde se můžou odesílat přihlašovací tokeny Azure Active Directory.  Výchozí hodnota je *. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Zde uvedené jsou přidány do výchozího nastavení. Z důvodu zabezpečení byste měli sem umístit jenom Microsoft Azure domény. Více položek oddělte středníkem.|

## <a name="see-also"></a>Viz také:

- [azcopy](storage-ref-azcopy.md)
