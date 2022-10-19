# VSCode 配置


### settings.json
```json
{
    "python.venvPath": "~/.virtualenvs",
    "python.testing.pytestEnabled": true,
    "fileheader.Author": "Huabo He",
    "fileheader.LastModifiedBy": "Huabo He",
    "python.analysis.completeFunctionParens": true,
    "python.terminal.activateEnvInCurrentTerminal": true,
    "python.defaultInterpreterPath": "~/.virtualenvs/WeTest/bin/python3.8",
    "pythonTestExplorer.testFramework": "pytest",
    "files.exclude": {
        "**/__pycache__": true,
        "**/.pytest_cache": true
    },
    "debug.inlineValues": "on",
    "testing.alwaysRevealTestOnStateChange": true,
    "pythonTestExplorer.testplanEnabled": true,
    "fileheader.tpl": "#--------------------------------\\n#  @Author: {author}\\n#  @Create: {createTime}\\n#--------------------------------\\n\\n\\n",
    "editor.formatOnSave": true,
    "python.formatting.blackArgs": [
        "--line-length",
        "130"
    ],
    "python.languageServer": "Pylance",
    "git.inputValidationSubjectLength": 100,
    "jupyter.debugJustMyCode": false,
    "editor.bracketPairColorization.enabled": true,
    "editor.guides.bracketPairs": true
}
```

<br>

### tasks.json
```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "pytest",
            "type": "process",
            "linux": {
                "command": "${config:python.defaultInterpreterPath}",
                "args": [
                    "-m",
                    "pytest",
                    "-s",
                    "${file}"
                ]
            }
        },
        {
            "label": "collect",
            "type": "process",
            "linux": {
                "command": "${config:python.defaultInterpreterPath}",
                "args": [
                    "-m",
                    "pytest",
                    "--collect-only"
                ]
            }
        },
        {
            "label": "format",
            "type": "process",
            "linux": {
                "command": "${config:python.defaultInterpreterPath}",
                "args": [
                    "-m",
                    "black",
                    "--line-length",
                    "130",
                    "-i",
                    "${file}"
                ]
            }
        },
        {
            "label": "clean-report",
            "type": "shell",
            "linux": {
                "command": "rm",
                "args": [
                    "-r",
                    "${workspaceRoot}/allure-result/"
                ]
            }
        },
        {
            "label": "show-report",
            "type": "shell",
            "command": "allure",
            "args": [
                "serve",
                "-h",
                "0.0.0.0",
                "-p",
                "8080",
                "${workspaceRoot}/allure-result/"
            ]
        },
        {
            "label": "run-server",
            "type": "process",
            "command": "${config:python.defaultInterpreterPath}",
            "args": [
                "-m",
                "uvicorn",
                "web.main:app",
                "--reload",
                "--debug"
            ],
            "options": {
                "cwd": "${workspaceFolder}/server"
            }
        }
    ]
}
```