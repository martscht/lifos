---
title: LIFOS Admin Dokumentation
author: Maximilian von Wissel
date: 31.03.2025
---
# LIFOS Admin Dokumentation

> 📅 Zuletzt aktualisiert am 31.03.2025
 
**LIFOS** basiert auf **GitLab**, einer Platform-Software, die vorwiegend in der Softwarenetwicklung zur Versionskontrolle, Dateinverwaltung und zu vielen weiteren Zwecken eingesetzt wird. Die Basis-Features bieten dabei auch ein passendes Grundgerüst, welches die Anforderungen von LIFOS erfüllt. Der Einsatz zu Bildungszwecken ist zudem für unbegrenzt viele User [kostenfrei](https://about.gitlab.com/solutions/education/join/). Da GitLab den gesamten Zyklus der Softwareentwicklung abbildet, gibt es eine Vielzahl an Features, die für LIFOS nicht notwendig sind, jedoch bedingt deaktiviert bzw. ausgeblendet werden können.

> ℹ️ Die nachfolgende Dokumentation beschreibt, welche Anpassungen für den Einsatz an der **Goethe Universität Frankfurt** im Fachbereich Psychologie vorgenommen wurden und richtet sich an Administratoren, die LIFOS in Ihrer Instutition einführen möchten. Nach erfolgreicher Installation sollte eine kontinuierliche Wartung und Verwaltung der Instanz sichergestellt werden, um auf (Sicherheits-)Updates oder Anfragen von Benutzer:innen reagieren zu können.

---

- [LIFOS Admin Dokumentation](#lifos-admin-dokumentation)
  - [1. Erstinstallation und Konfiguration GitLab](#1-erstinstallation-und-konfiguration-gitlab)
    - [1.1 Erstinstallation](#11-erstinstallation)
    - [1.2 Konfiguration](#12-konfiguration)
      - [1.2.1 Allgemeine Konfiguration](#121-allgemeine-konfiguration)
        - [gitlab.rb](#gitlabrb)
      - [1.2.2 Weitere Einstellungen](#122-weitere-einstellungen)
        - [API](#api)
          - [Authentifizierung](#authentifizierung)
          - [CLI](#cli)
    - [1.3 Ergänzende Anpassungen](#13-ergänzende-anpassungen)
      - [1.3.1 Erscheinungsbild](#131-erscheinungsbild)
      - [1.3.2 Datenschutz / Nutzungsbedingungen / Impressum](#132-datenschutz--nutzungsbedingungen--impressum)
    - [1.4 Gruppenstruktur](#14-gruppenstruktur)
      - [Wichtige Konzepte](#wichtige-konzepte)
      - [Empfohlene Struktur](#empfohlene-struktur)
        - [Beispiel](#beispiel)
      - [Berechtigungen](#berechtigungen)
    - [1.5 Templates](#15-templates)
  - [2. Allgemeine Admin Dokumentation](#2-allgemeine-admin-dokumentation)
    - [2.1 Erstellung von Gruppen und Projekten](#21-erstellung-von-gruppen-und-projekten)
    - [2.2 Erstellung von Accounts](#22-erstellung-von-accounts)
    - [2.3 Backups](#23-backups)
      - [2.3.1 Erstellung](#231-erstellung)
    - [2.4 Logging](#24-logging)
      - [2.4.1 Beispiel](#241-beispiel)
      - [2.4.2 fast-stats](#242-fast-stats)
    - [2.5 Upgrades](#25-upgrades)
      - [2.5.1 Allgemeines](#251-allgemeines)
      - [2.5.2 Bug und Security Patches](#252-bug-und-security-patches)
      - [2.5.3 Upgrade-Pfad](#253-upgrade-pfad)
      - [2.5.4 Vorgehen](#254-vorgehen)


---

> ⚠️ Zur erfolgreichen Administration und dem sicheren Betrieb einer GitLab Instanz ist entsprechendes Vorwissen zu Linux-basierten Serveranwendungen erforderlich!

Um eine möglichst hohe Verfügbarkeit sicherzustellen sowie Datenverluste und das Ausnutzen von Sicherheitslücken durch Dritte zu vermeiden, sollten folgende Konzepte bekannt sein:

- git (Versionskontrolle, Repositories, Branch-Management)
- ssh (sicherer Zugriff und Authentifizierung auf Server)
- ssl (Zertifikatsverwaltung für sichere Verbindungen)
- Single-Sign-On (SSO) (Einrichtung und Verwaltung von Authentifizierungsdiensten wie SAML, LDAP oder OAuth)
- User-, Gruppen- und Rollenmanagement
- Netzwerkkonfigurationen
- Backup- und Wiederherstellungsstrategien
- Monitoring und Fehleranalyse (Logs, Performance, Alerts)
- Umgang mit Dateien auf Linux-Systemen (Verzeichnisstruktur, Berechtigungen, Bearbeiten von Konfigurationsdateien, Kommandozeilen-Werkzeuge wie nano, vim, cat, grep, chmod, chown, ls, mv, cp und rm)

---

## 1. Erstinstallation und Konfiguration GitLab

> ℹ️ Diese Dokumentation wurde auf Basis der Version GitLab Version `v17.3.6-ee` erstellt. Einzelne Punkte könnten in zukünftigen Versionen abweichen.

### 1.1 Erstinstallation

Für die Installation von GitLab stehen mehrere Optionen zur Verfügung. Welche die geeignete Option ist, ist abhängig der technischen Infrastruktur und Kenntnissen der Administrator:innen. Die empfohlene Option ist die Nutzung der offiziellen Linux Pakete. Für eine Übersicht über alle Installationsoptionen und -anleitungen verweisen wir auf die [GitLab Website](https://about.gitlab.com/install/).

### 1.2 Konfiguration

Einige Einstellungen können in der **gitlab.rb** Konfigurationsdatei gesetzt werden. Andere wiederum können nicht in einer Konfigurationsdatei festgeschrieben werden, sondern müssen entweder
- über die **Admin Area** im Browser ("Beginner")
- oder die **API** gesetzt werden ("Advanced").
- Weiter gibt es noch die Möglichkeit der **Rails Console** ("Expert").

> ⚠️ Die [Rails Console](https://docs.gitlab.com/ee/administration/operations/rails_console.html) ist ein sehr mächtiges Werkzeug und sollte nur verwendet werden, wenn es für den Einsatzzweck zwingend notwendig ist und entsprechendes Vorwissen besteht. Im Rahmen dieser LIFOS-Dokumentation werden wir die Rails Console nicht einsetzen.

#### 1.2.1 Allgemeine Konfiguration

##### gitlab.rb

Insofern GitLab als Linux Paket installiert wurde, liegt diese Datei in der Regel unter ```/etc/gitlab/gitlab.rb```. Ergänzende Informationen zur Konfiguration über die gitlab.rb Datei kann bei Interesse der [GitLab-Dokumentation](https://docs.gitlab.com/omnibus/settings/configuration.html) entnommen werden. 

**Dieser Teil der Konfiguration ist höchst individuell und dient vorwiegend als Beispiel!** Die Parameter unterscheiden sich je nach gegebener Infrastruktur von Institution zu Institution. Ihre IT-Administrator:innen sollten die benötigten Zertifikate und Informationen zur Einrichtung des Single-Sign-Ons bereitstellen können.

> ℹ️ Wenn unsere Konfiguration (oder Teile von dieser) übernommen werden soll, sind die entsprechenden Zeilen in der vorhandenen gitlab.rb Datei zu ersetzen! Die vorhandene Datei beinhaltet in der Regel bereits die unten aufgeführten Einstellungen (auskommentiert oder mit abweichenden Werten).

    # URL unter der die Instanz erreichbar sein soll
    external_url 'https://lifos.uni-frankfurt.de'

    # Pfad des SSL-Zertifikats und -Keys
    # Dokumentation: https://docs.gitlab.com/omnibus/settings/ssl/
    nginx['ssl_certificate'] = "/etc/ssl/certs/lifos_uni-frankfurt_de.pem"
    nginx['ssl_certificate_key'] = "/etc/ssl/private/lifos.uni-frankfurt.de_key.pem"

    # Konfiguration des Single-Sign-Ons ueber SAML 2.0
    # Dokumentation: https://docs.gitlab.com/ee/integration/omniauth.html
    gitlab_rails['omniauth_allow_single_sign_on'] = ['saml']
    gitlab_rails['omniauth_block_auto_created_users'] = false
    gitlab_rails['omniauth_auto_link_saml_user'] = true

    gitlab_rails['omniauth_providers'] = [
    {
    name: "saml",
    label: "HRZ Login",
    args: {
        assertion_consumer_service_url: "https://lifos.uni-frankfurt.de/users/auth/saml/callback",
        idp_cert_fingerprint: "9F:CF:87:A8:21:C5:5D:61:C4:60:64:36:39:2D:34:0A:79:C3:98:94",
        idp_sso_target_url: "https://cas.rz.uni-frankfurt.de/cas/idp/profile/SAML2/Redirect/SSO",
        issuer: "https://lifos.uni-frankfurt.de",
        name_identifier_format: "urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" ,
        attribute_statements:{ email: ['mailPrimaryAddress'], name: ['displayName'] }
        }
    }
    ]

    # Prevent users from changing their usernames
    # Dokumentation: https://docs.gitlab.com/ee/administration/user_settings.html#prevent-users-from-changing-their-usernames
    gitlab_rails['gitlab_username_changing_enabled'] = false

    # Deaktivierung von bestimmten Features in Projekten die für die Zwecke von LIFOS nicht benoetigt werden
    gitlab_rails['gitlab_default_projects_features_issues'] = false
    gitlab_rails['gitlab_default_projects_features_merge_requests'] = false
    gitlab_rails['gitlab_default_projects_features_wiki'] = false
    gitlab_rails['gitlab_default_projects_features_snippets'] = false
    gitlab_rails['gitlab_default_projects_features_builds'] = false
    gitlab_rails['gitlab_default_projects_features_container_registry'] = false

#### 1.2.2 Weitere Einstellungen

##### API

Für die Interaktion mit der API empfehlen wir die Python Anwendung [python-gitlab](https://python-gitlab.readthedocs.io/en/stable/index.html#), welches das CLI Tool _gitlab_ bereitstellt. Informationen zur [Installation](https://python-gitlab.readthedocs.io/en/stable/index.html#installation) und [Konfiguration](https://python-gitlab.readthedocs.io/en/stable/cli-usage.html#configuration-files) können der verlinkten Dokumentationen entnommen werden.

> ℹ️ Die Installation muss nicht auf dem Gitlab Server geschehen und kann auch auf dem persönlichen Endgerät erfolgen.

###### Authentifizierung

Für die Authentifizierung kann ein "Personal Access Token" genutzt werden, welcher zunächst für einen User Account erstellt werden muss: [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html)

> ℹ️ Für die weiteren Schritte werden Admin Berechtigungen benötigt! Wähle dazu die entsprechenden API Scopes bei der Erstellung des Tokens aus.

Von einer erfolgreichen Verbindung und Authentifizierung kann ausgegangen werden, wenn folgender Befehl den erwarteten Username ausgibt:

    gitlab current-user get

###### CLI

> ⚠️ Der folgende Code kann als bash script gespeichert und ausgeführt werden. Wir empfehlen jedoch, die jeweiligen Befehle einzeln auszuführen, um die einzelnen Einstellungen sowie etwaigen Fehler schrittweiße nachvollziehen zu können.

    #!/bin/bash

    : 'Deaktivierung der eigenstaendigen Registrierung

    https://docs.gitlab.com/ee/administration/settings/sign_up_restrictions.html
    '

      gitlab application-settings update --signup-enabled False

    : 'Deaktivierung der Standard Login-Maske in Gitlab

    ACHTUNG: Lediglich auszufuehren, wenn zuvor der Single-Sign-On ueber einen external authentication provider eingerichtet wurde!
    https://docs.gitlab.com/ee/administration/settings/sign_in_restrictions.html#password-authentication-enabled
    '

      gitlab application-settings update --password-authentication-enabled-for-web False

    : 'Aenderung des Fullnames durch User unterbinden

    Hinweis: Wir nehmen an, dass der Name durch den Single-Sign-On gesetzt wird und daher in Gitlab nicht geaendert werden soll
    '

      gitlab application-settings update --updating-name-disabled-for-users True

    : 'Erstellung von Gruppen auf dem Top-Level unterbinden

    https://docs.gitlab.com/ee/administration/settings/account_and_limit_settings.html#prevent-new-users-from-creating-top-level-groups
    '

      gitlab application-settings update --can-create-group False

    : 'Unterbinden das User oeffentliche (ohne Login zugaengliche) Gruppen oder Projekte erstellen koennen

    https://docs.gitlab.com/ee/administration/settings/visibility_and_access_controls.html#restrict-visibility-levels
    '

      gitlab application-settings update --restricted-visibility-levels public

    : 'Speicherlimit fuer Repositories aus 1024 MiB setzen

    https://docs.gitlab.com/ee/administration/settings/account_and_limit_settings.html#repository-size-limit
    '

      gitlab application-settings update --repository-size-limit 1073741824

    : 'Default branch protection deaktivieren, damit auch User mit der Developer Rolle Änderungen in der Default Branch vornehmen koennen
    https://docs.gitlab.com/ee/user/project/repository/branches/default.html#instance-level-default-branch-protection
    '

      gitlab application-settings update --default-branch-protection 0

    : 'When set to `false` disable the `You won't be able to pull or push project
    code via SSH` warning shown to users with no uploaded SSH key.
    '

      gitlab application-settings update --user-show-add-ssh-key-message FALSE

    : Anzahl der Projekte in persoenlichen Namespace eines Users auf 0 beschraenken, um die Anlage von Projekten in Gruppen zu forcieren
    https://docs.gitlab.com/ee/administration/settings/account_and_limit_settings.html#default-projects-limit
    '

      gitlab application-settings update --default-projects-limit 0

### 1.3 Ergänzende Anpassungen

#### 1.3.1 Erscheinungsbild

In den Admin-Einstellungen ([GitLab-Dokumentation](https://docs.gitlab.com/ee/administration/appearance.html)) kann das Erscheinungsbild der GitLab Instanz angepasst werden. U. a. kann ein Favicon oder Hinweis- bzw. Footer- und Headertexte hinterlegt werden. Dort kann z. B. auch ein Hinweistext konfiguriert werden, welcher bei der Erstellung neuer Projekte angezeigt werden soll ("New project pages").

Für das allgemeine Aussehen von Gitlab stehen verschiedene vordefinierte Themes zur Verfügung. Es besteht die Möglichkeit, das Default Theme als Administrator:in festzulegen. Dies kann über eine weitere Anpassung in der [zuvor](#gitlabrb) bearbeiteten gitlab.rb Datei festgelegt werden. Siehe [Stackoverflow-Beitrag](https://stackoverflow.com/a/63842210) hierzu.

> ℹ️ Das Theme kann in den persönlichen [Einstellung](https://docs.gitlab.com/ee/user/profile/preferences.html) des Benutzeraccounts überschrieben werden. Das Erscheinungsbild ändert sich entsprechend nur für diesen User.

#### 1.3.2 Datenschutz / Nutzungsbedingungen / Impressum

> ⚠️ Die nachfolgend verlinkten Datenschutzinformationen und Nutzungsbedingungen verstehen sich als beispielhafte Vorlage. Eine unveränderte Übernahme wird nicht empfohlen. Stattdessen sollte die Ausarbeitung in Abstimmung mit der*dem Datenschutzbeauftragten der jeweiligen Institution erfolgen.

Wenn [Datenschutzinformationen](https://lifos.uni-frankfurt.de/root/guidelines/-/blob/main/datenschutz.md), [Nutzungsbedingungen](https://lifos.uni-frankfurt.de/root/guidelines/-/blob/main/nutzungsbedingungen.md) und das Impressum für die Nutzung Ihrer LIFOS Instanz nicht bereits anderweitig verfügbar sind, empfehlen wir diese in einem eigenen Projekt als Markdown Datein (.md) abzulegen. Dieses Projekt sollte dann **öffentlich verfügbar** (Siehe [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/public_access.html#public-projects-and-groups)) gemacht werden, damit die Inhalte auch von Personen ohne Login eingesehen werden können.

Die Links zu diesen Dateien können dann z. B. dem Footer hinzugefügt werden, damit diese bei der Nutzung von LIFOS zugänglich sind (Siehe [Erscheinungsbild](#erscheinungsbild)).

Damit alle Benutzer:innnen den Nutzungsbedingungen zustimmen müssen, kann das **Einverständnis** in den Einstellungen ([GitLab-Dokumentation](https://docs.gitlab.com/ee/administration/settings/terms.html#enforce-a-terms-of-service-and-privacy-policy)) entsprechend erzwungen werden.

### 1.4 Gruppenstruktur

Es ist empfehlenswert, zu Beginn der Einrichtung von GitLab eine Gruppenstruktur anzulegen, welche die Abteilungsstrukturen der Institution widerspiegelt. Gruppen können dabei wie **Ordner** betrachtet werden, die zusätzlich eine **Verwaltung von Berechtigungen** ermöglichen.

#### Wichtige Konzepte
- **Gruppen:** Übergeordnete Strukturen (z. B. Abteilungen), die Berechtigungen auf Subgruppen und Projekte vererben.
- **Subgruppen:** Untergeordnete Einheiten (z. B. Lehrveranstaltungen, Semester, Abschlussarbeiten).
- **Projekte:** Einzelne Repositorien für spezifische Projekte oder Aufgaben.
- **Sichtbarkeit (Visibility Level):** Alle Gruppen sollten mit dem Visibility Level **Internal** angelegt werden, damit alle Benutzer:innen alle Gruppen einsehen können. Dies erleichtert die Auswahl des richtigen Ablageorts für Projekte. Weitere Informationen hierzu ist in der [GitLab-Dokumentation zu Visibility Levels](https://docs.gitlab.com/ee/user/public_access.html#internal-projects-and-groups) zu finden.

Diese Konzepte und weitere Best Practices zur Organisation einer GitLab-Instanz sind in diesem [Blogbeitrag](https://about.gitlab.com/blog/2024/07/22/best-practices-to-set-up-organizational-hierarchies-that-scale/) beschrieben.

Um die Übersichtlichkeit bei einer wachsenden GitLab Instanz zu wahren, empfehlen wir, dass alle Benutzer:innen sich an die vorgegebene Gruppenstruktur halten und neue Projekte in einer Gruppe bzw. in der korrekten Subgroup anlegen:
- Über die API haben wir [zuvor](#cli) das Limit für Projekte im persönlichen "Namespace" auf 0 gesetzt. Dies forciert die Anlage in Gruppen.
- Mit Hinweisen in internen Tutorials und bei der Erstellung von Projekten ([siehe GitLab-Dokumentation](https://docs.gitlab.com/ee/administration/appearance.html#add-guidelines-to-the-new-project-page)) versuchen wir die Nutzung der korrekten Subgruppe sicherzustellen.

#### Empfohlene Struktur

(Angelehnt an die in [dieser](https://docs.gitlab.com/ee/user/group/#group-hierarchy) Dokumentation beschriebene Baumstruktur)

- Abteilungen der Institution als **Parent Groups** betrachten bzw. anlegen.
- Lehrveranstaltungen, Semester oder Abschlussarbeiten als **Subgroups** betrachten bzw. anlegen.
- Innerhalb von Subgruppen individuelle **Projekte** erstellen.
- Bestehende Gruppen können von Benutzer:innen wie in der [GitLab-Dokumentation beschrieben](https://docs.gitlab.com/ee/user/group/#view-groups) eingesehen werden.

##### Beispiel

    - Methoden und Evaluation
      - Abschlussarbeiten
        - Projekt 1
        - Projekt 2
        - ...
      - Empirisch-experimentelles Praktikum
        - WiSe 2023-24
          - Projekt 1
          - Projekt 2
          - ...
        - WiSe 2024-25
          - Projekt 1
          - Projekt 2
          - ...
      - Statistik I Praktikum
        - WiSe 2024-25
          - Projekt 1
          - Projekt 2
          - ...
    - Allgemeine und Neurokognitive Psychologie
      - Abschlussarbeiten
        - Projekt 1
        - Projekt 2
        - ...
    - Sozialpsychologie
      - Forschungsprojekte
        - Projekt 1
        - Projekt 2
        - ...

#### Berechtigungen

Die Berechtigung zur Anlage von Projekten in der spezifischen Gruppe kann auf folgenden Wegen erlangt werden:

- Ein "Owner" der Gruppe vergibt die Berechtigung an den jeweiligen User.
  - [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/group/#add-users-to-a-group)
- Der User beantragt die Berechtigung und ein "Owner" der Gruppe genehmigt diese.
  - [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/group/#request-access-to-a-group).

Wenn der zuvor beschriebenen Struktur-Empfehlung gefolgt wurde, sollten Berechtigungen nur auf Ebene der Subgruppen vergeben und Projekte somit auch nur in diesen angelegt werden!

Tipp: Sollte die versehentlichen Berechtigungsanträge auf Ebene der Parent group's Überhand nehmen, kann diese Möglichkeit in den Einstellungen der Gruppen deaktiviert werden ([GitLab-Dokumentation](https://docs.gitlab.com/ee/user/group/access_and_permissions.html#prevent-users-from-requesting-access-to-a-group)).

### 1.5 Templates

Wir empfehlen den Benutzer:innen, Vorlagen für die Erstellung neuer Projekte zur Verfügung zu stellen, damit 
- die Projekte eine einheitliche Struktur aufweißen und
- die Nutzung von LIFOS durch projektspezifische Informationen erleichtert wird. 
  
Dazu haben wir eine dedizierte Gruppe "Admin_Template" angelegt, in welcher eine Reihe verschiedener Projekte existieren. Die Konfiguration als instanzweite Vorlagen wird in der [GitLab-Dokumentation](https://docs.gitlab.com/ee/administration/custom_project_templates.html) beschrieben.

Um die Nutzung der Vorlagen sicherzustellen, empfehlen wir einen entsprechenden Hinweistext bei der Erstellung neuer Projekte anzeigen zu lassen (Siehe [Erscheinungsbild](#erscheinungsbild)).

> ℹ️ Leider besteht aktuell keine Möglichkeit, die Standard Vorlagen von Gitlab zu deaktiveren.

Unsere Vorlagen haben wir [hier](Link) als **Project Export** bereitgestellt. In der [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/project/settings/import_export.html#import-a-project) ist beschrieben, wie diese als neue Projekte auf einer anderen GitLab-Instanz **importiert** werden können.

## 2. Allgemeine Admin Dokumentation

### 2.1 Erstellung von Gruppen und Projekten

Für Informationen zur Erstellung von [Gruppen](https://docs.gitlab.com/ee/user/group/) und [Projekten](https://docs.gitlab.com/ee/user/project/organize_work_with_projects.html) verweisen wir auf die verlinkte GitLab-Dokumentation.

> ℹ️ Wir empfehlen Gruppen und Projekte mit der **Sichtbarkeit** _Internal_ [anzulegen](https://docs.gitlab.com/ee/user/public_access.html#internal-projects-and-groups).

### 2.2 Erstellung von Accounts

Insofern der Single-Sign-On konfiguriert wurde, sollte auf die manuelle Anlage von Accounts verzichtet werden. In Ausnahmfällen können jedoch Accounts manuell angelegt werden. Das Vorgehen wird in der [GitLab-Dokumentation](https://docs.gitlab.com/ee/user/profile/account/create_accounts.html#create-users-in-admin-area) beschrieben.

### 2.3 Backups

Um Datenverlust zu vermeiden, raten wir dazu, in regelmäßigen Abständen Backups der Instanz anzufertigen und die Backupdateien an einem anderen Ort zu sichern.

> ℹ️ Für Informationen zum gesamten Backup-Prozess (inkl. Wiederherstellung) verweisen wir auf die [GitLab-Dokumentation](https://docs.gitlab.com/ee/administration/backup_restore/backup_gitlab.html#simple-back-up-procedure).

#### 2.3.1 Erstellung

Insofern Gitlab als Linux Packet installiert wurde, kann auf dem Server mit dem Befehl ```sudo gitlab-backup create``` ein Backup erstellt werden, welches in der Regel unter ```/var/opt/gitlab/backups``` aufzufinden sein wird.

### 2.4 Logging

Sollte es bei der Verwendung von Gitlab zu **Fehlern bzw. Problemen** kommen, ist ein Blick in die Logs unabdingbar, um die Ursache zu ermitteln und idealerweiße beheben zu können.

GitLab erstellt eine ganze Reihe [verschiedener Logfiles](https://docs.gitlab.com/ee/administration/logs/). Das ```production_json.log``` oder ```application_json.log``` sind jedoch oftmals die interessantesten. Mit entsprechendem Vorwissen können die Logs gezielt durchsucht bzw. gefiltert werden.

> ℹ️ Insofern GitLab als Linux Packet installiert wurde, können der [GitLab-Dokumentation](https://docs.gitlab.com/omnibus/settings/logs.html) spezifische Informationen entnommen werden.

Logfiles werden in der Regel täglich "rotiert", komprimiert und nach einem bestimmten Zeitraum endgültigt gelöscht. Weitere Informationen zum ```logrotate``` sind in der [GitLab-Dokumentation](https://docs.gitlab.com/omnibus/settings/logs.html#logrotate) zu finden.

#### 2.4.1 Beispiel

Im Folgenden ein Beispiel, in welchem das ```application_json.log``` eines vergangenen Tages nach einem bestimmten Zeitraum gefiltet wird. Aufgrund des **JSON** Formates der Logsfiles, können relevante Informationen sehr effizient extrahiert werden.

    sudo gunzip -c /var/log/gitlab/gitlab-rails/application_json.log.10.gz | jq 'select(.time >= "2024-10-28T15:00:00Z" and .time <= "2024-10-28T15:30:00Z")'

#### 2.4.2 fast-stats

GitLab stellt mit **fast-stats** ein Tool [zur Verfügung](https://gitlab.com/gitlab-com/support/toolbox/fast-stats/), welches bei der Arbeit mit Logfiles hilflreich sein kann:

> fast-stats quickly extracts basic performance data from GitLab log files, which customers can provide to Support or parse directly on their server.

### 2.5 Upgrades

#### 2.5.1 Allgemeines

GitLab veröffentlicht jährlich eine neue **Major Version**, monatlich eine **Minor Version** und zweiwöchentlich **Bug und Security Patches**. Weitere Details sind [hier](https://docs.gitlab.com/ee/policy/maintenance.html) einzusehen.

> ℹ️ Wir empfehlen **zeitnah** nach Veröffentlichung auf die zweiwöchentlichen **Bug und Security Patches** zu aktualisieren!
>
> ⚠️ **Vor dem Upgrade** auf eine neue **Minor Version**, und insbesondere auf eine neue **Major Version**, sollten jeweils die Änderungen im [Update-Blog](https://about.gitlab.com/releases/categories/releases/) nachvollzogen werden, um deren Auswirkungen auf den Betrieb als LIFOS zu kennen.

#### 2.5.2 Bug und Security Patches

Patch-Releases umfassen **Bugfixes** für die aktuelle Version von GitLab und **Securityfixes** für die aktuelle und vorherigen beiden monatlichen Minor Releases. Weitere Details sind [hier](https://docs.gitlab.com/ee/policy/maintenance.html#patch-releases) einzusehen.

> **Beispiel**
> 
> Am 11.12.2024 war `17.6` die aktuellste GitLab Version. An diesen Tag wurden [Patch releases](https://about.gitlab.com/releases/2024/12/11/patch-release-gitlab-17-6-2-released/) für die Versionen `17.6.2`, `17.5.4`, `17.4.6` veröffentlicht.

> ℹ️ Bei besonders **schwerwiegenden Sicherheitslücken** hat GitLab in der Vergangenheit ([Beispiel](https://about.gitlab.com/releases/2024/09/25/patch-release-gitlab-16-10-10-released/)) zwar auch Patches releases für noch ältere Versionen veröffentlicht, dennoch **empfehlen wir dringend** spätestens alle drei Monate auf das neuste Minor Release zu aktualisieren.

#### 2.5.3 Upgrade-Pfad

> ⚠️ Sollte es einmal nicht möglich sein, dem monatlichen Upgrade-Pfad zu folgen, muss beachtet werden, dass **größere Versionssprünge** in der Regel zu **Problemen** führen können.

Wir empfehlen das [Upgrade Path Tool](https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/) von GitLab zu nutzen, welches spezifische Informationen und Hinweise für ein sicheres Aktualisieren der GitLab Instanz auf eine bestimmte Version gibt.

#### 2.5.4 Vorgehen

> ⚠️ Vor jedem Upgrade sollte zwingend ein aktuelles Backup erstellt werden! (Siehe [Backups](#23-backups))

Insofern GitLab als Linux Packet installiert wurde, beschreibt diese [GitLab-Dokumentation](https://docs.gitlab.com/ee/update/package/) den Upgrade-Prozess.