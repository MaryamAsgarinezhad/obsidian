Conda is an **open source package and environment management system** that runs on Windows, Mac OS and Linux. Conda can quickly install, run, and update packages and associated dependencies. Conda can create, save, load, and switch between project specific software environments on your local computer.

- **APT (Advanced Package Tool)**: `apt` is the package management system used by Debian-based distributions like Ubuntu. It primarily manages system-level software and libraries. The packages available through `apt` are tailored for general software management on a Debian-based system.
- **Conda**: Conda is a cross-platform package manager that is particularly popular in the data science and scientific computing community. It can manage packages from any language, but it is especially oriented towards Python and R environments. Conda packages might include software that is outside the scope of what's typically available in `apt` repositories, especially for specialized scientific libraries or tools.

install conda:
- Use `wget` or `curl` to download the Miniconda installer for Linux. Replace `Miniconda3-latest-Linux-x86_64.sh` with the appropriate filename if a newer version is available.
```shell
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

```

-  Make the installer script executable and run it.

```shell
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
```

**Use Conda:**

### 1. `conda create -n ivnews python=3.8`

This command creates a new Conda environment named `ivnews`.

- **`conda create`**: This is the command used to create a new environment.
- **`-n ivnews`**: The `-n` option specifies the name of the new environment, which in this case is `ivnews`.
- **`python=3.8`**: This specifies that the Python version to be installed in the environment is 3.8. Conda will handle installing this specific version of Python in the `ivnews` environment.

The result of this command is a new, isolated environment where Python 3.8 is installed, separate from other environments and the base Conda installation. This allows for project-specific dependencies without interfering with other projects or the main system setup.

### 2. `conda activate ivnews`

This command activates the previously created `ivnews` environment.

- **`conda activate`**: This is the command used to switch between Conda environments.
- **`ivnews`**: The name of the environment to activate.

Once activated, any further operations involving Python, pip, or other command-line tools will operate within the context of the `ivnews` environment. This means any Python scripts run will use the Python interpreter and libraries available in `ivnews`, and any packages installed using pip or conda will be contained within this environment.

### Purpose and Benefits

Using Conda environments like this is beneficial because:

- **Isolation**: Each environment is isolated from others, preventing version conflicts between project dependencies.
- **Reproducibility**: Environments can be easily duplicated on other machines, enhancing consistency across development, testing, and production settings.
- **Flexibility**: Different projects can use different versions of libraries or Python itself without affecting one another.

These environment management capabilities are part of why Conda is popular for data science, scientific computing, and other development work that requires careful management of software versions and dependencies.