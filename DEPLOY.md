# Deploying this study

This folder is a complete, static site. It is meant to be its own git repository, separate from the
research repo that generated it.

Recording endpoint: https://script.google.com/macros/s/AKfycbyuylZP8_oNHRGdOkL7_ogyj_ulS2suwsVEPNyi3umJevy1LoNKwpp9Zb_l5b74nPIP5Q/exec

## 1. Push it

```bash
cd <this folder>
git init -b main
git add .
git commit -m "Layout labeling study"
git remote add origin git@github.com:<you>/<study-repo>.git
git push -u origin main
```

`.gitignore` here is deliberately empty. The parent research repo ignores `*.png` globally, and a
study repo that inherited that would publish `index.html` with every image missing -- a page that
loads and shows nothing.

## 2. Turn on Pages

Repo **Settings -> Pages -> Build and deployment**: source *Deploy from a branch*, branch `main`,
folder `/ (root)`. The URL appears after a minute or two, as
`https://<you>.github.io/<study-repo>/`.

`.nojekyll` is present so Pages serves every path verbatim instead of treating names beginning with
`_` as Jekyll internals.

Note the **site is public even if the repository is private**. The renders and the dev-set examples,
including their written reasons, are readable by anyone with the link.

## 3. Wire up recording, and load the answer key

See `web/appscript/README.md` in the research repo: create a Sheet, paste `Code.gs`, import the
generated `*__answer_key.csv` into a `key` tab, deploy as a web app with *Who has access: Anyone*,
then rebuild this folder with `--endpoint '<the /exec URL>'` and push again.

The key tab is what fills the sheet's `true_label` and `agreement` columns. It stays in the
spreadsheet and is **never** part of this folder -- the packager refuses to write it here, because
everything under this directory is served to participants.

## 4. Invite people

Either works, because `?p=` only *pre-fills* the landing page's name box and the box stays editable:

* **One link each**, with their id baked in -- nothing for them to type:

  ```
  https://<you>.github.io/<study-repo>/?p=alice
  https://<you>.github.io/<study-repo>/?p=bob
  ```

* **One link for everybody** -- they enter their own initials, which is what attributes their rows:

  ```
  https://<you>.github.io/<study-repo>/
  ```

Whichever you use, the id someone ends up with is what lands in the `participant` column. Two people
entering the same initials get pooled, and two people using the *same browser profile* share
progress (it is stored per browser), so one person per machine or profile.

## 5. Collect

Sheet **File -> Download -> CSV**, then in the research repo:

```bash
python scripts/vglg_blind_labeling.py --study --merge_sheet ~/Downloads/labels.csv
```

which writes a session per participant and prints agreement with the dataset's existing labels.
