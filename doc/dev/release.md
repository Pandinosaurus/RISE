## Releases

Instructions and notes for preparing and publishing a release.

**NOTE** as part of release 5.7 or RISE, the sources repo layout has changed and the code
for the classic notebook extension has moved under the `classic` subdir; at this point the
`jlab` area is not ready for shipping, and so these instructions are **only about the
classic extension**.

**NOTE** be aware that the official JupyterLab RISE extensions eventually landed
  in a separate github repo at <https://github.com/jupyterlab-contrib/rise>

### Pre-Release check

**Step 0.** Clean your local repo copy at your top level (ROOT) directory:

```bash
git clean -fdx
ROOT=$(pwd)
```

**Step 1.** Check for updated version numbers in

```
$ROOT/classic/package.json
```

**Step 2.** Build rise-reveal (new step in release 5.7)
```bash
cd $ROOT/rise-reveal
npm install
npm run build
```

**Step 3.** Build the JS and CSS:

```bash
cd $ROOT/classic
npm install
npm run build
```

### Release

**Step 4.** Tag the repo with:

```bash
git tag -a release_tag -m "Release msg"
git push origin release_tag
```

**Step 5.** Build sdist and wheels packages:

```bash
cd $ROOT/classic
python setup.py sdist
python setup.py bdist_wheel
```

**Step 6.** Upload *sdist* and *wheels* to PyPI:

```bash
cd $ROOT/classic
twine upload dist/*
```

**NOTE** when checking the RISE packaging, it can come in handy to publish onto `test.pypi.org` so as to not pollute the official index; for that purpose do
```bash
# to publish on test.pypi.org
twine upload --repository-url https://test.pypi.org/legacy/ dist/*
# to install from that location
pip install --index https://test.pypi.org/simple --upgrade --pre rise
```

**Step 7.** Push changes to conda-forge:

**NOTE** this is performed automatically by some conda-forge bot around one hour after we release on PyPI; but in case you need detailed information about the manual process, please see below

The conda recipe to build the RISE package is maintained in a separate github repo at https://github.com/conda-forge/rise-feedstock.

* First read this section: https://github.com/conda-forge/rise-feedstock#updating-rise-feedstock
* You need to update the version number here: https://github.com/conda-forge/rise-feedstock/blob/master/recipe/meta.yaml#L1
* You need to update the sha number here: https://github.com/conda-forge/rise-feedstock/blob/master/recipe/meta.yaml#L9
* (Optional) You need to update any dependencies if you have new ones or remove old ones.
* (Optional) You may want to update the recipe, for instance, eventually, we will get rid of the post-link steps (see, https://github.com/damianavila/RISE/pull/444).
* (Optional) You may need to rerender the feedstock, eventually.

Open a PR with those changes and when the PR is merged, several CI runs will be triggered and the packages will be generated and uploaded to https://anaconda.org/conda-forge/rise/files.