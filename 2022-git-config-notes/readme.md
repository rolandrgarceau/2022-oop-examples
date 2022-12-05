# git config

What is new in 2022? Gitlab was good for AquaTech app. Now its a brush up for future endeavors.

Immediate concern is .ipynb cache and potential security risks having these in a public repo.

[StackOverflow](https://stackoverflow.com/questions/35916658/how-to-git-ignore-ipython-notebook-checkpoints-anywhere-in-repository) talks a bout a global `.gitignore` for a computer.

```zsh
git config --global core.excludesfile '~/.gitignore'
echo '.ipynb_checkpoints' >> ~/.gitignore
```

### .gitignore