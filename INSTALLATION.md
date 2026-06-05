# 🔒 SAMLOCK PRO — Guide d'installation complet

## 📁 Structure des fichiers

```
samlock-pro/
├── website/
│   ├── index.html          ← Site web complet (1 seul fichier)
│   └── supabase_schema.sql ← Schéma base de données
└── desktop/
    ├── main.py             ← Logiciel Windows
    ├── requirements.txt    ← Dépendances Python
    ├── adb.exe             ← (copier depuis ton dossier)
    ├── AdbWinApi.dll       ← (copier depuis ton dossier)
    ├── AdbWinUsbApi.dll    ← (copier depuis ton dossier)
    ├── fastboot.exe        ← (copier depuis ton dossier)
    ├── frp.bin             ← (copier depuis ton dossier)
    └── Start.bat           ← (copier depuis ton dossier)
```

---

## 🗄️ ÉTAPE 1 — Configurer Supabase

1. Va sur https://supabase.com et connecte-toi
2. Ouvre ton projet (ou crée-en un)
3. Va dans **SQL Editor** → **New query**
4. Colle le contenu de `supabase_schema.sql` et clique **Run**
5. Récupère tes clés:
   - **Settings** → **API** → copie:
     - `Project URL` → remplace `VOTRE_PROJECT_ID.supabase.co`
     - `anon public` key → remplace `VOTRE_ANON_KEY`

---

## 🌐 ÉTAPE 2 — Configurer le site web

1. Ouvre `website/index.html` dans un éditeur de texte
2. Trouve ces lignes et remplace par tes vraies clés:
```javascript
const SUPABASE_URL = 'https://VOTRE_PROJECT_ID.supabase.co';
const SUPABASE_ANON_KEY = 'VOTRE_ANON_KEY';
const ADMIN_USERNAME = 'admin'; // ← Ton username admin
```
3. **Déployer sur Vercel** (puisque tu as déjà le repo):
   - Push le fichier dans ton repo GitHub (`frp-samsung`)
   - Vercel redéploiera automatiquement
   - OU remplace le contenu de `src/` par ce fichier HTML

---

## 💻 ÉTAPE 3 — Configurer le logiciel Windows

1. Copie tous tes fichiers ADB dans le dossier `desktop/`:
   - `adb.exe`, `AdbWinApi.dll`, `AdbWinUsbApi.dll`
   - `fastboot.exe`, `frp.bin`, `Start.bat`

2. Ouvre `desktop/main.py` et remplace les clés:
```python
SUPABASE_URL  = "https://VOTRE_PROJECT_ID.supabase.co"
SUPABASE_ANON = "VOTRE_ANON_KEY"
```

3. Installe Python (https://python.org) si pas déjà fait

4. Ouvre un terminal dans le dossier `desktop/` et tape:
```bash
pip install -r requirements.txt
python main.py
```

---

## ⭐ ÉTAPE 4 — Créer ton compte Admin

1. Lance le site web ou le logiciel
2. Crée un compte avec le **nom d'utilisateur exact** configuré dans `ADMIN_USERNAME`
3. Dans Supabase → **Table Editor** → `profiles`
4. Trouve ton compte et coche `is_admin = true` et mets `credits = 999999`
   
   OU execute dans SQL Editor:
```sql
UPDATE public.profiles 
SET is_admin = TRUE, credits = 999999 
WHERE username = 'ton_username_admin';
```

---

## 📦 ÉTAPE 5 — Créer un .exe (optionnel)

Pour distribuer le logiciel sans que les utilisateurs installent Python:

```bash
pip install pyinstaller
pyinstaller --onefile --windowed --name "SamLockPro" main.py
```

Le `.exe` sera dans le dossier `dist/`.

⚠️ Assure-toi que les fichiers ADB, frp.bin et Start.bat sont dans le **même dossier** que le .exe.

---

## 🔄 Flux complet

```
Utilisateur                    Site Web                    Supabase
    │                              │                            │
    ├── S'inscrit ────────────────►│──── Crée compte ─────────►│
    ├── Se connecte ──────────────►│◄─── Token JWT ────────────│
    ├── Achète crédits ───────────►│──── Met à jour crédits ──►│
    │                              │                            │
    └── Lance logiciel Windows     │                            │
              │                    │                            │
              ├── Login ──────────►│◄─── Vérifie auth ─────────│
              ├── Clique LOCK      │                            │
              ├── Débite 5 crédits►│──── RPC debit_credits ───►│
              ├── Détecte Samsung  │                            │
              ├── Lance Start.bat  │                            │
              └── Log opération ──►│──── Insère operation ────►│
```

---

## ❓ Support

En cas de problème, vérifie:
- Les clés Supabase sont correctes
- Le schéma SQL a bien été exécuté
- Les fichiers ADB sont dans le bon dossier
- Python 3.10+ est installé
