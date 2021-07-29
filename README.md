# How to start a python package?
Starting a python package might be overwhelming to newbiees, which was the case for me. At the begining I was following the skeleton/folder structures from some of the published python packages from my mentors or lab- which is a good way to start. Even better will be use the tools such as cookie-cutter to to structure the architecure(skeleton) for a python package. There are many great cookie-cutter template with different features. Depending on your need such as template for conda-recipe, template for Cicle-CI or Travis ...etc, you can search for appropriate cookie-cutter template. For GuideMaker, I use the [template from conda](https://github.com/conda/cookiecutter-conda-python). You can follow the setup steps as decribed in the repo. During the setup, you can provide various meta data for the package. 


## cookiecutter
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

## setup.py

What is setup.py?
The setup script is the centre of all activity in building, distributing, and installing modules using the Distutils. The presence of this file indicats that the module/package you are about to install has likely been packaged and distributed with Distutils, which is the standard for distributing Python Modules. Additionally, this file allows one to define the package requirement as well as the metadata about the package.

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

## setup.cfg
What is setup.cfg? What it does? How to use it?

setup.py is the file with the actual instructions how to build your software. These instructions might have some configuration options, e.g. for unit tests you might be able to indicate whether test coverage should be computed or not, or the install prefix etc.

setup.cfg is a file which might be used to specify such options in addition to reading the command line when calling python setup.py <somecommand>.
    
[Documentation of setup.cfg](https://docs.python.org/2.7/distutils/configfile.html)

Often, it’s not possible to write down everything needed to build a distribution a priori: you may need to get some information from the user, or from the user’s system, in order to proceed. As long as that information is fairly simple—a list of directories to search for C header files or libraries, for example—then providing a configuration file, setup.cfg, for users to edit is a cheap and easy way to solicit it. Configuration files also let you provide default values for any command option, which the installer can then override either on the command-line or by editing the config file.

    
```bash
[flake8]
max-line-length = 100
ignore = E122,E123,E126,E127,E128,E731,E722
exclude = build,guidemaker/_version.py,tests,conda.recipe,.git,versioneer.py,benchmarks,.asv

[tool:pytest]
norecursedirs= .* *.egg* build dist conda.recipe
addopts =
    --cov=guidemaker --cov-report xml
    --ignore setup.py
    --ignore run_test.py
    --cov-report term-missing
    --tb native
    --strict
    --durations=20
env =
    PYTHONHASHSEED=0
markers =
    serial: execute test serially (to avoid race conditions)

[versioneer]
VCS = git
versionfile_source = guidemaker/_version.py
versionfile_build = guidemaker/_version.py
tag_prefix =
parentdir_prefix = GuideMaker-

[bdist_wheel]
universal=1

```



## MANIFEST.in
What is MANIFEST.in? What it does? How to use it?

When building a source distribution for your package, by default only a minimal set of files are included. You may find yourself wanting to include extra files in the source distribution, such as an authors/contributors file, a docs/ directory, or a directory of data files used for testing purposes. There may even be extra files that you need to include; for example, if your setup.py computes your project’s long_description by reading from both a README and a changelog file, you’ll need to include both those files in the sdist so that people that build or install from the sdist get the correct results.

Adding & removing files to & from the source distribution is done by writing a MANIFEST.in file at the project root.

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

## Pytest
What is Pytest? What it does? How to use it?
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


## circle ci

## coverage 

## codecov

## build and test within github

## GitHub actions and workflow

## How to build a docker?

## How to create a package in gitHub?

## CD/CI with workflows and actions file

## Automate python package creation
- manage package access

## Release a version of the software

## Put docker images in github registry

## Automate deployment to AWS Fargate
- resource setup
- how to do this for USDA

## AWS web deployment
 - Route 53
 - Load balancer
 - Auto scaling
 - Linking DNS name from load balancer to domain name via route 53
 - Adding SSL cerficate
 
## Bioconda recipe
- pull request
- Need to make comments prior so that someone will start looking your code for branch merge

## How to develop a web app using streamlit?

