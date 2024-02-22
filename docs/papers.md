# How to Add a Paper to the Commit Website?

We use Git to manage updates to the whole commit website. The website files are in `/afs/csail.mit.edu/group/commit/www/data`

To modify any file in the commit website, you need to have the right permissions. The entire commit group has read-write access on the website. If a user is in the commit Kerberos group, they should be able to edit the previous folder. If you don't have access, please send an email to `commit-sysadmin@lists.csail.mit.edu`

To add a paper:

- Add the PDF file of your paper to `/afs/csail.mit.edu/group/commit/www/data/papers/`. Make sure you add it in the right year (for example, papers of 2020 should be in the folder `/afs/csail.mit.edu/group/commit/www/data/papers/2020/`). If the folder does not exist, feel free to create it.

- Add a BibTeX entry for the paper in `papers.bib`. Make sure you add a link to your paper, for example using URL. For example:

  `url="http://groups.csail.mit.edu/commit/papers/2018/tiramisu_paper.pdf"`

- Run `add_paper.sh`.

- Please commit your changes to git.

- You are done. Open [http://groups.csail.mit.edu/commit/?page=publications](http://groups.csail.mit.edu/commit/?page=publications) and make sure your paper entry, the BibTeX, and the PDF link are all good.

- Please keep in mind that the script used to extract information from the file `papers.bib` expects the BibTeX to be in a certain format. For example, the BibTeX entries have to be between `" "`. This is why it might be a good idea to copy another similar entry and modify it.

