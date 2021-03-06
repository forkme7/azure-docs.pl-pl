---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
Możesz użyć albo certyfikatu głównego wygenerowanego za pomocą rozwiązania przedsiębiorstwa (zalecane), albo wygenerować certyfikat z podpisem własnym. Po utworzeniu certyfikatu głównego dane certyfikatu publicznego (nie klucz prywatny) należy wyeksportować jako plik cer X.509 z kodowaniem Base-64 i przekazać dane certyfikatu publicznego na platformę Azure.

* **Certyfikat przedsiębiorstwa:** jeśli korzystasz z rozwiązania dla przedsiębiorstwa, możesz użyć istniejącego łańcucha certyfikatów. Uzyskaj plik cer dla certyfikatu głównego, którego chcesz użyć.
* **Certyfikat główny z podpisem własnym:** jeśli nie używasz rozwiązania z certyfikatem przedsiębiorstwa, musisz utworzyć certyfikat główny z podpisem własnym. Ważne jest, aby wykonać czynności opisane w jednym z poniższych artykułów dotyczących certyfikatu punkt-lokacja. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd łączenia klientów podczas nawiązywania połączenia. Możesz użyć programu Azure PowerShell, MakeCert lub protokołu OpenSSL. Czynności opisane w podanych artykułach umożliwiają wygenerowanie zgodnego certyfikatu:

  * [Instrukcje programu Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): w przypadku tych instrukcji do generowania certyfikatów wymagany jest system Windows 10 i program PowerShell. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
  * [Instrukcje MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): użyj programu MakeCert, jeśli nie masz dostępu do komputera z systemem Windows 10 w celu wygenerowania certyfikatów. Rozwiązanie MakeCert jest przestarzałe, ale można nadal używać go do generowania certyfikatów. Certyfikaty klienta generowane na podstawie certyfikatu głównego można instalować na dowolnym obsługiwanym kliencie typu punkt-lokacja.
