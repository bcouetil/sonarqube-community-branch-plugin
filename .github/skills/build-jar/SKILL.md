---
name: build-jar
description: 'Génère le JAR du plugin sonarqube-community-branch-plugin avec Gradle shadowJar sur Windows. Use when: "génère un jar", "build le jar", "package le plugin", "incrementer la version", "build shadowJar". Gère le proxy EDF (vip-users.proxy.edf.fr) et l''inspection SSL corporate via le truststore Windows.'
argument-hint: 'patch | minor | major — niveau de version à incrémenter (défaut: patch)'
---

# Build JAR (Windows)

Génère le fat-JAR du plugin via `shadowJar` sur **Windows** (PowerShell + `gradlew.bat`). Gère le proxy EDF et l'inspection SSL corporate via le truststore Windows.

## Prérequis — config proxy globale

Le fichier `~/.gradle/gradle.properties` doit contenir ces lignes (à créer une seule fois) :

```properties
org.gradle.jvmargs=-Djava.net.useSystemProxies=true -Djavax.net.ssl.trustStoreType=WINDOWS-ROOT
```

Sans cette config, Gradle échoue avec `PKIX path building failed` car le proxy EDF fait de l'inspection SSL avec un certificat corporate que la JVM ne reconnaît pas. `WINDOWS-ROOT` dit à la JVM d'utiliser le truststore Windows (le même que Edge/Chrome).

## Procédure

### 1. Incrémenter la version dans `gradle.properties`

Lire la version actuelle :
```
version=X.Y.Z
```

Incrémenter selon la demande (défaut : patch) :
- **patch** : `X.Y.Z` → `X.Y.Z+1`
- **minor** : `X.Y.Z` → `X.Y+1.0`
- **major** : `X.Y.Z` → `X+1.0.0`

Modifier `gradle.properties` avec la nouvelle version. Ne pas toucher aux autres lignes.

### 2. Lancer le build

```powershell
.\gradlew.bat shadowJar
```

Si `~/.gradle/gradle.properties` n'est pas encore configuré (première fois sur la machine), utiliser les flags explicitement :

```powershell
.\gradlew.bat shadowJar "-Djava.net.useSystemProxies=true" "-Djavax.net.ssl.trustStoreType=WINDOWS-ROOT"
```

Puis créer le fichier global pour les prochaines fois :

```powershell
@"
org.gradle.jvmargs=-Djava.net.useSystemProxies=true -Djavax.net.ssl.trustStoreType=WINDOWS-ROOT
"@ | Out-File "$env:USERPROFILE\.gradle\gradle.properties" -Encoding utf8
```

### 3. Vérifier le JAR produit

```powershell
Get-ChildItem build\libs\
```

Le JAR est dans `build/libs/sonarqube-community-branch-plugin-X.Y.Z.jar` (~6.6 MB).
