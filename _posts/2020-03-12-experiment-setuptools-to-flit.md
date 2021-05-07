---
categories: Python Packaging
---

# Experiment: From Setuptools to Flit

I've been working on a small personal project named [d2animdata](https://github.com/pastelmind/d2animdata). It's a single-module Python script that is now [published on PyPI](https://pypi.org/project/d2animdata/). I learned a lot about packaging in Python while working on this project. I recently moved away from [Setuptools] to [Flit] and would like to document my thoughts on the journey.

[distutils]: https://docs.python.org/3/distutils/
[setuptools]: https://setuptools.readthedocs.io/
[flit]: https://flit.readthedocs.io/
[poetry]: https://poetry.eustace.io/

## From Setuptools to Flit

Disclaimer: Whenever choosing a new program, library or framework, I'm always asking myself, "Am I doing it right? Is this standardized? Is this popular, and will remain so in the future?" I'm wary of stepping outside established practices. This is why creating and distributing packages in Python has been particularly difficult--there is no established practice that everyone likes.

That is why I initially chose to write `setup.py`. I wanted to stick with [Distutils]. It ships with Python, so it's widely supported, right?

Unfortunately, Distutils is very outdated. It's missing some important features like [specifying the minimum compatible version of Python](https://stackoverflow.com/q/19534896/). This is why I quickly switched to Setuptools, which provides those missing features.

Setuptools has its own issues. My personal gripe being that there is no formal way to [single-source your package version](https://packaging.python.org/guides/single-sourcing-package-version/).

The Python ecosystem has another standard, the `pyproject.toml` file, which was introduced along with [PEP 517](https://www.python.org/dev/peps/pep-0517/) and [518](https://www.python.org/dev/peps/pep-0518/). It's supported by more recent versions of Pip.

As far as I know, two major projects use `pyproject.toml` to build packages: [Flit] and [Poetry]. Flit takes care of building and publishing pure Python projects. I haven't used Poetry, but I heard it adds dependency management on top. Since Flit seemed to be aiming for a smaller scope, I felt it would be easier to migrate from `setup.py`.

## What I got

After migrating to Flit, I found it offered:

- A well-defined way of single-sourcing the [package version](https://flit.readthedocs.io/en/latest/index.html#usage). No messy fiddling with regexes.
- Building and publishing packages was easier. I didn't have to install [wheel] and run `setup.py sdist bdist_wheel` to build [wheels](https://pythonwheels.com/). I didn't have to install and run [twine] to publish dists to PyPI. Flit supported these tasks out of the box. These are "easy" tasks, but it's nice to have less tools to care about.
- A lack of dependencies is somewhat confusing, but I'm used to managing `requirements.txt` by hand. I'd

**The documentation was the dealbreaker**. The documentation for Distutils and Setuptools is poorly organized. The [docs for Distutils](https://docs.python.org/3/distutils/) are spread across a multi-page tutorial, and its [reference page](https://docs.python.org/3/distutils/apiref.html) contains scant information. Setuptools, on the other hand, crams much its documentation in a [single page](https://setuptools.readthedocs.io/en/latest/setuptools.html).

Distutils/Setuptools docs are not coordinated, either. The documentation for `setup()` parameters are scattered across these two projects. To understand how a particular argument works, I have to examine the docs of _both_ projects.

Flit doesn't have this problem.

[wheel]: https://pypi.org/project/wheel/
[twine]: https://pypi.org/project/twine/
