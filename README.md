# sumo-doc
SUMO Documentation with Markdown using MkDocs

## Instructions (from MkDocs): GitHub Pages
If you host the source code for a project on GitHub, you can easily use GitHub Pages to host the documentation for your project. There are two basic types of GitHub Pages sites: Project Pages sites, and User and Organization Pages sites. They are nearly identical but have some important differences, which require a different work flow when deploying.

### Project Pages
Project Pages sites are simpler as the site files get deployed to a branch within the project repository (gh-pages by default). After you checkout the primary working branch (usually master) of the git repository where you maintain the source documentation for your project, run the following command:

mkdocs gh-deploy
That's it! Behind the scenes, MkDocs will build your docs and use the ghp-import tool to commit them to the gh-pages branch and push the gh-pages branch to GitHub.

Use mkdocs gh-deploy --help to get a full list of options available for the gh-deploy command.

Be aware that you will not be able to review the built site before it is pushed to GitHub. Therefore, you may want to verify any changes you make to the docs beforehand by using the build or serve commands and reviewing the built files locally.

!!! Warning!
    You should never edit files in your pages repository by hand if you're using the gh-deploy command because you will lose your work the next time you run the command.
