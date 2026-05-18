# ⚽ CAN À LOMÉ 2026 — Site Web Officiel

Site web complet pour l'événement étudiant CAN À LOMÉ 2026.

---

## 🗂️ Structure du projet

```
can-a-lome-fedapay/
├── backend/
│   ├── server.py          ← Serveur Flask (API + fichiers statiques)
│   ├── requirements.txt   ← Dépendances Python
│   └── can_lome.db        ← Base SQLite (générée au premier lancement)
│
└── frontend/
    └── public/
        ├── index.html     ← Site public
        ├── styles.css     ← Styles du site public
        ├── admin.html     ← Tableau de bord admin
        └── admin.css      ← Styles admin
```

---

## 🚀 Lancer le projet en local

### 1. Installer Python (3.10+)
Vérifie avec : `python --version`

### 2. Installer les dépendances
```bash
cd backend
pip install -r requirements.txt
```

### 3. Lancer le serveur
```bash
python server.py
```

Le site sera disponible sur : **http://localhost:5000**

> Le backend sert aussi les fichiers du dossier `frontend/public/`. N'ouvrez pas les HTML en double-clic (`file://`) : l'API ne fonctionnera pas.

---

## 🔐 Accès Admin

- URL : `http://localhost:5000/admin`
- Identifiant : `admin`
- Mot de passe : `admin2026`

> ⚠️ **Change le mot de passe** avant de mettre en ligne !
> Dans `server.py`, modifie la ligne :
> ```python
> pw = hashlib.sha256('admin2026'.encode()).hexdigest()
> ```
> Remplace `admin2026` par ton vrai mot de passe.

---

## ☁️ Déployer sur Render (gratuit)

1. Crée un compte sur [render.com](https://render.com)
2. Nouveau service → **Web Service**
3. Connecte ton repo GitHub
4. Paramètres :
   - **Build Command** : `pip install -r backend/requirements.txt`
   - **Start Command** : `python backend/server.py`
   - **Root Directory** : laisser vide
5. Variables d'environnement :
   - `JWT_SECRET` → une longue chaîne secrète aléatoire
   - `FEDAPAY_SECRET_KEY`, `FEDAPAY_ENV`, `SITE_URL` (voir section FedaPay)

---

## 💳 Paiements (FedaPay)

Les clients sont redirigés vers FedaPay (TMoney, Moov Money, Carte). Configure les clés API et le webhook (voir fin de ce README).

---

## 🛠️ Personnaliser

| Quoi | Où |
|---|---|
| Nom de l'événement | `frontend/public/index.html` |
| Équipes par défaut | `backend/server.py` → `init_db()` |
| Prix des tickets | `frontend/public/index.html` + `backend/server.py` → dict `prices` |
| Infos de contact | `frontend/public/index.html` → `#contact` |
| Couleurs (site public) | `frontend/public/styles.css` → `:root` |
| Couleurs (admin) | `frontend/public/admin.css` → `:root` |
| Mot de passe admin | `backend/server.py` → `init_db()` |

---

## 📱 Fonctionnalités

### Site public
- Page d'accueil avec stats en temps réel
- Liste des équipes et calendrier des matchs
- Billetterie et réservation de stands (FedaPay)
- Formulaire de contact

### Tableau de bord admin
- Login sécurisé (JWT)
- Stats, tickets, stands, équipes, scores

---

*Fait avec ❤️ pour CAN À LOMÉ 2026*

---

## 💳 Configurer FedaPay

### 1. Créer un compte
- Test : https://sandbox.fedapay.com
- Live : https://app.fedapay.com

### 2. Récupérer ta clé API secrète
Dashboard FedaPay → **Clés API** → `sk_sandbox_...` ou `sk_live_...`

### 3. Variables d'environnement (Render)
| Variable | Valeur |
|---|---|
| `FEDAPAY_SECRET_KEY` | `sk_live_XXXXXXXX` |
| `FEDAPAY_ENV` | `live` |
| `SITE_URL` | `https://ton-site.onrender.com` |
| `JWT_SECRET` | une longue chaîne aléatoire |

### 4. Webhook FedaPay
- **URL** : `https://ton-site.onrender.com/api/webhook/fedapay`
- **Événements** : `transaction.approved`, `transaction.canceled`, `transaction.declined`

### Flux de paiement
1. Client remplit le formulaire → redirection FedaPay
2. Paiement → retour sur le site
3. Webhook → ticket/stand marqué `paid` en base
