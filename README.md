# KAMI Plugin Registry

[![Validate Registry](https://github.com/Hypijump31/kami-registry/actions/workflows/validate.yml/badge.svg)](https://github.com/Hypijump31/kami-registry/actions/workflows/validate.yml)

Index communautaire des plugins KAMI. Ce dépôt contient **uniquement des métadonnées** — les binaires WASM sont hébergés sur les releases GitHub de chaque auteur.

---

## Pour les utilisateurs

### Chercher un plugin

```bash
kami search "json transform"
```

### Installer un plugin

```bash
# Depuis ce registre (GitHub shorthand)
kami install Hypijump31/kami-echo@v1.0.0

# Depuis une URL directe
kami install https://example.com/mon-plugin.zip
```

### Lister les plugins installés

```bash
kami list
```

---

## Pour les auteurs de plugins

### 1. Créer votre plugin

Suivez le [Guide de création de plugins](https://github.com/Hypijump31/kami/blob/main/docs/TOOL_AUTHOR_GUIDE.md).

### 2. Préparer la release

```bash
cd mon-plugin
cargo build --target wasm32-wasip2 --release

# Créer l'archive zip
mkdir release
cp tool.toml target/wasm32-wasip2/release/mon_plugin.wasm release/
cd release && zip -r ../plugin.zip . && cd ..
```

### 3. Publier sur GitHub

Uploadez `plugin.zip` comme asset d'une release GitHub taguée (ex: `v1.0.0`).

### 4. Générer l'entrée registry

```bash
kami publish --source votre-org/mon-plugin@v1.0.0
```

Cela produit :

```json
{
  "id": "dev.monorg.mon-plugin",
  "name": "mon-plugin",
  "version": "1.0.0",
  "description": "Ce que fait le plugin",
  "source": "votre-org/mon-plugin@v1.0.0",
  "wasm_sha256": "a1b2c3d4..."
}
```

### 5. Soumettre une Pull Request

1. Forkez ce dépôt
2. Ajoutez votre entrée à `index.json`
3. Ouvrez une PR — la CI validera automatiquement

---

## Format des entrées

Chaque entrée dans `index.json` :

| Champ | Requis | Description |
|-------|--------|-------------|
| `id` | Oui | Identifiant reverse-DNS (depuis `tool.toml`) |
| `name` | Oui | Nom lisible |
| `version` | Oui | Version semver |
| `description` | Oui | Description courte (< 120 caractères) |
| `source` | Oui | GitHub shorthand : `owner/repo@tag` |
| `download_url` | Non | URL HTTPS directe vers le `.zip` du plugin |
| `wasm_sha256` | Oui | Hash SHA-256 hex du fichier `.wasm` |
| `signature` | Non | Signature Ed25519 hex (128 chars) |
| `signer_public_key` | Non | Clé publique Ed25519 hex (64 chars) |

### Règles

- **Une entrée par ID** — mettez à jour l'entrée existante pour les nouvelles versions
- Le `source` doit pointer vers un dépôt GitHub valide avec tag de release
- Le `download_url` doit pointer vers un `.zip` contenant `tool.toml` + `.wasm`
- La description doit être concise (< 120 caractères)
- Les IDs doivent suivre le format reverse-DNS : `dev.org.nom` ou `com.company.tool`

---

## Signer un plugin (optionnel, recommandé)

```bash
# Générer une paire de clés (une seule fois)
kami keygen

# Signer le WASM
kami sign ./mon-plugin/

# Ajouter signature + signer_public_key à votre entrée index.json
```

---

## Validation locale

Avant de soumettre votre PR, vous pouvez valider localement :

```bash
# Vérifier la syntaxe JSON
python3 -m json.tool index.json > /dev/null

# Valider contre le schéma
pip install jsonschema
python3 -c "
import json, jsonschema
with open('schema.json') as s:
    schema = json.load(s)
with open('index.json') as f:
    data = json.load(f)
jsonschema.validate(data, schema)
print(f'Valide: {len(data)} entrées')
"
```

---

## Structure du dépôt

```
kami-registry/
├── index.json                    # Index des plugins
├── schema.json                   # Schéma JSON pour validation
├── README.md                     # Ce fichier
├── LICENSE                       # MIT
├── CONTRIBUTING.md               # Guide de contribution
└── .github/
    └── workflows/
        └── validate.yml          # CI : validation automatique des PR
```

---

## Licence

MIT
