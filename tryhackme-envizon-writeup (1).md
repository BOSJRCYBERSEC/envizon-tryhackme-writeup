# 📚 Summary

- [🇬🇧 English Version](#-tryhackme---envizon-write-up-summary)
- [🇫🇷 Version Française](#-tryhackme---envizon-write-up-résumé)

# 🧪 TryHackMe - Envizon (Write-Up Summary)

## 1. 🔍 Initial Enumeration

- **Port 3000**: Envizon web app (Ruby on Rails).
- **Port 22**: SSH is open.

## 2. 📄 Unauthenticated Note Access

- `/notes/1` is accessible without authentication and reveals information.
- A note hints that a password is stored in note ID 380, obfuscated by HashID.

## 3. 🔐 Password Retrieval (IDOR + HashID)

- Using `acts_as_hashids` with default secret to generate hash for ID 380.
- Accessing `/notes/<hashid>` gives the password for the instance.

## 4. 🧨 Command Execution via Nmap (`GTFOBins`)

- The application allows importing XML scan files, processed with `nmap`.
- We exploit this by uploading a crafted XML and a Lua script containing a reverse shell.
- Since Nmap uses the NSE engine, the following gets executed in the background:

  ```bash
  nmap --script=/tmp/reverse.lua 127.0.0.1
  ```

- The Lua script runs `os.execute()` to trigger a reverse shell.

## 5. 📂 Privilege Escalation

- A `borgmatic` config file contains sensitive data.
- Use this info to read the `root.txt` file.

---

## 🔓 Vulnerabilities Identified

- **IDOR**: Insecure Direct Object Reference.
- **Command Injection via Nmap**.
- **Unsecured file upload**.
- **Sensitive config file exposure**.

## ✅ Best Practices

- Enforce strict access control.
- Sanitize and validate user input.
- Restrict file import types and handling.
- Secure sensitive files and credentials.

## 💣 Other Exploitation Vectors

- Ruby on Rails abuse if source is exposed (e.g., `rails console`).
- Poorly secured REST API routes.
- Privilege escalation via misconfigured cronjobs or services.

---

# 🧪 TryHackMe - Envizon (Write-Up Résumé)

## 1. 🔍 Découverte initiale

- **Port 3000** : Application web Envizon (Ruby on Rails).
- **Port 22** : SSH ouvert.

## 2. 📄 Accès non authentifié aux notes

- `/notes/1` est accessible sans authentification et révèle des informations.
- La note mentionne un mot de passe stocké dans une note ID 380, masquée par HashID.

## 3. 🔐 Récupération du mot de passe (IDOR + HashID)

- Utilisation de `acts_as_hashids` avec le secret par défaut pour générer le hash de l’ID 380.
- Accès à `/notes/<hashid>` donne le mot de passe de l’instance.

## 4. 🧨 Exécution de commande via Nmap (`GTFOBins`)

- L’application autorise l’import de fichiers XML de scan, qui sont traités via `nmap`.
- On exploite cette fonctionnalité pour uploader un fichier XML factice **et** un script `Lua` contenant une reverse shell.
- Nmap étant compilé avec le moteur de script `NSE`, la commande suivante est exécutée en arrière-plan :
  
  ```bash
  nmap --script=/tmp/reverse.lua 127.0.0.1
  ```

- Ce script appelle `os.execute()` pour obtenir une reverse shell.

## 5. 📂 Escalade de privilèges

- Le fichier de config `borgmatic` contient des informations sensibles.
- Utilisation de ces informations pour lire le fichier `root.txt`.

---

## 🔓 Vulnérabilités identifiées

- **IDOR** : Accès à des ressources via identifiants prévisibles.
- **Command Injection via Nmap**.
- **Upload de fichiers non sécurisé**.
- **Fichiers de configuration sensibles exposés**.

## ✅ Bonnes pratiques

- Appliquer un contrôle d’accès strict sur les ressources.
- Valider et filtrer toutes les entrées utilisateur.
- Restreindre l’import de fichiers à des types précis.
- Sécuriser les fichiers de configuration et secrets.

## 💣 Autres pistes d’exploitation possibles

- Abus du Ruby on Rails si le code source est exposé (ex : `rails console` en local).
- Attaques sur les routes API REST si peu protégées.
- Escalade via cronjobs ou services système mal configurés.