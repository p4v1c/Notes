#git 
- Tools : 
```sh
wget https://github.com/arthaud/git-dumper
```

- Run git dumper : 
```sh
./git_dumper.py http://website.com/.git ~/website
```

- Recover files : 
```sh
git status 
git restore --staged <files>
git restore <file>
```