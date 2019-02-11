# Fathom to Clever-Cloud

### Requirements

* clever-cloud cli (logged in + SSH key set-up)
* git
* curl
* wget
* tar
* ~ openssl is required to generate the secret_key, but you can change to something else

### Create the app

* clone [this repository](https://github.com/gmolveau/fathom-clevercloud.git)

```bash
git clone https://github.com/gmolveau/fathom-clevercloud.git
cd fathom-clevercloud
```

* create the app on clever-cloud

```bash
clever create --type node -a fathom fathom
```

* you can change the type of plan if you want - https://www.clever-cloud.com/en/pricing

```bash
clever scale --flavor pico -a fathom
```

* link the current folder to the app

```bash
clever link fathom
```

* add a postgres database

```bash
clever addon create postgresql-addon fathom-postgres --yes --plan dev --link fathom
```

* download latest version of fathom for linux 64bit

```bash
curl -s https://api.github.com/repos/usefathom/fathom/releases/latest \
  | grep browser_download_url \
  | grep linux_amd64.tar.gz \
  | cut -d '"' -f 4 \
  | wget -qi - -O- \
  | tar --directory bin -xz - fathom
```

* configure environment variables

```bash
clever env set FATHOM_DATABASE_DRIVER postgres 
clever env set FATHOM_DATABASE_URL $(clever env | grep POSTGRESQL_ADDON_URI | cut -d '=' -f2) 
clever env set FATHOM_DEBUG true 
clever env set FATHOM_SECRET $(openssl rand -base64 32) 
clever env set FATHOM_GZIP true
```

* add, commit and deploy the code

```bash
git init
git add --all
git commit -m "First Commit"
clever deploy -q
```

* check if everything works

```bash
# single quotes
echo '$APP_ID/bin/fathom --version; exit;' | clever ssh
```

* add the first user

```bash
echo '$APP_ID/bin/fathom user add --email="test@example.fr" --password="test_password"; exit;' | clever ssh
```

* open the browser to login and add your first website

```bash
clever open
```

* ENJOY :)
