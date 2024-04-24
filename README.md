# GitHub Workflows Repository

## Über dieses Repository

Dieses Repository dient als zentrale Wissensdatenbank für nützliche GitHub Workflows, die speziell entwickelt wurden, um in verschiedenen Projekten unter dem `kurmann` GitHub-Account wiederverwendet zu werden. Das Hauptziel ist es, die Wiederverwendbarkeit von Prozessen zu maximieren, die Konsistenz über mehrere Projekte hinweg zu gewährleisten und die Wartung zu vereinfachen.

## Anleitung zur Nutzung der Workflows

Um einen Workflow aus diesem Repository zu verwenden, kopiere die gewünschte `.yml`-Datei und etwaige Konfigurationsdateien in das `.github/workflows`-Verzeichnis deines eigenen Repositories. Passe die Konfiguration bei Bedarf an die spezifischen Anforderungen deines Projekts an.

## Verfügbare Workflows






### Veröffentlichung eines NuGet-Pakets bei Release

Dieser Workflow zeigt, wie man ein NuGet-Paket automatisch veröffentlicht, wenn ein Release auf GitHub publiziert wird. Er ist ideal für Projekte, die ihre Software regelmäßig und automatisch als Paket bereitstellen möchten.

#### Über den NuGet-Paket-Veröffentlichungs-Workflow

Der NuGet-Paket-Veröffentlichungs-Workflow ist für Projekte gedacht, die ihre .NET-Softwarekomponenten effizient verpacken und verteilen möchten. Dieser Workflow ermöglicht es, auf ein veröffentlichtes Release zu reagieren und das entsprechende NuGet-Paket automatisch zu erstellen und zu publizieren.

#### Wie der Workflow funktioniert

Der Workflow wird durch das Event `release` ausgelöst, speziell wenn ein Release als `published` gekennzeichnet wird. Er kann auch manuell über die GitHub-Oberfläche gestartet werden, was durch das `workflow_dispatch` Event ermöglicht wird.

#### Schritte des Workflows

1. **Get latest release information:** Dieser Schritt sammelt Informationen über das neueste Release, darunter den Tag-Namen, den Release-Namen und die URL des Releases.

2. **Build and publish:** In diesem Schritt wird das Projekt zunächst gebaut. Anschließend wird das NuGet-Paket gepackt und veröffentlicht, sowohl auf nuget.org als auch auf GitHub Packages.

#### YAML-Konfiguration des Workflows

```yaml
name: Publish NuGet Package on Release
on:
  release:
    types: [published]
  workflow_dispatch:

env:
  CSPROJ_FILE: src/ProjectName/ProjectName.csproj
  DOTNET_VERSION: '8.0.x'

jobs:
  get_release:
    runs-on: ubuntu-latest
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

  build_and_publish:
    needs: get_release
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }}

      - name: Restore dependencies
        run: dotnet restore ${{ env.CSPROJ_FILE }}

      - name: Build
        run: dotnet build ${{ env.CSPROJ_FILE }} --no-restore --configuration Release

      - name: Pack
        run: |
          echo "Using version ${{ needs.get_release.outputs.release_version }} from setup job."
          dotnet pack ${{ env.CSPROJ_FILE }} --no-build --configuration Release \
          -p:PackageVersion=${{ needs.get_release.outputs.release_version }} \
          -p:PackageReleaseNotes=${{ needs.get_release.outputs.release_url }} \
          -p:PackageProjectUrl="https://github.com/${{ github.repository }}" \
          -p:RepositoryUrl="https://github.com/${{ github.repository }}.git" \
          -p:RepositoryType="git" \
          --include-symbols -p:SymbolPackageFormat=snupkg \
          -o nupkgs
        shell: bash

      - name: List output files
        run: |
          echo "Listing built packages in nupkgs directory:"
          ls nupkgs/
        shell: bash

      - name: Push NuGet Package
        run: |
          cd nupkgs
          dotnet nuget push "*.nupkg" --api-key ${{ secrets.NUGET_API_KEY }} --source https://api.nuget.org/v3/index.json --skip-duplicate
        shell: pwsh

      - name: Push to GitHub Packages
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} 
        run: |
          cd nupkgs
          dotnet nuget push "*.nupkg" --api-key $env:GITHUB_TOKEN --source "https://nuget.pkg.github.com/${{ github.repository_owner }}" --skip-duplicate
        shell: pwsh
```

#### Anwendung des Workflows

Um diesen Workflow zu nutzen, füge die obige YAML-Konfiguration in das `.github/workflows`-Verzeichnis deines Projekts ein. Stelle sicher, dass die erforderlichen Secrets wie `GITHUB_TOKEN` und `NUGET_API_KEY` in deinem Repository hinterlegt sind, damit der Workflow korrekt funktionieren kann.

Dieser Workflow bietet eine robuste Lösung für die Automatisierung von Build- und Veröffentlichungsprozessen von NuGet-Paketen, was zur Effizienzsteigerung und Fehlerminimierung in Softwareprojekten beiträgt.


#### Einsatz von PowerShell für den Push von NuGet-Paketen

In unserem Workflow haben wir uns für die Verwendung von PowerShell entschieden, um die NuGet-Pakete zu pushen. Der Hauptgrund dafür ist die fortgeschrittene Unterstützung von PowerShell für Wildcards. Dies erlaubt es uns, mit dem Platzhalter `*.nupkg` alle NuGet-Paketdateien im `nupkgs` Verzeichnis zu identifizieren und zu veröffentlichen, ohne ihre spezifischen Namen angeben zu müssen. Diese Funktion ist besonders nützlich, da die genauen Dateinamen der Pakete je nach Build variieren können und die Verwendung von Wildcards eine flexible und fehlerresistente Handhabung ermöglicht.


## Zukünftige Pläne

Weitere Workflows werden kontinuierlich hinzugefügt und dokumentiert, um unterschiedliche Aspekte der Software-Entwicklung und -Veröffentlichung zu unterstützen.

## Support

Bei Fragen oder Problemen mit den Workflows wende dich bitte über die Issues-Seite an uns.
