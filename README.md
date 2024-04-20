# GitHub Workflows

## Über dieses Repository

Dieses Repository dient als zentrale Sammelstelle für wiederverwendbare GitHub Workflows, die entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von CI/CD-Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Verfügbare Workflows

### Release Drafter

Der Release Drafter Workflow automatisiert das Erstellen von Release-Entwürfen, indem er Änderungen, die durch Pull Requests eingeführt werden, aufzeichnet und in einem formatierten Release-Dokument zusammenfasst.

#### Wie man den Workflow verwendet

Um den `release-drafter` Workflow in deinem Projekt zu verwenden, füge folgenden Befehl in die Workflow-Datei deines eigenen Repositories ein:

```yaml
jobs:
  release_draft:
    uses: kurmann/GitHub.Workflows/.github/workflows/release-drafter.yml@main
    secrets:
      github_token: ${{ secrets.GITHUB_TOKEN }}
```

**Geheimnisse:**
- `github_token`: Ein GitHub-Token, das erforderlich ist, um auf Repository-Daten zuzugreifen, die für die Erstellung von Release-Entwürfen benötigt werden.

#### Funktionalität des Workflows

Dieser Workflow nutzt eine zentral verwaltete Konfigurationsdatei im `kurmann` Repository, um festzulegen, wie die Release Notes formatiert und verwaltet werden sollen. Der Workflow wird automatisch ausgelöst, wenn Pull Requests in den `main` Branch gemerged werden, und erstellt einen Release-Entwurf basierend auf den Änderungen, die in diesen Pull Requests vorgenommen wurden.

## Zukünftige Workflows

Weitere wiederverwendbare Workflows werden hinzugefügt, um verschiedene Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen. Jeder Workflow wird vollständig dokumentiert sein, um seine Verwendung und Funktionalität zu erklären.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an mich.
