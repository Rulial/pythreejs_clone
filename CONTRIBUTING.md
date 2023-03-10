# Contributing

Thanks for contributing to pythreejs!

## Code Of Conduct

This project follows the [Project Jupyter Code of Conduct][coc].

[coc]: https://github.com/jupyter/governance/blob/master/conduct/code_of_conduct.md

## Prerequisites

A full development environment will require:

- `python >=3.6`
- `jupyterlab >=3`
  - once built, `jupyterlab` version `1` or `2` may be used
- `nodejs >=12` e.g. via
  ```bash
  conda install -c conda-forge 'nodejs>=12'
  ```
  Or, if you're outside of a conda enviornment, use the node version manager.  See
  [install nvm](https://github.com/nvm-sh/nvm#install--update-script).

## Development Tasks

The relevant commands while working on the repository are included below. These are not meant to be run sequentially, but rather as a list of useful commands.

Most of these tasks are executed on many platforms and clients in [continuous integration][ci]

[ci]: https://github.com/jupyter-widgets/pythreejs/blob/master/.github/workflows/ci.yml

### Clone

```bash
git clone https://github.com/jupyter-widgets/pythreejs
cd pythreejs
```

### Dev setup

```bash
python -m pip install -e .
```

### Re-generate autogen files

```bash
cd js
npm run autogen
```

> More about [autogen](#Autogen-update)

### Build and install JS distribution

```bash
cd js
npm run build:all
jupyter nbextension install --py --symlink --sys-prefix pythreejs
```

Or alternatively as a user local installation:

```bash
jupyter nbextension install --py --symlink --user pythreejs
```

### Build the python distributions

```bash
python setup.py sdist bdist_wheel
```

### Clean out generated files

```bash
cd js
npm run clean
```

### Symlink the JupyterLab 3 federated extension

```bash
jupyter labextension develop . --overwrite
```

### Run the tests

```bash
python -m pip install -e .[test]
pytest -vv -l --nbval-lax --current-env .
```

### Build the docs

```bash
python -m pip install -e .[docs]
cd docs
make html
```

### Explore the examples

```bash
python -m pip install -e .[examples]
```

And then:

```bash
jupyter notebook --debug
# or
jupyter lab --no-browser --debug
```

### Watching sources

```bash
cd js
npm watch
```

### Watching JupyterLab 3+ federated extension

```bash
jupyter labextension watch .
```

## Autogen update

This is a _significant_ re-work of the pythreejs extension that introduces an "autogen" script that generates the majority of the ipython-widget code to wrap each of three.js's types. It also takes a different view towards the pythreejs API. Whereas pythreejs adds custom functionality to the classes, sometimes renaming, etc., this approach attempts to mimic the low-level three.js API as closely as possible, opening up the possibility for others to build utility libraries on top of this.

The autogen script, `generate-wrappers.js`, takes advantage of a config file `three-class-config.js` to auto-generate both javascript and python files to define the ipywidget wrappers for each three.js class. The generated javascript files will have `.autogen.js` as the extension. The generated python files have `_autogen.py` as their extension. The script uses the handlebars template system to generate the various code files.

The autogen solution allows for overriding the default behavior of the generated classes. E.g., if `Object3D.js` is present, then it will be loaded into the namespace as opposed to loading `Object3D.autogen.js`. It is up to the author of the override classe to decide whether to inherit behavior from the autogen class or not. Same goes for the python modules. This allows for writing custom methods on both the python and javascript side when needed.

The autogen script relies on a json-like config file (`three-class-config.js`) to describe the classes. Reasonable defaults should take care of most, but it allows specifying the base class, constructor args, etc. for each of the wrappers. A base version of this file can be generated by `generate-class-config.js`, but beware, it overwrites any customization to the config file that has already been done.
