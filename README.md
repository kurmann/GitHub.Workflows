# GitHub Workflows

## Archivierung

### Dieses Repository wurde in das Monorepo "kurmann/videoschnitt" migriert

**Hinweis:** Dieses Repository wird nicht mehr aktiv weiterentwickelt und ist archiviert. Die Entwicklung wird nun im Monorepo [kurmann/videoschnitt](https://github.com/kurmann/videoschnitt) fortgeführt.

---

## Über dieses Repository

Dieses Repository dient als zentrale Wissensdatenbank für nützliche GitHub Workflows, die speziell entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Anleitung zur Nutzung der Workflows

Um einen Workflow aus diesem Repository zu verwenden, kopiere den gewünschten YAML-Inhalt in der Ziel-Workflow-Datei im `.github/workflows`-Verzeichnis deines eigenen Repositories. Passe die Konfiguration bei Bedarf an die spezifischen Anforderungen deines Projekts an.

## Verfügbare Workflows

### Draft Release Workflow

Der Draft Release Workflow automatisiert das Erstellen von Release-Entwürfen basierend auf den neuesten Commits und Pull Requests. Für eine detaillierte Beschreibung siehe [Draft Release Workflow](docs/draft_release.md).

### Publish NuGet Package on Release

Dieser Workflow ermöglicht die automatische Veröffentlichung von NuGet-Paketen, sobald ein Release auf GitHub publiziert wird. Für eine detaillierte Beschreibung siehe [Publish NuGet Package Workflow](docs/publish_package.md).

### Get Latest Release Info

Dieser Workflow liest Informationen über das zuletzt veröffentlichte Release eines Repositories aus. Für eine detaillierte Beschreibung siehe [Get Latest Release Info Workflow](docs/get_latest_release_info.md).

## Zukünftige Pläne

Weitere Workflows werden kontinuierlich hinzugefügt und dokumentiert, um unterschiedliche Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an mich.
