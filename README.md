![image](https://github.com/user-attachments/assets/aea05989-59c4-43a2-989c-ffdcf89f6270)

# Descope Keycloak Migration Tool

This repository includes a Python utility for migrating your Keycloak users, roles, groups, and password hashes to Descope.

The utility allows you to migrate users by loading them from Keycloak export files, preserving user attributes, roles, groups, and password hashes.

## Setup 💿

1. **Clone the Repository**
```bash
git clone git@github.com:descope/descope-keycloak-migration.git
cd descope-keycloak-migration
```

2. **Create and activate a virtual environment**
```bash
python3 -m venv venv
source venv/bin/activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Setup Your Environment Variables**

You can rename `.env.example` to `.env` and fill in your credentials:

```
DESCOPE_PROJECT_ID=your_project_id
DESCOPE_MANAGEMENT_KEY=your_management_key
```



## Exporting Users from Keycloak 📤

Before running the migration script, you need to export your users and realm configuration from Keycloak. The export format should be in JSON.

Keycloak supports export via CLI using the kc.sh export command. The documentation for this command is available here:
🔗 [Keycloak Export and Import Docs](https://www.keycloak.org/server/importExport)

Run the following command inside your Keycloak container to export your realm and users:

```bash
docker exec -it keycloak /opt/keycloak/bin/kc.sh export \
  --dir /opt/keycloak/data/export \
  --realm myrealm \
  --users different_files \
  --users-per-file 100
```

Then copy the export to your local machine:
```bash
docker cp keycloak:/opt/keycloak/data/export ./keycloak-export
```

> This assumes you're using the Docker version of Keycloak. Commands may differ slightly depending on your setup. Refer to the Keycloak export documentation. 

You should now have a folder with the following structure:
```
keycloak-export/
├── master-realm.json
├── master-users-0.json
├── myrealm-realm.json
├── myrealm-users-0.json
├── myrealm-users-1.json
├── myrealm-users-2.json
├── myrealm-users-3.json
├── myrealm-users-4.json
```

## Running the Migration Script 🚀

From the root of the project, run:
```bash
python src/main.py --realm myrealm --path /absolute/path/to/keycloak-export
```

- `--realm` is the name of the realm you exported (not `master`)
- `--path` is the absolute path to the exported JSON files


> A `logs/` folder will be created with details about the process.


The users, roles, and groups will be created in Descope and assigned automatically. If any users are disabled in Keycloak that will carry over to Descope. 


## Issue Reporting ⚠️

For any issues or suggestions, feel free to open an issue in the GitHub repository.

## License 📜

This project is licensed under the MIT License - see the LICENSE file for details.
