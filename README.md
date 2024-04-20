
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

## Zukünftige Workflows

Weitere wiederverwendbare Workflows werden hinzugefügt, um verschiedene Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen. Jeder Workflow wird vollständig dokumentiert sein, um seine Verwendung und Funktionalität zu erklären.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an mich.
