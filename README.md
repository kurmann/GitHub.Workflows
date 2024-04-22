
# GitHub Workflows

## Über dieses Repository

Dieses Repository dient als zentrale Sammelstelle für wiederverwendbare GitHub Workflows, die entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von CI/CD-Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Verfügbare Workflows

### Release Drafter

Der Release Drafter Workflow automatisiert das Erstellen von Release-Entwürfen, indem er Änderungen, die durch Pull Requests eingeführt werden, aufzeichnet und in einem formatierten Release-Dokument zusammenfasst.

#### Wie man den Workflow manuell integriert
 
**Schritte zur manuellen Integration des Release Drafter in dein Projekt:**

1. Kopiere die `release-drafter.yml` und die `releasevdrafter-config.yml` aus diesem Repository in das `.github/workflows`-Verzeichnis deines eigenen Repositories.
2. Passe die `release-drafter-config.yml` bei Bedarf an, um sie an die spezifischen Anforderungen deines Projekts anzupassen.
3. Füge den Workflow in deine CI/CD-Pipeline ein, indem du folgenden Job in deine GitHub Actions Workflow-Datei einfügst:

```yaml
jobs:
  release_draft:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Release Drafter
        uses: ./.github/workflows/release-drafter.yml
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Wichtige Hinweise:**

- Stelle sicher, dass du die `GITHUB_TOKEN` Secret in deinem Repository konfiguriert hast, um GitHub Aktionen zu ermöglichen, auf das Repository zuzugreifen.
- Überprüfe und aktualisiere die Pfade in der `release-drafter.yml`, falls notwendig, um sicherzustellen, dass sie korrekt auf die `release-drafter-config.yml` verweisen.

#### Funktionalität des Workflows

Dieser Workflow wird manuell in dein Repository integriert und kann nach Bedarf angepasst werden. Er wird automatisch ausgelöst, wenn Pull Requests in den `main` Branch gemerged werden, und erstellt einen Release-Entwurf basierend auf den Änderungen, die in diesen Pull Requests vorgenommen wurden.

Natürlich, hier ist das gekürzte Teilkapitel mit der Dokumentation der spezifischen Berechtigungen für den Release Drafter:

### Berechtigungen für den Release Drafter

Beim Einsatz des Release Drafters in GitHub Actions ist es wichtig, die erforderlichen Berechtigungen präzise festzulegen, um sowohl die Funktionalität zu gewährleisten als auch die Sicherheit des Repositories zu wahren.

#### Notwendige Berechtigungen

Der Release Drafter benötigt spezifische Berechtigungen, die im YAML des Workflows definiert werden:

```yaml
permissions:
  contents: write  # Notwendig, um Releases zu erstellen und Tags zu schreiben.
  pull-requests: read  # Ermöglicht das Lesen von Informationen aus Pull Requests.
