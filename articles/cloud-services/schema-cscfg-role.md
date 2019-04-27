---
title: Schéma rolí cloudové služby Azure Cloud Services | Dokumentace Microsoftu
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: ''
ms.author: adegeo
manager: timlt
ms.openlocfilehash: aa6f8a821edea6261d64bb411154e82fdf212a8d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130241"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schéma konfigurace rolí cloudové služby Azure Cloud Services

`Role` Prvek konfiguračního souboru Určuje počet instancí role pro nasazení pro každou roli ve službě, hodnoty všech nastavení konfigurace a kryptografický otisk pro jakékoli certifikáty přidružené k roli.

Další informace o schématu konfigurace služby Azure najdete v tématu [schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md). Další informace o schématu definice služby Azure najdete v tématu [Cloudová služba (klasická) schématu definice](schema-csdef-file.md).

##  <a name="Role"></a> Role Element
Následující příklad ukazuje `Role` elementu a jeho podřízené prvky.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Následující tabulka popisuje atributy `Role` elementu.

| Atribut | Popis |
| --------- | ----------- |
| jméno   | Povinná hodnota. Určuje název role. Název musí odpovídat názvu k dispozici pro roli v definičním souboru služby.|
| vmName | Volitelné. Určuje název DNS pro virtuální počítač. Název musí mít 10 znaků nebo méně.|

Následující tabulka popisuje podřízených elementů `Role` elementu.

| Element | Popis |
| ------- | ----------- |
| Instance | Povinná hodnota. Určuje počet instancí k nasazení pro tuto roli. Počet instancí, které je určené celého čísla pro `count` atribut.|
| Nastavení   | Volitelné. Určuje název a hodnotu v kolekci nastavení pro roli. Název nastavení je určené pro řetězec `name` řetězec pro je definován atribut a hodnota nastavení `value` atribut.|
| Certifikát | Volitelné. Určuje název, kryptografický otisk a algoritmu, který má být přidružena role certifikátu služby. Název certifikátu je definován na řetězec `name` atribut. Kryptografický otisk certifikátu je definován řetězec šestnáctkových čísel neobsahující žádné mezery pro `thumbprint` atribut. Šestnáctková čísla musí reprezentovat pomocí číslic a velká písmena alfanumerické znaky. Algoritmus certifikát se vyznačuje řetězec pro `thumbprintAlgorithm` atribut.|

## <a name="see-also"></a>Viz také
[Schéma konfigurace cloudové služby (klasické)](schema-cscfg-file.md)