Repository for docker images to be used when running KingMaker and CROWN

# KingMaker Images CI

This repository builds Conda environment-based container images using GitHub Actions workflows for KingMaker/CROWN. The workflows consume the provided Conda environment YAML files and produce container images for the target OS.

**Repository layout (relevant files)**

- **Conda envs:** ``KingMaker_envs/*_env.yml``
- **Workflows:** ``.github/workflows/deploy-*-images.yml``
- **OS Dockerfile:** ``rhel9/Dockerfile``

How it works

- Each GitHub Actions workflow reads one Conda environment YAML files and builds a corresponding container image for each requested OS. The workflows are configured in the `.github/workflows` directory and triggered by pushes.
- The built images are pushed to the github registry with tags based on the commit hash.

Creation of yaml files

- To build the Conda files the environment is set up locally and exported via conda:

```
conda env export > NewEnv_env.yml
```

- To build the Dockerfile locally (Alma/RHEL9) the respective env has to be copied to the Dockerfile directory with the name ``conda_env.yml``.
- Then the build process can be run from the `kingmaker-images` folder:

```
docker build -t kingmaker:rhel9 -f rhel9/Dockerfile .
```

Triggering CI

- Push changes to the repository trigger the published GitHub Actions workflows in `kingmaker-images/.github/workflows`.
- The workflows will use the environment YAML files to assemble images. Check workflow logs on GitHub to debug failures.