# git config

What is new in 2022? Gitlab was good for AquaTech app. Now its a brush up for future endeavors.

#### [Git best practices](https://jupyter-tutorial.readthedocs.io/en/stable/productive/git/best-practices.html)

Immediate concern is any .ipynb cache or hidden files and potential security risks having these uploaded to a public repo.

[StackOverflow](https://stackoverflow.com/questions/35916658/how-to-git-ignore-ipython-notebook-checkpoints-anywhere-in-repository) talks a bout a global `.gitignore` for a computer.

```zsh
git config --global core.excludesfile '~/.gitignore'
echo '.ipynb_checkpoints' >> ~/.gitignore
```

ipynb_checkpoints and is also a . ipynb file. By default, Jupyter will autosave your notebook every 120 seconds to this checkpoint file without altering your primary notebook file. When you “Save and Checkpoint,” both the notebook and checkpoint files are updated.

### .gitignore