```

Mit `contents: write` kann der Release Drafter Release-Entwürfe erstellen und verwalten sowie Tags für neue Versionen anlegen. `pull-requests: read` erlaubt es, Details aus Pull Requests zu lesen, die für die Erstellung von Release-Notizen notwendig sind.

#### Vorgehensweise

Füge den `permissions`-Block direkt in deinen Release Drafter Workflow ein, um diese Berechtigungen zu setzen. Die Einstellungen auf Repository-Ebene für Workflow-Berechtigungen können auf „Read repository contents and packages permissions“ belassen werden, da die spezifischen Berechtigungen im YAML des Workflows die allgemeinen Einstellungen überschreiben.

### Warum der manuelle Kopierweg beim Release Drafter?

Bei der Entwicklung und Wartung von Software-Projekten ist die Konfiguration der verwendeten Tools entscheidend für die Effizienz und Konsistenz der Arbeitsabläufe. Der Release Drafter, ein Tool zur Automatisierung der Erstellung von Release-Notizen, erfordert eine spezifische Konfigurationsdatei (`release-drafter-config.yml`), um korrekt zu funktionieren. Diese Datei definiert, wie die Release-Notizen aufgebaut und welche Informationen inkludiert werden sollen.

#### Herausforderungen der zentralen Konfiguration

1. **Zugänglichkeit und Sichtbarkeit**: Wenn die Konfiguration zentral verwaltet wird, könnte es schwierig sein, sie an die spezifischen Bedürfnisse jedes einzelnen Projekts anzupassen, ohne Einfluss auf andere Projekte zu nehmen.
2. **Komplexität der Verwaltung**: Die Pflege einer zentralen Konfigurationsdatei, insbesondere in einem dynamischen Umfeld mit vielen Projekten, kann schnell unübersichtlich und schwer zu verwalten werden.
3. **Abhängigkeit und Flexibilität**: Zentrale Lösungen erhöhen die Abhängigkeit von einem zentralen Repository. Änderungen oder Updates erfordern Koordination und können Verzögerungen verursachen.

#### Vorteile des manuellen Kopierwegs

Durch die Entscheidung für den manuellen Kopierweg, bei dem jedes Projekt seine eigene Kopie der Workflow- und Konfigurationsdateien erhält, ergeben sich mehrere Vorteile:

- **Anpassungsfähigkeit**: Jedes Projekt kann seine eigene Version der Konfigurationsdatei anpassen, ohne andere Projekte zu beeinflussen.
- **Einfachheit**: Die Einrichtung ist geradlinig und erfordert keine komplexen Zugriffsrechte oder Abhängigkeiten von externen Quellen.
- **Kontrolle und Transparenz**: Projektteams haben die volle Kontrolle und Transparenz über die verwendeten Automatisierungstools und deren Konfiguration.

Diese Strategie maximiert die Flexibilität und reduziert die Komplexität für Teams, indem sie ihnen ermöglicht, ihre Tools direkt und unabhängig zu verwalten.

Natürlich! Hier ist ein Teilkapitel in Markdown, das das Vorgehen und die Benennung Ihrer CI-Workflows beschreibt und begründet. Dieses Kapitel kann als Teil der Dokumentation in Ihrem GitHub-Repository, das als zentrale Stelle für GitHub-Workflows dient, integriert werden:

## Empfehlungen für Continuous Integration Workflows

### Überblick

Continuous Integration (CI) ist ein fundamentaler Bestandteil der modernen Softwareentwicklung, der darauf abzielt, die Softwareentwicklung durch automatisierte Tools zu beschleunigen und gleichzeitig die Codequalität zu verbessern. CI hilft dabei, Fehler frühzeitig im Entwicklungsprozess zu erkennen und zu beheben, was zu einer zuverlässigeren Software führt.

### Workflow-Konfiguration

In unserem zentralen Repository für GitHub-Workflows empfehlen wir zwei spezifische Workflows für die Handhabung von CI-Prozessen:

1. **CI-Build-and-Package.yml**: Dieser Workflow wird auf jeden Push in `main` und alle Feature-Branches ausgelöst. Er ist verantwortlich für:
   - Automatisches Bauen des Codes
   - Durchführen von Tests
   - Paketierung des Codes ohne Veröffentlichung
   - Bereitstellung von Build-Artefakten für weitere Schritte oder Tests

   ```yaml
   jobs:
     build_and_package:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v2
         - name: Build
           run: make build
         - name: Test
           run: make test
         - name: Package
           run: make package
   ```

2. **CI-PR-Build-Publish.yml**: Dieser Workflow wird für Pull Requests gegen den `main` Branch ausgelöst. Zusätzlich zu den Aufgaben des CI-Build-and-Package Workflows übernimmt er:
   - Veröffentlichung der NuGet-Pakete
   - Erstellung eines Release Drafts, wenn der Pull Request erfolgreich gemerged wurde

   ```yaml
   jobs:
     build_publish_and_release:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v2
         - name: Build
           run: make build
         - name: Test
           run: make test
         - name: Package and Publish
           run: make publish
         - name: Create Release Draft
           run: make release
   ```

### Begründung der Workflow-Benennung

Die Benennung der Workflows **CI-Build-and-Package** und **CI-PR-Build-Publish** wurde gewählt, um ihre Funktionen klar zu kommunizieren:
- **Build-and-Package**: Konzentriert sich auf das Erstellen und Paketieren des Codes ohne dessen Veröffentlichung.
- **Build-Publish**: Erweitert den Prozess um die Veröffentlichung und Release-Erstellung.

Diese klare Trennung sorgt für eine bessere Verständlichkeit und erleichtert die Wartung der Workflows. Sie hilft neuen Entwicklern oder externen Teams, die mit dem Repository arbeiten, sofort zu verstehen, was jeder Workflow tut und wann er ausgelöst wird.

### Abschluss

Die Einrichtung spezifischer Workflows für unterschiedliche Zwecke ermöglicht es uns, die Prozesse umsichtig zu steuern und sicherzustellen, dass die Veröffentlichungen nach den höchsten Standards der Codequalität und Zuverlässigkeit erfolgen. Durch die zentrale Dokumentation und Verwaltung dieser Workflows schaffen wir eine konsistente und effiziente Entwicklungsumgebung.

## Zukünftige Workflows

Weitere wiederverwendbare Workflows werden hinzugefügt, um verschiedene Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen. Jeder Workflow wird vollständig dokumentiert sein, um seine Verwendung und Funktionalität zu erklären.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an mich.
