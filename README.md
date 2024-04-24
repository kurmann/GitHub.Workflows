# GitHub Workflows Repository

## Über dieses Repository

Dieses Repository dient als zentrale Wissensdatenbank für nützliche GitHub Workflows, die speziell entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Anleitung zur Nutzung der Workflows

Um einen Workflow aus diesem Repository zu verwenden, kopiere die gewünschte `.yml`-Datei und etwaige Konfigurationsdateien in das `.github/workflows`-Verzeichnis deines eigenen Repositories. Passe die Konfiguration bei Bedarf an die spezifischen Anforderungen deines Projekts an.

## Verfügbare Workflows

## Draft Release Workflow

### Über den Draft Release Workflow

Der Draft Release Workflow ist speziell entwickelt, um den Prozess der Erstellung von Release-Entwürfen zu automatisieren. Durch die Verwendung des Release Drafter wird basierend auf den neuesten Commits und Pull Requests ein formatierter Entwurf für das nächste Release erstellt. Dieser Workflow hilft dabei, den Überblick über wichtige Änderungen zu behalten und stellt sicher, dass alle relevanten Informationen im Changelog des nächsten Releases enthalten sind.

### Wie der Workflow funktioniert

Der Workflow wird automatisch ausgelöst, wenn Änderungen auf den `main`-Branch gepusht werden. Er besteht aus zwei Hauptteilen: dem Erstellen des Release-Entwurfs und dem Extrahieren der Versionsnummer aus dem Tag, die für nachfolgende Schritte verwendet wird.

### Schritte des Workflows

1. **Run Release Drafter:** Dieser Schritt nutzt die `release-drafter/release-drafter@v6` GitHub Action, um auf Basis der Änderungen seit dem letzten Release Release-Notizen zu generieren. Die Konfiguration für den Release Drafter wird aus der `release-drafter-config.yml` Datei gelesen.

2. **Remove leading 'v' from tag name and store in release_version:** Hier wird das führende 'v' aus dem Tag-Namen entfernt, um die reine Versionsnummer zu extrahieren und diese in einer Umgebungsvariable zu speichern. Dies erleichtert die weitere Verwendung in anderen Prozessen oder Workflows.

### YAML-Konfiguration des Workflows

```yaml
name: Draft Release
on:
  push:
    branches:
      - main

jobs:
  draft_release:
    runs-on: ubuntu-latest
    steps:
      - name: Run Release Drafter
        uses: release-drafter/release-drafter@v6
        with:
          config-name: 'release-drafter-config.yml'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Remove leading 'v' from tag name and store in release_version
        run: |
          TAG_NAME="${{ steps.release_drafter.outputs.tag_name }}"
          echo "Tag name: $TAG_NAME"
          VERSION="${TAG_NAME#v}"
          echo "VERSION=$VERSION" >> $GITHUB_ENV

    outputs:
      release_id: ${{ steps.release_drafter.outputs.id }}
      release_name: ${{ steps.release_drafter.outputs.name }}
      release_tag_name: ${{ steps.release_drafter.outputs.tag_name }}
      release_body: ${{ steps.release_drafter.outputs.body }}
      release_html_url: ${{ steps.release_drafter.outputs.html_url }}
      release_upload_url: ${{ steps.release_drafter.outputs.upload_url }}
      release_version: ${{ env.VERSION }}

  print_release_info:
    needs: draft_release
    runs-on: ubuntu-latest
    steps:
      - name: Print Release Drafter outputs
        run: |
          echo "Release ID: ${{ needs.draft_release.outputs.release_id }}"
          echo "Release Name: ${{ needs.draft_release.outputs.release_name }}"
          echo "Release Tag Name: ${{ needs.draft_release.outputs.release_tag_name }}"
          echo "Release Body: ${{ needs.draft_release.outputs.release_body }}"
          echo "Release HTML URL: ${{ needs.draft_release.outputs.release_html_url }}"
          echo "Release Version: ${{ needs.draft_release.outputs.release_version }}"
```

### Anwendung des Workflows

Um diesen Workflow in deinem Projekt zu verwenden, füge die oben gezeigte YAML-Konfiguration und die notwendige `release-drafter-config.yml` Datei in das `.github/workflows`-Verzeichnis deines Repositories ein. Stelle sicher, dass der `GITHUB_TOKEN` in den Secrets deines Repositories gespeichert ist, um GitHub Actions den Zugriff auf das Repository zu ermöglichen.

### Empfohlenes Template für den Release Drafter

Der Release Drafter ist ein Tool, das dazu dient, den Prozess der Erstellung von Release-Notizen zu automatisieren. Hier ist ein empfohlenes Template für die Konfiguration des Release Drafter, das Sie in Ihrer `release-drafter-config.yml` Datei verwenden können:

```yaml
# Konfigurationsdatei für das Release-Drafter-Tool.
# release-drafter-config.yml

# Definiert die Vorlage für den Namen des Releases.
name-template: 'v$RESOLVED_VERSION' # Automatisch erhöhte Versionsnummer

# Definiert die Vorlage für den Namen des Release-Tags.
tag-template: 'v$RESOLVED_VERSION' # Automatisch erhöhte Versionsnummer

# Kategorien für das Changelog, identifiziert durch Labels auf Pull Requests.
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

# Layout des Changelogs, formatiert nach den Änderungen in den Pull Requests.
template: |
  ## Änderungen
  $CHANGES

# Version-Resolver bestimmt die Art der Versionserhöhung basierend auf den PR Labels.
version-resolver:
  major:
    labels:
      - 'breaking'
  minor:
    labels:
      - 'enhancement'
  patch:
    labels:
      - 'bug'
      - 'documentation'
  default: minor
```

