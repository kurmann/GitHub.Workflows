# Get Latest Release Info

Dieser Workflow zeigt, wie man Informationen über das zuletzt veröffentlichte Release eines Repositories ausliest. Er nutzt die aktualisierten Methoden für die Definition von Outputs zwischen Jobs gemäß der [neusten GitHub-Dokumentation](https://docs.github.com/en/actions/using-jobs/defining-outputs-for-jobs).

## Trigger

Der Workflow wird durch das `workflow_dispatch` Ereignis ausgelöst, was bedeutet, dass er manuell über die GitHub UI gestartet werden kann.

## Jobs und Schritte

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

## Beispieloutput

```plaintext
Run echo "Release Version: 0.4.0"
Release Version: 0.4.0
Release Tag Name: v0.4.0
Release URL: https://github.com/kurmann/GitHub.Workflows/releases/tag/v0.4.0
```

Dieser Workflow demonstriert eine effektive Nutzung der GitHub Actions, um dynamisch Daten zwischen Jobs zu übertragen und bietet eine solide Basis für Workflows, die abhängige Daten benötigen.
