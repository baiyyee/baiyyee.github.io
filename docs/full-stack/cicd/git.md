# git

- git setup

```console
$ git config --global user.name "username"
$ git config --global user.email "useremail@test.com"

ssh-keygen -t rsa -C "useremail@test.com"
cat ~/.ssh/id_rsa.pub
```

- git commit

| TYPE     | DESCRIPTION                                                          |
| -------- | -------------------------------------------------------------------- |
| feat     | new feature                                                          |
| fix      | bugfix                                                               |
| docs     | just update docs, like README, CHANGELOG, CONTRIBUTE...              |
| style    | update the blank space, indentation, comma, no any code logic change |
| refactor | code refactor, no new feature or bugfix                              |
| perf     | code optimization, like performance, user experience                 |
| test     | testcase, include unite test, integration test...                    |
| chore    | update build process, add dependency, tools...                       |
| revert   | back to the last version                                             |


- git command

```console
git checkout -b dev
git push origin dev
```