# Minecraft Resource Pack Deployment

This repository is used to manage and automatically deploy Minecraft resource packs for development (dev) and production (prod) environments.
## Project structure

The resource packages are organized in the following structure:
```
resource-pack/
├── .github/
│   └── workflows/
│       └── zip-and-upload.yml               # This GitHub Actions workflow
├── assets/                                  # Design-related assets (e.g., icons, logos) (not zipped)
│   └── design-stuff/                        
├── minecraft/
│   ├── dev/                                 # Development resource pack
│   │   ├── assets/
│   │   └── pack.mcmeta
│   └── prod/                                # Production resource pack
│       ├── assets/
│       └── pack.mcmeta
├── sha1_hashes.json                         # JSON file containing the SHA1 hashes of the resource packs
└── README.md
```

## Deployment workflow

The `zip-and-upload.yml` GitHub Actions workflow automates the process of zipping and uploading resource packages to a server via SFTP.

**Functionality:**

1.  **Checkout:** The repository is checked out.
2.  **SHA1 hash calculation:** The current SHA1 hashes are calculated for the directories `minecraft/dev` and `minecraft/prod`. These hashes represent the contents of the respective packages.
3.  **Hash comparison:** The currently calculated hashes are compared with the last known hashes stored in the `sha1_hashes.json` file.
4.  **Conditional upload:** A package is **only zipped and uploaded** if its current SHA1 hash differs from the stored hash. This ensures that only changed packages are transferred.
     * The ZIP files are created so that their contents (e.g., `pack.mcmeta`, `assets/`) are located directly in the root directory of the ZIP.
     * The `dev` ZIP is uploaded to the `dev/` folder on the server, and the `prod` ZIP is uploaded to the `prod/` folder.
5. **Hash update:** After a successful upload, the new SHA1 hash of the package is updated in the `sha1_hashes.json` file and this change is pushed back to the repository. This serves as a persistent “key” for the state of the packages.

## Setup Instructions

To use the automated ZIP creation and upload of Minecraft resource packs via GitHub Actions, follow these steps:

### 1. Configure Repository Secrets

To enable SFTP upload to your Server, add the following secrets in your GitHub repository under **Settings > Secrets and variables > Actions**:

| Secret Name      | Description                                               |
|------------------|-----------------------------------------------------------|
| `SFTP_HOST`      | Hostname or IP address of your Server                     |
| `SFTP_USER`      | Username for SFTP access                                  |
| `SFTP_PASSWORD`  | Password for the SFTP user                                |
| `SFTP_PORT`      | SFTP port (default is usually 22)                         |
| `SFTP_PATH`          | Destination path on the Server (e.g., `/path/to/uploads`) |

> **Note:** Make sure the user has write permissions to the target directory on your SFTP server.

### 2. Review and Customize the Workflow

- The workflow file is located at `.github/workflows/zip-and-upload.yml`.
- It runs on pushes to the `master` branch.
- When changes in the `minecraft/dev` or `minecraft/prod` directories are detected, it zips the respective folder and uploads it via SFTP.
- SHA1 hashes of the last successful uploads are stored in `sha1_hashes.json` to avoid redundant uploads.

### 3. Push Changes and Test

- When you push changes to the resource packs, the workflow will run automatically.
- You can check the progress and logs in the GitHub Actions tab of your repository

## Current pack hashes

The current SHA1 hashes of the `dev` and `prod` packages that were last successfully uploaded can be found in the file [`sha1_hashes.json`](./sha1_hashes.json) in the root directory of this repository.