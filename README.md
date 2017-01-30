# SmartThings Developer Documentation

This is the repository for the public SmartThings developer documentation.

This documentation is written using [reStructuredText](http://docutils.sourceforge.net/rst.html), powered by [Sphinx](http://www.sphinx-doc.org/en/stable/), and hosted on [ReadTheDocs](http://readthedocs.org).

## Building the docs

Follow these steps to build the documentation locally:

1. [Install virtualenv](https://virtualenv.pypa.io/en/latest/installation.html).
2. Create an isolated environment in a different location than the Documentation directory: `virtualenv --no-site-packages .venv`
3. Activate the environment: `source .venv/bin/activate`
4. Install dependencies: `(.venv)~/Documentation$ pip install -r requirements.txt`
5. Build HTML: `(.venv)~/Documentation$ make html`
6. Open `_build/html/index.html` in a web browser.

To see the available make targets, simply execute `make`.

## Contributing

We love contributions! If you find a typo, error, or think something can be communicated better, fork this repository and make a pull request.

If you have a larger change that might involve a lot of new content or organization, let us know in advance by creating an issue.

### Style guide
For documentation formatting and syntax, please see the [Writing the Docs Guide](http://docs.smartthings.com/en/latest/contributing/style-guide.html).

### Pull request guidelines

We use the fork-and-pull GitHub flow.
You should fork this repository, configure a remote for your fork, and keep your fork in sync.
This is all documented in the [Working with forks](https://help.github.com/articles/working-with-forks) GitHub documentation.

Once your fork is configured and synced with the latest, you should make your changes on a topic branch, commit those changes, and push the branch to make a Pull Request:

1. Checkout a topic branch based off your master branch (this should be synced with the SmartThingsCommunity/Documentation repository):

    `$ git checkout -b topic-branch-name origin/master`

    Where "topic-branch-name" is descriptive of your changes (for example, "fix-scheduling-broken-link").

2. Once you are confident in your changes, you can add them to your index and commit them. Be sure to include a meaningful commit message. Each PR should have exactly one commit. If you have multiple commits on your branch, squash them before making the PR.

    **NOTE:** SmartThings employees should include the JIRA ticket number at the beginning of the commit message (e.g., "[DOCS-234] add documentation for passing data to scheduler method callbacks").

3. Push your topic branch to your fork's remote:

    `$ git push origin topic-branch-name`

4. Visit your fork's remote in a web browser. There will be a yellow banner indicating new changes have been pushed, with the option to make a pull request from them. Click that link.

5. On the Pull Request page, verify your changes look correct, add any description necessary, and submit the pull request. The documentation team will always review any pull requests, but if anything requires the attention of someone specific, `@` them in the pull request description.

6. If there are changes requested as part of the pull request review, you can either add new commits to the PR, or amend your existing commit. If you add new commits, be sure to [squash them to one commit](https://github.com/ginatrapani/todo.txt-android/wiki/Squash-All-Commits-Related-to-a-Single-Issue-into-a-Single-Commit) before merging.

7. Once the PR has a +1 (thumbs-up emoji), a member of the docs team will merge your PR.

## Contact

You can reach us at <mailto:docs@smartthings.com> with any feedback or questions.
