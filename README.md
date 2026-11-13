# Déploiement automatisé d'un portfolio Next.js avec Ansible

## 1. Présentation

Ce projet consiste à automatiser le déploiement d'une application web **Next.js** à l'aide d'**Ansible** sur un serveur Ubuntu.

L'objectif est de mettre en place une infrastructure de déploiement reproductible comprenant :

- Ansible pour l'automatisation de la configuration.
- Node.js pour l'exécution de l'application.
- PM2 pour la gestion du processus applicatif.
- systemd pour le démarrage et la supervision du service.
- Nginx comme reverse proxy.

**Application déployée :** [Montassar Hannachi – Portfolio](https://github.com/MontassarHannachi/portfolio)

## 2. Architecture technique

```text
Administrateur
     |
     | ansible-playbook site.yml
     v
Machine Ansible
     |
     | SSH
     v
Serveur Ubuntu
     |
     +-- Ansible
     |
     +-- Node.js
     |
     +-- PM2 / systemd
     |       |
     |       +-- Next.js : 127.0.0.1:3000
     |
     +-- Nginx : port 8080
             |
             +-- Reverse proxy vers Next.js
```

**URL d'accès au portfolio :**

http://192.168.213.129:8080

## 3. Technologies utilisées

| Technologie | Rôle |
|---|---|
| Ubuntu | Système d'exploitation |
| Ansible | Automatisation du déploiement |
| SSH | Connexion distante |
| Git | Récupération du code source |
| Node.js 22 | Environnement d'exécution |
| npm | Gestion des dépendances |
| Next.js | Framework frontend |
| PM2 | Gestion du processus applicatif |
| systemd | Gestion du service |
| Nginx | Reverse proxy |

## 4. Organisation du projet

```text
portfolio-deploy/
├── ansible.cfg
├── group_vars/
│   └── all.yml
├── inventory/
│   └── hosts.ini
├── roles/
│   ├── common/
│   │   └── tasks/main.yml
│   ├── nodejs/
│   │   └── tasks/main.yml
│   ├── app/
│   │   ├── handlers/main.yml
│   │   ├── tasks/main.yml
│   │   └── templates/portfolio.service.j2
│   └── nginx/
│       ├── handlers/main.yml
│       ├── tasks/main.yml
│       └── templates/portfolio.conf.j2
└── site.yml
```

## 5. Rôle des composants Ansible

### common

- Mise à jour du cache APT.
- Installation des paquets essentiels.
- Création de l'utilisateur de déploiement.
- Création du répertoire `/opt/portfolio`.

### nodejs

- Configuration du dépôt NodeSource.
- Installation de Node.js.
- Installation globale de PM2.

### app

- Clonage ou mise à jour du dépôt GitHub.
- Installation des dépendances avec `npm ci`.
- Compilation de l'application avec `npm run build`.
- Configuration du service systemd.
- Démarrage de l'application avec PM2.

### nginx

- Installation de Nginx.
- Configuration du reverse proxy.
- Activation du site.
- Suppression du site Nginx par défaut.
- Activation et démarrage du service.

## 6. Configuration

Les variables principales sont définies dans `group_vars/all.yml`.

| Variable | Valeur |
|---|---|
| `app_name` | portfolio |
| `app_repo` | Dépôt GitHub du portfolio |
| `app_version` | main |
| `app_dir` | /opt/portfolio |
| `app_port` | 3000 |
| `app_host` | 127.0.0.1 |
| `deploy_user` | deploy |
| `nodejs_version` | 22 |
| `nginx_port` | 8080 |
| `node_env` | production |

L'inventaire Ansible est défini dans `inventory/hosts.ini`.

## 7. Déploiement

### Vérifier la connectivité SSH

```bash
ansible portfolio -m ping
```

### Vérifier la syntaxe du playbook

```bash
ansible-playbook site.yml --syntax-check
```

### Exécuter le déploiement

```bash
ansible-playbook site.yml
```

### Vérifier le service applicatif

```bash
ansible portfolio -b -m command -a "systemctl status portfolio"
```

### Vérifier Nginx

```bash
ansible portfolio -b -m command -a "systemctl status nginx"
```

### Tester la réponse HTTP

```bash
ansible portfolio -b -m uri -a "url=http://127.0.0.1:8080 status_code=200"
```

## 8. Résultats obtenus

Le déploiement a été exécuté avec succès.

Résultats des vérifications :

- Application Next.js compilée en mode production.
- Service portfolio actif.
- Processus géré par PM2.
- Nginx actif sur le port 8080.
- Reverse proxy fonctionnel vers Next.js.
- Réponse HTTP 200 confirmée.
- Deuxième exécution Ansible sans changement : `changed=0`.

## 9. Améliorations possibles

Les prochaines évolutions envisagées :

- Ajouter un pipeline GitHub Actions pour déclencher automatiquement le déploiement.
- Configurer HTTPS avec un certificat TLS.
- Ajouter une stratégie de rollback.
- Mettre en place une supervision avec Zabbix ou Prometheus.
- Séparer la machine de contrôle Ansible du serveur cible.

## 10. Auteur

**Montassar Hannachi**

Junior Cloud & DevOps Engineer

GitHub : https://github.com/MontassarHannachi
