# How to start a python package?

## Starting....
Starting a python package might be overwhelming to newbiees, which was the case for me. At the begining I was following the skeleton/folder structures from some of the published python packages from my mentors or lab- which is a good way to start. Even better will be use the tools such as cookie-cutter to to structure the architecure(skeleton) for a python package. There are many great cookie-cutter template with different features. Depending on your need such as template for conda-recipe, template for Cicle-CI or Travis ...etc, you can search for appropriate cookie-cutter template. For GuideMaker, I use the template from conda (https://github.com/conda/cookiecutter-conda-python). You can follow the setup steps as decribed in the repo. During the setup, you can provide various meta data for the package. 

```
# install cookiecutter
$ conda install cookiecutter

# download cookiecutter-conda-python template 
$ cookiecutter https://github.com/conda/cookiecutter-conda-python.git

# Once cookiecutter clones the template, you will be asked a series of questions related to your project

$ full_name [Full Name]: Enter your full name.

$ email [Email Address]: Enter your email address

$ github_username [github_username]: Enter your github username

$ repo_name [repository_name]: Enter the name of your project's repository

$ package_name [package_name]: Enter the name of your package

$ application_name [application]: Enter the name of your GUI application

$ project_short_description [Short description]: Enter a short description about your project
```


Following is the folder tree for repo_name: `GuideMaker` where package_name: `guidemaker`.

```
(myenv) 🙏 tree -a
.
├── .circleci
│   └── config.yml
├── .codacy.yml
├── .coveragerc
├── .gitattributes
├── .gitignore
├── .travis.yml
├── LICENSE
├── MANIFEST.in
├── README.rst
├── appveyor.yml
├── conda.recipe
│   └── meta.yaml
├── guidemaker
│   ├── __init__.py
│   ├── __main__.py
│   ├── _version.py
│   └── cli.py
├── setup.cfg
├── setup.py
├── tests
│   ├── __init__.py
│   └── test_cli.py
└── versioneer.py

```
Lets look little in detail on file `setup.py`.

```
from setuptools import setup
import versioneer

requirements = [
    # package requirements go here
]

setup(
    name='GuideMaker',
    version=versioneer.get_version(),
    cmdclass=versioneer.get_cmdclass(),
    description="Software to design CRISPR-Cas guide RNA pools in non-model genomes",
    license="MIT",
    author="Ravin Poudel",
    author_email='rp3448@ufl.edu',
    url='https://github.com/ravinpoudel/GuideMaker',
    packages=['guidemaker'],
    entry_points={
        'console_scripts': [
            'guidemaker=guidemaker.cli:cli'
        ]
    },
    install_requires=requirements,
    keywords='GuideMaker',
    classifiers=[
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3.6',
        'Programming Language :: Python :: 3.7',
    ]
)

```


Following is how we modify `setup.py` file for GuideMaker.

```
"""setup.py: python package setup for GuideMaker
"""

from setuptools import setup
import versioneer

requirements = [
    # package requirements go here
    'biopython==1.76',
    'numpy >=1.11',
    'pybedtools>=0.8.2',
    'nmslib>=2.0.6',
    'pandas>=1.0.0',
    'pyyaml>=5.4.1',
    'regex==2020.11.13',
    'altair',
    'streamlit>=0.81.0',
    'pytest>=4.6',
    'pytest-cov',
    'streamlit_tags>=1.2.6',
    'pdoc3'
]



setup(
    name='guidemaker',
    version=versioneer.get_version(),
    cmdclass=versioneer.get_cmdclass(),
    description='GuideMaker: Software to design gRNAs pools in non-model genomes and CRISPR-Cas',
    license='CC0 1.0 Universal (CC0 1.0) Public Domain Dedication',
    author='Adam Rivers',
    author_email='adam.rivers@usda.gov',
    url='http://tinyecology.com',
    long_description=open('README.md').read(),
    packages=['guidemaker'],
    entry_points={
        'console_scripts': [
            'guidemaker=guidemaker.cli:main'
        ]
    },
    install_requires=requirements,
    python_requires='>=3.6',
    test_suite='pytest',
    tests_require=['pytest'],
    keywords='CRISPR-Cas',
    include_package_data=True,
    zip_safe=False,
    classifiers=[
        'Programming Language :: Python :: 3.7',
        'Programming Language :: Python :: 3.8',
        'Development Status :: 3 - Alpha'
    ]
)


```
Lets look more into another file `MANIFEST.in`


```
include versioneer.py

```

Following is how we modify `MANIFEST.in` file for GuideMaker.


```
include versioneer.py
include guidemaker/_version.py
include tests/test_data/*
include LICENSE.txt
include README.md
include guidemaker/data/*
include guidemaker/data/Pseudomonas_aeruginosa.gbk.gz
include guidemaker/data/Carsonella_ruddii.gbk.gz
```



Now you are ready to install your python package!!!

```bash
# cd to the folder where you have setup.py file
(myenv) 🙏 pip install .

(myenv) 🙏 guidemaker -h
```
Congratulations!!!! 

This is how you can get started. Now, you need to add more functions to your packages. The actual code that has been implented for `__main__.py` and `__cli__.py` are available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/guidemaker)

## Running Pytest/Unit test of the functions.

```bash
(myenv) 🙏 pip install pytest-cov
(myenv) 🙏 pytest


---------- coverage: platform darwin, python 3.7.10-final-0 ----------
Name                     Stmts   Miss  Cover   Missing
------------------------------------------------------
guidemaker/__init__.py       3      0   100%
guidemaker/cli.py           10      2    80%   27-28
------------------------------------------------------
TOTAL                       13      2    85%
Coverage XML written to file coverage.xml

======================================================== slowest 20 durations =========================================================

(3 durations < 0.005s hidden.  Use -vv to show these durations.)
==================================================== 1 passed, 2 warnings in 0.11s ====================================================
```
Example of writing pytests for functions in the softare is available [here](https://github.com/USDA-ARS-GBRU/GuideMaker/tree/main/tests).



