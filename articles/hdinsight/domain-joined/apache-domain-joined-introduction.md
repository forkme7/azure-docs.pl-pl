---
title: Azure HDInsight — klastry usługi HDInsight przyłączonych do domeny —
description: Dowiedz się...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 6225bd824e3bcff24b84c79f39ce209f16caafd8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Wprowadzenie do zabezpieczeń Hadoop z klastrami HDInsight przyłączonych do domeny

Do tej pory usługa Azure HDInsight obsługiwała tylko jednego użytkownika z uprawnieniami lokalnego administratora. Bardzo dobrze funkcjonowało to dla mniejszych zespołów aplikacji lub działów. Jako Hadoop oparte na obciążeń zdobytych więcej popularne sektora przedsiębiorstwa, potrzebę funkcji klasy przedsiębiorstwa, takich jak uwierzytelnianie usługi active directory na podstawie, obsługa wielu użytkowników i kontroli dostępu opartej na rolach stał się coraz bardziej ważne. Za pomocą przyłączonych do domeny klastrów usługi HDInsight można utworzyć klaster usługi HDInsight przyłączony do domeny usługi Active Directory i skonfigurować listę pracowników przedsiębiorstwa, którzy mogą uwierzytelniać się za pomocą usługi Azure Active Directory podczas logowania się do klastra usługi HDInsight. Żadna osoba spoza przedsiębiorstwa nie może zalogować się lub uzyskać dostępu do klastra usługi HDInsight. Administratorzy przedsiębiorstwa można skonfigurować kontroli dostępu opartej na rolach dla gałęzi zabezpieczeń przy użyciu [zakres Apache](http://hortonworks.com/apache/ranger/), w związku z tym ograniczanie dostępu do danych tylko jako wymagane. Poza tym administrator może przeprowadzać inspekcje dostępu do danych przez pracowników oraz wszystkich zmian w zasadach kontroli dostępu, osiągając w ten sposób wysoki poziom nadzoru nad zasobami firmy.

> [!NOTE]
> Nowe funkcje opisane w tym artykule są dostępne tylko na następujące typy klastrów: Hadoop, Spark i interaktywne zapytania. Innych obciążeń, takich jak bazy danych HBase, Storm i Kafka, będą włączone w przyszłych wersjach.

> [!IMPORTANT]
> Oozie nie jest włączona w usłudze HDInsight z przyłączonych do domeny.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="benefits"></a>Korzyści
Enterprise zabezpieczeń zawiera cztery główne słupków — granicznej zabezpieczeń, uwierzytelniania, autoryzacji i szyfrowania.

![Filary korzyści z zastosowania przyłączonych do domeny klastrów usługi HDInsight](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Zabezpieczenia brzegowe
Zabezpieczenia brzegowe w usłudze HDInsight są realizowane za pomocą sieci wirtualnych i usługi bramy. Obecnie administrator przedsiębiorstwa może utworzyć klaster usługi HDInsight wewnątrz sieci wirtualnej i wykorzystać grupy zabezpieczeń sieci (reguły zapory dla ruchu przychodzącego lub wychodzącego) do ograniczenia dostępu do sieci wirtualnej. Tylko adresy IP określone w regułach zapory dla ruchu przychodzącego będą mogły nawiązać połączenia z klastrem usługi HDInsight, zapewniając w ten sposób działanie zabezpieczeń brzegowych. Inna warstwa zabezpieczeń brzegowych jest realizowana przy użyciu usługi bramy. Brama jest usługi, która działa jako pierwszą linię obrony dla wszystkie żądania przychodzące do klastra usługi HDInsight. Akceptuje żądanie, sprawdza jego poprawność, a następnie zezwala na przekazanie go do innych węzłów w klastrze, zapewniając w ten sposób zabezpieczenia brzegowe dla innych węzłów nazw i danych w klastrze.

### <a name="authentication"></a>Authentication
Administrator przedsiębiorstwa może utworzyć klaster HDInsight przyłączonych do domeny w [sieci wirtualnej](https://azure.microsoft.com/services/virtual-network/). Węzły klastra usługi HDInsight zostaną dołączone do domeny zarządzanej przez przedsiębiorstwo. Jest to realizowane poprzez użycie [usług Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Wszystkie węzły w klastrze są przyłączone do domeny, którą zarządza przedsiębiorstwo. W przypadku takiej konfiguracji pracownicy przedsiębiorstwa mogą logować się do węzłów klastra przy użyciu poświadczeń domeny. Mogą również wykorzystać swoich poświadczeń domeny do uwierzytelniania za pomocą innych zatwierdzonych punktów końcowych, takie jak Hue, widoki Ambari ODBC, JDBC, programu PowerShell i interfejsów API REST na interakcję z klastrem. Administrator ma pełną kontrolę nad ograniczaniem liczby użytkowników wchodzących w interakcję z klastrem za pośrednictwem tych punktów końcowych.

### <a name="authorization"></a>Autoryzacja
Najlepsze rozwiązanie stosowane przez większość przedsiębiorstw polega na tym, że nie każdy pracownik ma dostęp do wszystkich zasobów organizacji. Podobnie w tym wydaniu Administrator można zdefiniować zasady kontroli dostępu opartej na rolach dla zasobów klastra. Na przykład administrator może skonfigurować środowisko [Apache Ranger](http://hortonworks.com/apache/ranger/) do ustawiania zasad kontroli dostępu dla usługi Hive. Ta funkcja zapewnia, że pracownicy będą mieli dostęp tylko do tych danych, które są im potrzebne do skutecznego wykonywania swoich zadań. Także dostęp do klastra przy użyciu protokołu SSH jest ograniczony tylko do administratora.

### <a name="auditing"></a>Inspekcja
Wraz z ochroną zasobów klastra usługi HDInsight przed dostępem ze strony nieautoryzowanych użytkowników oraz zabezpieczaniem danych niezbędna jest inspekcja wszystkich przypadków dostępu do zasobów klastra i danych w celu śledzenia prób nieautoryzowanego lub niezamierzonego dostępu do zasobów. Administrator można wyświetlać i zgłoś dostęp do danych i zasobów klastra usługi HDInsight. Administrator może także przeglądać i raportować wszystkie zmiany wprowadzane do zasad kontroli dostępu poprzez punkty końcowe obsługiwane przez środowisko Apache Ranger. Przyłączony do domeny klaster usługi HDInsight używa znanego interfejsu użytkownika środowiska Apache Ranger do wyszukiwania dzienników inspekcji. W wewnętrznej bazie danych do przechowywania i wyszukiwania dzienników środowisko Ranger używa rozwiązania [Apache Solr](http://hortonworks.com/apache/solr/).

### <a name="encryption"></a>Szyfrowanie
Ochrona danych ma duże znaczenie dla spełniania wymagań organizacyjnych w zakresie zgodności z przepisami i zabezpieczeń, dlatego poza ograniczaniem dostępu do danych przez nieautoryzowanych pracowników powinna uwzględniać również ich szyfrowanie. Oba rodzaje magazynów danych dla klastrów usługi HDInsight, czyli magazyn usługi Azure Storage Blob i magazyn usługi Azure Data Lake, obsługują przezroczyste [szyfrowanie danych](../../storage/common/storage-service-encryption.md) po stronie serwera w odniesieniu do danych magazynowanych. Zabezpieczanie klastrów usługi HDInsight funkcjonuje bezproblemowo przy użyciu funkcji szyfrowania danych magazynowanych po stronie serwera.

## <a name="next-steps"></a>Kolejne kroki
* Aby skonfigurować przyłączony do domeny klaster usługi HDInsight, zobacz [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Konfigurowanie przyłączonych do domeny klastrów usługi HDInsight).
* Aby zarządzanie klastrem HDInsight przyłączonych do domeny, zobacz [przyłączonych do domeny zarządzania w usłudze hdinsight](apache-domain-joined-manage.md).
* Aby znaleźć informacje na temat konfigurowania zasad Hive i uruchamiania kwerend Hive, zobacz [Konfigurowanie zasad usługi Hive dla przyłączonych do domeny klastrów usługi HDInsight](apache-domain-joined-run-hive.md).
* Do uruchamiania zapytań Hive przy użyciu protokołu SSH w klastrach HDInsight przyłączonych do domeny, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Aby użycie VSCode połączenia z klastrem przyłączone do domeny, zobacz [Link do domeny przyłączone do klastra z VSCode](../hdinsight-for-vscode.md#linkcluster).
* Aby użycie IntelliJ połączenia z klastrem przyłączone do domeny, zobacz [Link do domeny przyłączone do klastra z rozwiązaniem IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#linkcluster).
* Połącz z klastrem przyłączone do domeny za pomocą programu Eclipse, dla [Link do domeny przyłączone do klastra z Eclipse](../spark/apache-spark-eclipse-tool-plugin.md#linkcluster).
