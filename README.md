# Bastion-Teleport


---

<img width="800" height="536" alt="image" src="https://github.com/user-attachments/assets/825d0734-bac5-467c-b097-8c0e02427e62" />


---


## **INTRODUCTION :**

Teleport est une solution de gestion sécurisée des accès aux serveurs, incluant SSH, accès aux applications, base de données et clusters Kubernetes. Elle permet :

* La centralisation des accès.
* L’audit complet des connexions.
* La gestion des utilisateurs et permissions.


---

## **Installation et configuration de Teleport**

### **1) Installation du serveur :**

**Prérequis :**

* OS : Debian 11
* Ports ouverts dans le firewall :

  * `22/tcp` → SSH
  * `443/tcp` → Web UI + Proxy sécurisé
  * `3025/tcp` → Auth Service
  * `3022/tcp` → SSH via Teleport

```bash
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw allow 3025/tcp
sudo ufw allow 3022/tcp
sudo ufw status
```

---

### **2) Installation de Teleport :**

#### **Mise à jour système**

```bash
sudo apt update && sudo apt dist-upgrade -y
sudo apt install curl apt-transport-https -y
```

#### **Ajout de la clé GPG Teleport**

```bash
sudo curl https://apt.releases.teleport.dev/gpg \
  -o /usr/share/keyrings/teleport-archive-keyring.asc
```

#### **Ajout du dépôt Teleport**

```bash
echo "deb [signed-by=/usr/share/keyrings/teleport-archive-keyring.asc] \
https://apt.releases.teleport.dev/debian bullseye stable/v16" \
| sudo tee /etc/apt/sources.list.d/teleport.list > /dev/null
```

#### **Installation de Teleport**

```bash
sudo apt update
sudo apt install teleport -y
```

#### **Configuration de Teleport**

```bash
sudo teleport configure -o file --acme --acme-email=admin@ka-cloud.com --cluster-name=teleport.ka-cloud
```

<img width="1820" height="461" alt="image" src="https://github.com/user-attachments/assets/351c25b2-63f5-4a07-ab03-f6071889f528" />



* `--acme` → permet de générer automatiquement des certificats SSL/TLS via Let’s Encrypt.
* `--cluster-name` → définit le nom du cluster Teleport.
* `--acme-email` → adresse e-mail pour les notifications de certificat.

---

#### **Exemple de fichier `/etc/teleport.yaml`**

```yaml
version: v3
teleport:
  nodename: srv-vm-v1
  data_dir: /var/lib/teleport
  join_params:
    token_name: ""
    method: token
  log:
    output: stderr
    severity: INFO
    format:
      output: text
  ca_pin: ""
  diag_addr: ""
auth_service:
  enabled: "yes"
  listen_addr: 0.0.0.0:3025
  cluster_name: teleport.ka-cloud
  proxy_listener_mode: multiplex
ssh_service:
  enabled: "yes"
proxy_service:
  enabled: "yes"
  web_listen_addr: 0.0.0.0:443
  public_addr: 192.168.0.23:443
  https_keypairs: []
  https_keypairs_reload_interval: 0s
  acme:
    enabled: "no"
```

> Remarque : si tu n’as pas de domaine, `acme` doit être désactivé (`enabled: "no"`) pour éviter l’échec du démarrage.

---

#### **Activation et démarrage du service**

```bash
sudo systemctl enable teleport --now
sudo systemctl status teleport
```
<img width="1768" height="586" alt="image" src="https://github.com/user-attachments/assets/6ef2460e-0b7d-4c75-94a7-318092fe18ef" />



#### **Vérification du statut du cluster**

```bash
sudo tctl status
```
<img width="1298" height="192" alt="image" src="https://github.com/user-attachments/assets/2e74a0c5-96f8-4c56-9533-3348c46cb8b6" />

---

### **3) Création des utilisateurs**

#### **Créer un utilisateur Admin**

```bash
sudo tctl users add admin2 --roles=editor,access --logins=root
```

* `admin2` → nom de l’utilisateur Teleport
* `--roles=editor,access` → droits complets
* `--logins=root` → login système utilisé pour SSH

> Remarque : Pour supprimer un utilisateur on utilise la commande : 

```bash
sudo tctl users rm admin2
```


#### **Lien d’invitation pour définir mot de passe et MFA**

```
Signup URL: https://192.168.0.23:443/web/invite/<token>
```

* Copiez ce lien dans un navigateur pour configurer le mot de passe et la MFA.
* Exemple de mot de passe temporaire pour test : `Admin2@2025@`

---
<img width="1353" height="753" alt="image" src="https://github.com/user-attachments/assets/3c757fda-11a0-4d33-bd03-81f86ccabeb0" />

---

<img width="901" height="777" alt="image" src="https://github.com/user-attachments/assets/7885d00c-f251-4b3d-be6f-804ba83cd492" />


### **4) Test de connexion SSH**

* Depuis l’interface, on peut se connecter à une machine ou procéder à l’enrôlement d’une nouvelle machine.

<img width="1326" height="391" alt="image" src="https://github.com/user-attachments/assets/431597f2-8435-41b8-adc3-ebfbf3ec428e" />

---

<img width="1116" height="258" alt="image" src="https://github.com/user-attachments/assets/1d5ab6f1-e5f3-441e-8f3f-5dd81a56f5fa" />



---
OUSMANE KA
