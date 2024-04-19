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

Natürlich, hier ist eine aktualisierte Version der Dokumentation für Ihren `README.md`, die die Anwendung des Release Drafters und die Lösung des Problems mit der Versionsnummer widerspiegelt:

## Automatisierte Release Notes mit Release Drafter

### Zweck des Release Drafters

Der Release Drafter ist ein Werkzeug, das dazu dient, den Prozess der Erstellung und Pflege von Release Notes zu automatisieren. Es verfolgt Änderungen, die durch Pull Requests im Repository eingeführt werden, und generiert einen zusammengefassten Entwurf für die Release Notes.

### Release Drafter Konfiguration

Die Konfiguration für den Release Drafter erfolgt über eine YAML-Datei namens `.github/release-drafter.yml`. Diese Datei definiert das Layout und Verhalten der automatisierten Release Notes. 

```yaml
# Beispiel für eine `release-drafter.yml` Konfiguration:
name-template: 'v$NEXT_PATCH_VERSION'
tag-template: 'v$NEXT_PATCH_VERSION'
categories:
  - title: 'Features'
    labels:
      - 'enhancement'
  - title: 'Bug Fixes'
    labels:
      - 'bug'
  - title: 'Dokumentation'
    labels:
      - 'documentation'
change-template: '- $TITLE @$AUTHOR (#$NUMBER)'
template: |
  ## Änderungen
  $CHANGES
```

### Workflow-Anpassungen für die Versionierung

Der Workflow wurde so angepasst, dass er die Standard-GitHub-Labels für Features (`enhancement`), Bug Fixes (`bug`) und Dokumentationsupdates (`documentation`) verwendet. Der Release Drafter wendet automatisch eine neue Versionsnummer an, basierend auf den vorgenommenen Änderungen:

- `bug` und `documentation` führen zu einer Patch-Versionserhöhung.
- `enhancement` führt zu einer Minor-Versionserhöhung.

### Versionsnummernvergabe

Die Versionserhöhung erfolgt automatisch durch den Release Drafter, wobei die Minor-Version erhöht wird, wenn ein Feature mit dem Label `enhancement` hinzugefügt wird. Wenn die automatische Versionserhöhung nicht wie erwartet funktioniert, sollten die angewandten Labels und ihre Zuordnung in der Konfigurationsdatei überprüft werden.

### Manuelle Überprüfung und Release

Vor der Veröffentlichung eines Releases wird der Release-Entwurf manuell überprüft. Major-Versionserhöhungen werden bewusst manuell gehandhabt, um eine klare Trennung und spezielle Aufmerksamkeit für solche signifikanten Releases zu gewährleisten. Die Entscheidung über die Freigabe und das Tagging eines Major-Releases liegt beim Maintainer.

### Beobachtung und Anpassung

Sollten Inkonsistenzen in der automatischen Versionserhöhung beobachtet werden, wird dies weiter beobachtet und bei Bedarf werden Anpassungen an der Konfiguration oder dem Workflow vorgenommen, um sicherzustellen, dass der Release Drafter korrekt funktioniert.

### Fazit

Durch die Automatisierung mit dem Release Drafter bleibt das Repository organisiert und die Release Notes reflektieren stets den aktuellen Stand der Entwicklungen. Dies erleichtert die Versionierung und Veröffentlichung von neuen Releases erheblich.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die [Issues-Seite](https://github.com/mein-username/reusable-workflows/issues) an mich.

## Änderungsverlauf

Änderungen an den Workflows werden im [Änderungsverlauf](docs/CHANGELOG.md).