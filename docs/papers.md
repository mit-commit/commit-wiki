# How to Add a Paper to the Commit Website?

We use Git to manage updates to the whole commit website.

**DO NOT EDIT FILES IN `/afs/csail.mit.edu/group/commit/www/data`** (we used to use a different system for this, but we are now using Git).

To add a paper:

- `git clone` the commit website at [`https://github.com/mit-commit/commit-website`](https://github.com/mit-commit/commit-website).

- Create a new branch for your changes.

- Add the PDF file of your paper to `/data/papers/`. Make sure you add it in the right year (for example, papers of 2020 should be in the folder `/data/papers/2020/`). If the folder does not exist, feel free to create it.

- Add a BibTeX entry for the paper in `papers.bib`. Make sure you add a link to your paper, for example using URL. For example:

  `url="http://groups.csail.mit.edu/commit/papers/2018/tiramisu_paper.pdf"`

- Run `add_paper.sh`.

- Follow the instructions in the Readme to verify that the website looks good.

- Commit your changes to git, push them to the repository, and create a pull request.

- You are done. After merging your PR, an admin will update the server based on the `master` branch of the git repo.

- Please keep in mind that the script used to extract information from the file `papers.bib` expects the BibTeX to be in a certain format. For example, the BibTeX entries have to be between `" "`. This is why it might be a good idea to copy another similar entry and modify it.

- Consider sending an email to the commit mailing list to let everyone know about the new paper.
