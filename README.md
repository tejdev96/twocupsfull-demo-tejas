# Test Project


# Install

- Install `jq` from [here](https://jqlang.github.io/jq/download/)
- Instll `python3` from [here](https://www.python.org/downloads/)
- Install `virtualenv` from [here](https://virtualenv.pypa.io/en/latest/installation.html)
- Create a virtual env with `virtualenv -p python3.9 venv-test`
- Activate virtual env with `source venv-test/bin/activate`
- Install requirements with `pip install -r requirements.txt`

# Build

- Move to `app` directory `cd app`
- Make `html` with `make clean html`

# Artifact

Built artifacts can be found at `${REPO_ROOT}/app/build/html/`. To deploy, this directory can be zipped and uploaded to artifact
management system and then from there can be deployed to target system like `staging` or `qa` or `production` server.

# Jenkinsfile

`Jenkinsfile` can be used to automatically build and ship artifacts.
