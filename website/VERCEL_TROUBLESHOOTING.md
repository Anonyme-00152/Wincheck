# Guide de Dépannage Vercel

## Erreur : 404 DEPLOYMENT_NOT_FOUND

Cette erreur signifie que Vercel n'a pas pu trouver ou créer le déploiement.

### Solutions

#### 1. Vérifier la configuration du projet

**Sur Vercel Dashboard :**
1. Allez dans **Settings** → **General**
2. Vérifiez que :
   - **Root Directory** = `website` ✅
   - **Build Command** = `pnpm install && pnpm build`
   - **Output Directory** = `dist`
   - **Install Command** = `pnpm install --frozen-lockfile`

#### 2. Vérifier les variables d'environnement

1. Allez dans **Settings** → **Environment Variables**
2. Assurez-vous que toutes ces variables sont présentes :
   ```
   DATABASE_URL
   JWT_SECRET
   VITE_APP_ID
   OAUTH_SERVER_URL
   VITE_OAUTH_PORTAL_URL
   OWNER_OPEN_ID
   OWNER_NAME
   BUILT_IN_FORGE_API_URL
   BUILT_IN_FORGE_API_KEY
   VITE_FRONTEND_FORGE_API_KEY
   VITE_FRONTEND_FORGE_API_URL
   VITE_APP_TITLE
   VITE_APP_LOGO
   ```

#### 3. Redéployer

1. Allez dans **Deployments**
2. Cliquez sur le dernier déploiement échoué
3. Cliquez sur **Redeploy**
4. Sélectionnez **Use existing Environment Variables**
5. Cliquez sur **Redeploy**

#### 4. Forcer un redéploiement via Git

```bash
cd Wincheck/website
git commit --allow-empty -m "Trigger Vercel redeploy"
git push origin main
```

## Erreur : Build Failed

### Vérifier les logs

1. Allez dans **Deployments**
2. Cliquez sur le déploiement échoué
3. Cliquez sur **Build Logs**
4. Cherchez le message d'erreur

### Erreurs courantes

#### "pnpm: command not found"
- Vercel n'a pas pnpm installé
- Solution : Utilisez npm à la place
  ```json
  {
    "installCommand": "npm install",
    "buildCommand": "npm run build"
  }
  ```

#### "DATABASE_URL is not set"
- La variable d'environnement n'est pas définie
- Solution : Allez dans Settings → Environment Variables et ajoutez-la

#### "ENOENT: no such file or directory"
- Un fichier est manquant
- Solution : Vérifiez que tous les fichiers sont poussés sur GitHub
  ```bash
  git status
  git add .
  git push origin main
  ```

#### "Module not found"
- Une dépendance est manquante
- Solution : Réinstallez les dépendances
  ```bash
  cd website
  pnpm install
  git add pnpm-lock.yaml
  git push origin main
  ```

## Erreur : Deployment Timeout

Le build prend trop de temps.

### Solutions

1. **Optimiser le build** :
   - Réduire la taille des dépendances
   - Utiliser le cache Vercel

2. **Augmenter le timeout** (Vercel Pro) :
   - Les plans gratuits ont un timeout de 45 secondes
   - Les plans Pro ont 10 minutes

3. **Vérifier les logs** :
   - Voir quel processus prend du temps
   - Optimiser ce processus

## Erreur : Application Crashes

L'application démarre mais crash après.

### Vérifier les logs

1. Allez dans **Deployments** → Votre déploiement
2. Cliquez sur **Runtime Logs**
3. Cherchez les messages d'erreur

### Erreurs courantes

#### "Cannot find module"
- Un module n'est pas installé
- Solution :
  ```bash
  cd website
  pnpm install
  git push origin main
  ```

#### "Database connection failed"
- La base de données n'est pas accessible
- Solution :
  - Vérifiez que DATABASE_URL est correct
  - Vérifiez que votre base de données accepte les connexions distantes
  - Vérifiez les pare-feu

#### "Port already in use"
- Vercel utilise un port différent
- Solution : Le code doit utiliser `process.env.PORT`
  ```ts
  const port = process.env.PORT || 3000;
  app.listen(port);
  ```

## Vérifier que tout fonctionne

### 1. Tester localement

```bash
cd website
pnpm install
pnpm build
pnpm start
```

Accédez à `http://localhost:3000`

### 2. Vérifier les logs Vercel

1. Allez dans **Deployments** → Votre déploiement
2. Cliquez sur **Runtime Logs**
3. Cherchez les erreurs

### 3. Tester l'URL publique

Vercel vous donne une URL (ex: `https://wincheck-web.vercel.app`)
Accédez-y et vérifiez que l'application fonctionne

## Contacter le support

Si rien ne fonctionne :

1. **Vercel Support** : https://vercel.com/support
2. **GitHub Issues** : https://github.com/Anonyme-00152/Wincheck/issues
3. **Logs complets** : Copiez les logs de build et les logs runtime

## Checklist de déploiement

- [ ] Repository cloné sur GitHub
- [ ] Vercel connecté au repository
- [ ] Root Directory = `website`
- [ ] Build Command = `pnpm install && pnpm build`
- [ ] Output Directory = `dist`
- [ ] Toutes les variables d'environnement ajoutées
- [ ] Base de données MySQL créée et accessible
- [ ] Déploiement réussi (pas d'erreur)
- [ ] Application accessible via l'URL Vercel
- [ ] Authentification fonctionne
- [ ] Tableau de bord charge les données

Si tout est coché ✅, votre déploiement est réussi !
