Install
=======


Create Virtual Environment
--------------------------

- Move to the root of the repository `cd ${REPO_ROOT}`

  .. note::
     Here variable `${REPO_ROOT}` indicates the root of the repository.

- Create a virtual env

  .. code-block:: shell

     virtualenv -p python3.9 venv-test

- Activate virtualenv

  .. code-block:: shell

     source venv-test/bin/acttivate

- Install requirements

  .. code-block:: shell

     pip install -r requirements.txt