#### Erklärung der Konfigurationselemente

- **`name-template` und `tag-template`:** Diese Vorlagen definieren, wie die Namen der Releases und Tags formatiert werden. `$RESOLVED_VERSION` wird durch die nächste Versionsnummer ersetzt, die durch die Analyse der Labels auf Pull Requests bestimmt wird.

- **`categories`:** Diese Sektion listet die verschiedenen Kategorien auf, die im Changelog erscheinen sollen. Jede Kategorie ist durch spezifische Labels gekennzeichnet, die auf Pull Requests angewendet werden. Änderungen, die mit diesen Labels versehen sind, werden automatisch in die entsprechende Kategorie einsortiert.

- **`template`:** Definiert das Layout des Changelogs. `$CHANGES` wird durch die formatierten Einträge für jede Änderung ersetzt, die den definierten Kategorien zugeordnet sind.

- **`version-resolver`:** Bestimmt, wie die Versionsnummer basierend auf den angehängten Labels erhöht wird. Es gibt Optionen für Major-, Minor- und Patch-Updates. Der `default` wird verwendet, wenn keine spezifischen Labels gefunden werden, die eine andere Art der Versionserhöhung bestimmen.

#### Anwendung und Best Practices

- **Anpassung:** Sie können die Labels in den Kategorien und im `version-resolver` nach Bedarf anpassen, um sie an die spezifischen Anforderungen Ihres Projekts anzupassen.

- **Dokumentation:** Stellen Sie sicher, dass alle Teammitglieder verstehen, welche Labels sie ihren Pull Requests zuweisen müssen, um korrekte Release-Notizen zu gewährleisten.

- **Sicherheit:** Verwenden Sie sichere Secrets für den Zugriff auf `GITHUB_TOKEN`, um die Aktionen des Release Drafter auszuführen.

Mit dieser Konfiguration können Sie einen effizienten und automatisierten Prozess für die Erstellung Ihrer Release-Notizen implementieren, der Ihnen hilft, den Überblick über wichtige Änderungen zu bewahren und die Kommunikation mit Ihren Stakeholdern zu verbessern.

### Get Latest Release Info

Dieser Workflow zeigt, wie man Informationen über das zuletzt veröffentlichte Release eines Repositories ausliest. Er nutzt die aktualisierten Methoden für die Definition von Outputs zwischen Jobs gemäß der [neusten GitHub-Dokumentation](https://docs.github.com/en/actions/using-jobs/defining-outputs-for-jobs).

#### Trigger

Der Workflow wird durch das `workflow_dispatch` Ereignis ausgelöst, was bedeutet, dass er manuell über die GitHub UI gestartet werden kann.

#### Jobs und Schritte

**Job 1: `read_and_print_release_data`**

1. **Checkout Code**: Der Workflow checkt den Code aus, damit Tools wie die GitHub CLI verwendet werden können.
   
2. **Get latest release**: Dieser Schritt verwendet die GitHub CLI, um die neuesten Release-Informationen abzurufen. Er verwendet `jq` zum Parsen der JSON-Antwort und speichert die relevanten Daten (Versionsnummer, Tag-Name und URL des Releases) in den Workflow-Outputs.

   ```yaml
   steps:
     - name: Checkout code
       uses: actions/checkout@v4

     - name: Get latest release
       id: get_release
       run: |
         RELEASE_JSON=$(gh release view --json tagName,name,url)
         RELEASE_TAG_NAME=$(echo "$RELEASE_JSON" | jq -r '.tagName')
         RELEASE_NAME=$(echo "$RELEASE_JSON" | jq -r '.name')
         RELEASE_URL=$(echo "$RELEASE_JSON" | jq -r '.url')
         RELEASE_VERSION="${RELEASE_TAG_NAME#"v"}"
         echo "release_version=$RELEASE_VERSION" >> "$GITHUB_OUTPUT"
         echo "release_tag_name=$RELEASE_TAG_NAME" >> "$GITHUB_OUTPUT"
         echo "release_url=$RELEASE_URL" >> "$GITHUB_OUTPUT"
       env:
         GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
   outputs:
     release_version: ${{ steps.get_release.outputs.release_version }}
     release_tag_name: ${{ steps.get_release.outputs.release_tag_name }}
     release_url: ${{ steps.get_release.outputs.release_url }}
   ```

**Job 2: `second_job`**

Dieser Job ist abhängig von `read_and_print_release_data` und druckt die ausgelesenen Informationen des vorherigen Jobs.

   ```yaml
   - name: Print outputs
     run: |
       echo "Release Version: ${{ needs.read_and_print_release_data.outputs.release_version }}"
       echo "Release Tag Name: ${{ needs.read_and_print_release_data.outputs.release_tag_name }}"
       echo "Release URL: ${{ needs.read_and_print_release_data.outputs.release_url }}"
   ```

#### Beispieloutput

```plaintext
Run echo "Release Version: 0.4.0"
Release Version: 0.4.0
Release Tag Name: v0.4.0
Release URL: https://github.com/kurmann/GitHub.Workflows/releases/tag/v0.4.0
```

Dieser Workflow demonstriert eine effektive Nutzung der GitHub Actions, um dynamisch Daten zwischen Jobs zu übertragen und bietet eine solide Basis für Workflows, die abhängige Daten benötigen.

## Zukünftige Pläne

Weitere Workflows werden kontinuierlich hinzugefügt und dokumentiert, um unterschiedliche Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an uns.
