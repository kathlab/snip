# Andaconda for Windows

## Create unmutable environment Jupyter Notebook environment for unprivileged users
---

requirements.yaml for CPU tasks
---

```
channels:
  - defaults
  - conda-forge
dependencies:
  - conda-forge::ncurses # fixes warning about libtinfo.so missing version information
  - ipykernel # vscode
  - ipython # vscode
  - kneed
  - matplotlib
  - notebook
  - numba
  - numpy
  - openpyxl
  - pandas # vscode
  - pyDOE
  - python>=3.9
  - scikit-learn<=0.24.1
  - seaborn
  - pip
  # - pip:
```

Add Anaconda3 bin directory to system path (admin)
---

![System path for all Users](assets/anaconda_sys_env.png "System path for all users")

```
C:\ProgramData\Anaconda3\Library\bin
```

Create new environment in C:\ProgramData\Anaconda3\envs (admin)
---

```
> conda create -n ENVIRONMENT_NAME -f requirements.yaml
```

Start jupyter notebook (non-privileged)
---

Run without token and password and connect to [http://localhost:8888](http://localhost:8888)

```
> cd
> C:\ProgramData\Anaconda3\python.exe C:\ProgramData\Anaconda3\cwp.py C:\ProgramData\Anaconda3\envs\ENVIRONMENT_NAME C:\ProgramData\Anaconda3\envs\ENVIRONMENT_NAME\python.exe C:\ProgramData\Anaconda3\envs\ENVIRONMENT_NAME\Scripts\jupyter-notebook-script.py --no-browser --NotebookApp.token='' --NotebookApp.password='' --notebook-dir=C:\TEMP
```