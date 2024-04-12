# GitHub Workflows

## Über dieses Repository

Dieses Repository dient als zentrale Sammelstelle für Reusable Workflows, die in verschiedenen Projekten wiederverwendet werden können. Es zielt darauf ab, die Wiederverwendbarkeit von Code zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung der CI/CD-Pipelines zu vereinfachen.

## Verwendung der Workflows

Um einen der Reusable Workflows in deinem Projekt zu verwenden, füge den entsprechenden `uses`-Befehl in die Workflow-Datei deines eigenen Repositories ein. Hierbei solltest du darauf achten, dass du auf eine spezifische Version des Workflows verweist, um Stabilität und Kompatibilität zu gewährleisten.

### Beispiel

Um einen Workflow zu nutzen, der sich in diesem Repository befindet, kannst du die folgende Syntax in deinem eigenen Workflow verwenden:

```yaml
jobs:
  my-job:
    uses: kurmann/reusable-workflows/.github/workflows/mein-workflow.yml@v1.0.0
    with:
      some-input: value
    secrets:
      PERSONAL_ACCESS_TOKEN: ${{ secrets.PERSONAL_ACCESS_TOKEN }}
```

Ersetze `kurmann/reusable-workflows` mit dem tatsächlichen Namen dieses Repositories und `mein-workflow.yml` mit dem Namen der Workflow-Datei, die du verwenden möchtest.

## Beitrag zu diesem Repository

### Workflows hinzufügen

Um einen neuen Reusable Workflow hinzuzufügen:

1. Erstelle eine neue YAML-Datei im Verzeichnis `.github/workflows` des Repositories.
2. Definiere deinen Workflow gemäß den [GitHub Actions Spezifikationen](https://docs.github.com/en/actions).
3. Stelle sicher, dass dein Workflow gut dokumentiert ist, insbesondere was Inputs, Outputs und Secrets betrifft.
4. Eröffne einen Pull Request gegen das Hauptrepository, damit der Workflow überprüft und integriert werden kann.

### Best Practices

- Verwende klare und beschreibende Namen für Workflows und deren Dateien.
- Dokumentiere jede Action und jeden Schritt innerhalb des Workflows ausführlich.
- Halte dich an die allgemeinen Sicherheitspraktiken, insbesondere bei der Handhabung von Secrets.
- Teste Workflows gründlich, bevor du sie zum Repository hinzufügst.

## Versionierung

Ich verwende Semantic Versioning für die Versionierung der Workflows. Dies gewährleistet, dass Änderungen systematisch und vorhersehbar sind und hilft Nutzern, abwärtskompatible Versionen leicht zu identifizieren.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die [Issues-Seite](https://github.com/mein-username/reusable-workflows/issues) an mich.
