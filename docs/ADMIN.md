# Introduction

This documentation provides a guide for `pymatgen` administrators. The
following assumes you are using `miniconda` or Anaconda.

## Releases

The general procedure for releasing `pymatgen` comprises the following
steps:

1. Make sure all CI checks are green. We don't want to release known bugs.
1. Update changelog.
1. Run `invoke make-doc` to update the HTML docs.
1. Tag the latest commit with `git tag v<yyyy.mm.dd>`.
1. Make a GitHub release with auto-generated release notes.
1. Make sure the release action runs that publishes the new version to PyPI and conda-forge runs to completion.

## Initial setup

Pymatgen uses [`invoke`](http://pyinvoke.org) to automate releases.
You will also need `sphinx` and `doc2dash`.

```sh
pip install --upgrade invoke sphinx doc2dash
```

## Doing the release

First update the change log. The autogenerated change log is simply a list of commit messages since the last version. Make sure to edit the log for brevity and to attribute significant features to appropriate developers:

```sh
invoke update-changelog
```

Then, do the release with the following sequence of commands (you can put them in a bash script in your PATH somewhere):

```sh
invoke release --notest --nodoc
invoke update-doc
python setup.py develop
```

Double check that the releases are properly done on Pypi. If you are releasing on a Mac, you should see a pymatgen.version.tar.gz and two wheels (Py37 and P). There will be a py37 wheel for Windows that is generated by Appveyor.

## Materials.sh

Fork and clone the [materials.sh](https://github.com/materialsvirtuallab/materials.sh). This repo contains the `conda` skeletons to build the `conda` versions for various matsci codes on the Anaconda [matsci channel](https://anaconda.org/matsci).

The first time this is run, you may need to `pip install beautifulsoup4`.

If you doing this for the first time, make sure conda-build and
anaconda-client are installed:

```sh
conda install --yes conda-build anaconda-client
```

Update the pymatgen meta.yaml:

```sh
invoke update-pypi pymatgen
```

Build the mac versions manually:

```sh
invoke build-conda pymatgen
```

Commit and push to repo, which will build the Linux and Windows
versions.

Check that the [matsci channel](https://anaconda.org/matsci) versions
are properly updated.