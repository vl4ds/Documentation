# SmartThings Developer Documentation

This is the repository for the public SmartThings developer documentation.

This documentation is written using [reStructuredText](http://docutils.sourceforge.net/rst.html), powered by [Sphinx](http://www.sphinx-doc.org/en/stable/), and hosted on [ReadTheDocs](http://readthedocs.org).

## Building the docs

Follow these steps to build the documentation locally:

1. [Install virtualenv](https://virtualenv.pypa.io/en/latest/installation.html).
2. Create an isolated environment: `virtualenv --no-site-packages .venv`
3. Activate the environment: `source .venv/bin/activate`
4. Install dependencies: `(.venv)~/Documentation$ pip install -r requirements.txt`
5. Build HTML: `(.venv)~/Documentation$ make html`
6. Open `_build/html/index.html` in a web browser.

To see the available make targets, simply execute `make`.

## Contributing

We love contributions! If you find a typo, error, or think something can be communicated better, fork this repository and make a pull request.

If you have a larger change that might involve a lot of new content or organization, let us know in advance by creating an issue.

## Contact

You can reach us at <mailto:docs@smartthings.com> with any feedback or questions.
