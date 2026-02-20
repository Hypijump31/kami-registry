# Contribuer au KAMI Registry

Merci de vouloir publier un plugin dans le registre KAMI !

## Comment ajouter votre plugin

### Prérequis

1. Un plugin KAMI fonctionnel (voir le [Guide de création](https://github.com/Hypijump31/kami/blob/main/docs/TOOL_AUTHOR_GUIDE.md))
2. Une release GitHub avec un `plugin.zip` (contenant `tool.toml` + `.wasm`)

### Étapes

1. **Forkez** ce dépôt
2. Générez votre entrée :
   ```bash
   kami publish --source votre-org/mon-plugin@v1.0.0
   ```
3. Ajoutez le JSON produit à `index.json` (gardez l'ordre alphabétique par `id`)
4. **Validez localement** :
   ```bash
   python3 -m json.tool index.json > /dev/null
   ```
5. **Commitez** : `feat: add votre-org/mon-plugin v1.0.0`
6. Ouvrez une **Pull Request**

### Ce que la CI vérifie

- ✅ Syntaxe JSON valide
- ✅ Conformité au `schema.json`
- ✅ Pas d'IDs dupliqués
- ✅ URLs GitHub accessibles

### Mise à jour d'un plugin existant

Pour publier une nouvelle version, modifiez l'entrée existante dans `index.json` :
- Mettez à jour `version`, `source`, `wasm_sha256`
- Ne créez pas de nouvelle entrée (un seul enregistrement par `id`)

### Règles

- Un plugin par PR (facilite la review)
- Le `id` doit correspondre exactement à celui dans votre `tool.toml`
- Le `wasm_sha256` doit correspondre au binaire dans la release
- La description < 120 caractères
- Pas de contenu malveillant, offensant ou illégal

## Contact

Pour toute question, ouvrez une [issue](https://github.com/Hypijump31/kami-registry/issues).
