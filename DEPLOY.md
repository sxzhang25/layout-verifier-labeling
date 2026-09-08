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

## 3. Wire up recording

See `web/appscript/README.md` in the research repo: create a Sheet, paste `Code.gs`, deploy as a web
app with *Who has access: Anyone*, then rebuild this folder with `--endpoint '<the /exec URL>'` and
push again. Without an endpoint the page still works -- participants click **Download JSON** and send
you the file.

## 4. Invite people

One link per participant, with their id in it:

```
https://<you>.github.io/<study-repo>/?p=alice
https://<you>.github.io/<study-repo>/?p=bob
```

The id is what attributes their rows, and it makes the page skip asking for a name. Send each person
their own link.

## 5. Collect

Sheet **File -> Download -> CSV**, then in the research repo:

```bash
python scripts/vglg_blind_labeling.py --study --merge_sheet ~/Downloads/labels.csv
```

which writes a session per participant and prints agreement with the dataset's existing labels.
