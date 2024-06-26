# Draft Release Workflow

## Über den Draft Release Workflow

Der Draft Release Workflow ist speziell entwickelt, um den Prozess der Erstellung von Release-Entwürfen zu automatisieren. Durch die Verwendung des Release Drafter wird basierend auf den neuesten Commits und Pull Requests ein formatierter Entwurf für das nächste Release erstellt. Dieser Workflow hilft dabei, den Überblick über wichtige Änderungen zu behalten und stellt sicher, dass alle relevanten Informationen im Changelog des nächsten Releases enthalten sind.

## Wie der Workflow funktioniert

Der Workflow wird automatisch ausgelöst, wenn Änderungen auf den `main`-Branch gepusht werden. Er besteht aus zwei Hauptteilen: dem Erstellen des Release-Entwurfs und dem Extrahieren der Versionsnummer aus dem Tag, die für nachfolgende Schritte verwendet wird.

## Schritte des Workflows

1. **Run Release Drafter:** Dieser Schritt nutzt die `release-drafter/release-drafter@v6` GitHub Action, um auf Basis der Änderungen seit dem letzten Release Release-Notizen zu generieren. Die Konfiguration für den Release Drafter wird aus der `release-drafter-config.yml` Datei gelesen.

2. **Remove leading 'v' from tag name and store in release_version:** Hier wird das führende 'v' aus dem Tag-Namen entfernt, um die reine Versionsnummer zu extrahieren und diese in einer Umgebungsvariable zu speichern. Dies erleichtert die weitere Verwendung in anderen Prozessen oder Workflows.

## YAML-Konfiguration des Workflows

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

## Anwendung des Workflows

Um diesen Workflow in deinem Projekt zu verwenden, füge die oben gezeigte YAML-Konfiguration und die notwendige `release-drafter-config.yml` Datei in das `.github/workflows`-Verzeichnis deines Repositories ein. Stelle sicher, dass der `GITHUB_TOKEN` in den Secrets deines Repositories gespeichert ist, um GitHub Actions den Zugriff auf das Repository zu ermöglichen.

## Empfohlenes Template für den Release Drafter

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
  default: patch
```

### Erklärung der Konfigurationselemente

- **`name-template` und `tag-template`:** Diese Vorlagen definieren, wie die Namen der Releases und Tags formatiert werden. `$RESOLVED_VERSION` wird durch die nächste Versionsnummer ersetzt, die durch die Analyse der Labels auf Pull Requests bestimmt wird.

- **`categories`:** Diese Sektion listet die verschiedenen Kategorien auf, die im Changelog erscheinen sollen. Jede Kategorie ist durch spezifische Labels gekennzeichnet, die auf Pull Requests angewendet werden. Änderungen, die mit diesen Labels versehen sind, werden automatisch in die entsprechende Kategorie einsortiert.

- **`template`:** Definiert das Layout des Changelogs. `$CHANGES` wird durch die formatierten Einträge für jede Änderung ersetzt, die den definierten Kategorien zugeordnet sind.

- **`version-resolver`:** Bestimmt, wie die Versionsnummer basierend auf den angehängten Labels erhöht wird. Es gibt Optionen für Major-, Minor- und Patch-Updates. Der `default` wird verwendet, wenn keine spezifischen Labels gefunden werden, die eine andere Art der Versionserhöhung bestimmen.

## Beschreibung der Workflow-Outputs

Nachdem der Release Drafter Workflow ausgeführt wurde, generiert er mehrere Outputs, die für weitere Schritte oder für Berichtszwecke verwendet werden können. Hier ist eine Beschreibung jedes Outputs, basierend auf dem vorgegebenen Beispiel:

### Output Details

- **Release ID:** Die eindeutige Identifikationsnummer des erstellten Releases. Diese Nummer ist spezifisch für GitHub und wird verwendet, um das Release in weiteren API-Aufrufen oder Aktionen zu referenzieren.
  - **Beispiel:** `152526037`

- **Release Name:** Der Name des Releases, wie er im GitHub Repository erscheint. Dieser Name ist typischerweise identisch mit dem Tag-Namen, kann aber zusätzliche Informationen enthalten, je nach Konfiguration.
  - **Beispiel:** `v0.5.1`

- **Release Tag Name:** Der Git-Tag, der mit dem Release verknüpft ist. Tags markieren spezifische Punkte in der Versionsgeschichte eines Repositories und sind häufig nach der Versionsnummer benannt.
  - **Beispiel:** `v0.5.1`

- **Release Body:** Der Inhalt der Release-Notizen, wie er vom Release Drafter generiert wird. Dies umfasst typischerweise eine Zusammenfassung der Änderungen, Verbesserungen und Fehlerbehebungen, die in das Release aufgenommen wurden, jeweils formatiert mit Details zu den Pull Requests und den beteiligten Beitragenden.
  - **Beispiel:**
    ```
    ## Änderungen
    * Contributors wieder auf Release Drafter-Standard gesetzt (#125) @kurmann

    ## Dokumentation

    * Verwende Release Drafter Standard-Template (#127) @kurmann
    ```

- **Release HTML URL:** Die URL zur Release-Seite auf GitHub, wo Benutzer Details zum Release einsehen, Downloads finden und weitere Aktionen ausführen können.
  - **Beispiel:** `https://github.com/kurmann/GitHub.Workflows/releases/tag/untagged-876ddb835dd6d3661a5d`

- **Release Version:** Die Versionsnummer des Releases, die verwendet wird, um die Reihenfolge und den Fortschritt der Software-Versionierung zu dokumentieren.
  - **Beispiel:** `0.5.1`

### Nutzung der Outputs

Diese Outputs können in verschiedenen Kontexten genutzt werden:

- **Automatisierung weiterer Schritte:** Verwenden Sie die Release ID und andere Outputs, um automatisierte Prozesse wie Deployments, Benachrichtigungen oder andere Workflows auszulösen.

- **Berichterstattung und Dokumentation:** Integrieren Sie die Informationen in Release-Berichte, automatische E-Mails oder andere Dokumentationsressourcen, um Stakeholder über neue Releases zu informieren.

- **Audit und Nachverfolgung:** Speichern Sie die Outputs für Auditzwecke oder als Teil eines Prozesses zur Nachverfolgung von Änderungen und Releases in Ihrer Software.

Diese Outputs bieten wertvolle Informationen, die dazu beitragen, den Software-Release-Prozess transparent, nachvollziehbar und automatisiert zu gestalten.

### Anwendung und Best Practices

- **Anpassung:** Sie können die Labels in den Kategorien und im `version-resolver` nach Bedarf anpassen, um sie an die spezifischen Anforderungen Ihres Projekts anzupassen.

- **Dokumentation:** Stellen Sie sicher, dass alle Teammitglieder verstehen, welche Labels sie ihren Pull Requests zuweisen müssen, um korrekte Release-Notizen zu gewährleisten.

- **Sicherheit:** Verwenden Sie sichere Secrets für den Zugriff auf `GITHUB_TOKEN`, um die Aktionen des Release Drafter auszuführen.

Mit dieser Konfiguration können Sie einen effizienten und automatisierten Prozess für die Erstellung Ihrer Release-Notizen implementieren, der Ihnen hilft, den Überblick über wichtige Änderungen zu bewahren und die Kommunikation mit Ihren Stakeholdern zu verbessern.
