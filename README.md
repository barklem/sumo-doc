# sumo-doc
SUMO Documentation with Markdown using MkDocs including Math support

## Get started
To work with the documentaton you need to install MkDocs, the Material theme and an extension package:
1. MkDocs Installation: https://www.mkdocs.org/#installation
2. Install Material theme for MkDocs: https://squidfunk.github.io/mkdocs-material
3. Install PyMdown extension for e.g. LaTeX-style math support: https://squidfunk.github.io/mkdocs-material/extensions/pymdown/

### Project Pages
GitHub Project Pages sites are simple as the site files get deployed to a branch within the project repository (gh-pages by default). After you checkout the primary working branch (usually master) of the git repository where you maintain the source documentation for your project, run the following command:

``` bash
mkdocs gh-deploy
```

That's it! Behind the scenes, MkDocs will build your docs and use the ghp-import tool to commit them to the gh-pages branch and push the gh-pages branch to GitHub.

Use ```mkdocs gh-deploy --help``` to get a full list of options available for the gh-deploy command.

Be aware that you will not be able to review the built site before it is pushed to GitHub. Therefore, you may want to verify any changes you make to the docs beforehand by using the build or serve commands and reviewing the built files locally.

### Warning!
You should **never** edit files in your gh-pages repository, nor the sites folder, by hand if you're using the gh-deploy command because you will lose your work the next time you run the command. Edit the .md files and let ```mkdocs gh-deploy``` do the magic.
