# setup.py


```python
import os
from setuptools import setup, find_packages

VERSION = "0.3.1.3"

def recursive_requirements(requirements_file, libs, links, path=""):
    if not requirements_file.startswith(path):
        requirements_file = os.path.join(path, requirements_file)

    with open(requirements_file) as requirements:
        for requirement in requirements.readlines():
            if requirement.startswith("-r"):
                requirements_file = requirement.split()[1]
                if not path:
                    path = requirements_file.rsplit("/", 1)[0]
                recursive_requirements(requirements_file, libs, links, path=path)
            elif requirement.startswith("-f"):
                links.append(requirement.split()[1])
            elif requirement.startswith("--allow"):
                pass
            else:
                libs.append(requirement)

requirements, dependency_links = [], []
recursive_requirements("requirements.txt", requirements, dependency_links)

setup(
    name="xxxxxxxx",
    version=VERSION,
    url="xxxxxxxx",
    description="xxxxxxxx",
    long_description=open("README.md", "r").read(),
    author="xxxxxxxx",
    author_email="xxxxxxxx",
    maintainer="xxxxxxxx",
    maintainer_email="xxxxxxxx",
    license="BSD",
    include_package_data=True,
    install_requires=requirements,
    dependency_links=dependency_links,
    packages=find_packages(),
    classifiers=[
        "Programming Language :: Python :: 3.7+",
        "Development Status :: 5 - Production/Stable",
        "Environment :: Console",
        "Intended Audience :: End Users/Desktop",
        "Operating System :: OS Independent",
        "Programming Language :: Python",
        "Topic :: Software Development :: Libraries :: Python Modules",
        "Topic :: Utilities",
        "License :: OSI Approved :: BSD License",
    ],
)
```

```console
python3 setup.py bdist_wheel
```