# GitHub Workflows Repository

## Über dieses Repository

Dieses Repository dient als zentrale Wissensdatenbank für nützliche GitHub Workflows, die speziell entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Anleitung zur Nutzung der Workflows

Um einen Workflow aus diesem Repository zu verwenden, kopiere die gewünschte `.yml`-Datei und etwaige Konfigurationsdateien in das `.github/workflows`-Verzeichnis deines eigenen Repositories. Passe die Konfiguration bei Bedarf an die spezifischen Anforderungen deines Projekts an.

## Verfügbare Workflows

### Release Drafter

Der Release Drafter Workflow automatisiert das Erstellen von Release-Entwürfen, indem er Änderungen, die durch Pull Requests eingeführt werden, aufzeichnet und in einem formatierten Release-Dokument zusammenfasst.

#### Wie man den Workflow manuell integriert

**Schritte zur manuellen Integration des Release Drafter in dein Projekt:**

1. Kopiere die `release-drafter.yml` und die `release-drafter-config.yml` aus diesem Repository in das `.github/workflows`-Verzeichnis deines eigenen Repositories.
2. Passe die `release-drafter-config.yml` bei Bedarf an, um sie an die spezifischen Anforderungen deines Projekts anzupassen.
3. Füge den Workflow in deine CI/CD-Pipeline ein, indem du folgenden Job in deine GitHub Actions Workflow-Datei einfügst:

```yaml
jobs:
  release_draft:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Release Drafter
        uses: ./.github/workflows/release-drafter.yml
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

#### Funktionalität des Workflows

Dieser Workflow wird manuell in dein Repository integriert und kann nach Bedarf angepasst werden. Er wird automatisch ausgelöst, wenn Pull Requests in den `main` Branch gemerged werden, und erstellt einen Release-Entwurf basierend auf den Änderungen, die in diesen Pull Requests vorgenommen wurden.

## Warum der manuelle Kopierweg beim Release Drafter?

Der manuelle Kopierweg ermöglicht es jedem Projekt, eigene, angepasste Versionen der Workflow- und Konfigurationsdateien zu pflegen. Dies bietet Flexibilität und Kontrolle, indem es jedem Team ermöglicht wird, ihre Tools direkt und unabhängig zu verwalten, ohne von zentralen Änderungen abhängig zu sein.

## Zukünftige Pläne

Weitere Workflows werden kontinuierlich hinzugefügt und dokumentiert, um unterschiedliche Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an uns.
